---
title: 인증 참조
description: Adobe LLM 앱의 최종 사용자 인증을 위한 필드 정의, 토큰 요구 사항, 검색 끝점, 핸들러 API 및 LLM 플랫폼 동작.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '1260'
ht-degree: 2%
---

# 인증 참조 {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

이 페이지를 사용하여 인증 필드 및 계약을 조회합니다. 설치 여정에 대해서는 [자체 ID 공급자를 통해 최종 사용자 인증](/help/guides/authentication.md)을 참조하십시오.

## 인증 설정 {#authentication-settings}

**[!UICONTROL 설정]** > **[!UICONTROL 인증]**&#x200B;에서 찾을 수 있습니다. 모든 필드는 환경별로 저장됩니다. **[!UICONTROL Workspace]** 선택기는 편집 중인 필드를 선택하고, 저장은 다른 필드에는 영향을 주지 않습니다.

| 필드 | 필수 | 설명 |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | 이러한 설정이 적용되는 환경: **[!UICONTROL 단계]** 또는 **[!UICONTROL 프로덕션]** |
| **[!UICONTROL 인증 사용]** | — | 기본 스위치입니다. 해제 시 모든 작업은 인증 모드에 관계없이 공개됩니다. |
| **[!UICONTROL 발급자]** | 예 | ID 공급자의 발급자 URL 및 예상 `iss` 클레임입니다. HTTPS여야 합니다. 또한 이 앱의 인증 서버로 게시됩니다. 앱당 ID 공급자 1명 |
| **[!UICONTROL 지원되는 범위]** | 아니요 | 이 앱 작업에 필요할 수 있는 전체 범위 세트입니다. 앱에서 지원되는 범위로 LLM 플랫폼에 게시됩니다. |
| **[!UICONTROL JWKS URI]** | 아니요 | 고급. 서명 키 집합의 HTTPS URL입니다. 인증 서버의 메타데이터가 알리는 내용과 다른 경우에만 필요합니다. |

### 유효성 검사 규칙

| 규칙 | 효과 |
|------|--------|
| **[!UICONTROL 인증 사용]**&#x200B;을 사용하는 동안 **[!UICONTROL 발급자]**&#x200B;이(가) 비어 있습니다. | 저장이 차단됨 |
| **[!UICONTROL 발급자]** 또는 **[!UICONTROL JWKS URI]**&#x200B;이(가) HTTPS URL이 아닙니다. | 저장이 차단됨 |
| 액션에는 **[!UICONTROL 지원되는 범위]**&#x200B;에서 누락된 범위가 필요합니다. | 범위를 추가하거나 작업에서 제거할 때까지 저장이 차단됩니다. |
| **[!UICONTROL 지원되는 범위]**&#x200B;에서 범위가 제거되었습니다. | 저장을 기다리지 않고 필요한 모든 작업에서 즉시 제거됩니다 |
| **[!UICONTROL 지원되는 범위]**&#x200B;이(가) 비어 있습니다 | 범위를 부여할 수 없으므로 작업에 이미 있는 범위는 제거됩니다. 이 경우에는 경고가 표시되지 않습니다 |
| `offline_access`이(가) **[!UICONTROL 지원되는 범위]** 또는 작업에 나열됩니다. | 설정 페이지에 배포된 앱에 없는 범위가 표시될 수 있도록 대소문자나 주변 공백에 관계없이 앱이 배포될 때 제거됩니다. `offline_access`이(가) 이 앱에 대한 액세스 권한을 부여하지 않고 인증 서버에서 새로 고침 토큰을 요청하므로 이 앱이 광고하는 범위가 아닙니다. 나열할 필요가 없습니다. LLM 플랫폼은 인증 서버에서 직접 요청합니다 |

**[!UICONTROL Issuer]**&#x200B;의 후행 슬래시가 표준화되고 `iss` 비교에서 차이를 허용합니다. 후행 슬래시를 항상 내보내는 공급자가 유효성을 검사합니다.

## 인증 모드 {#auth-modes}

**[!UICONTROL 작업별 구성]**&#x200B;에서 작업별로 설정합니다.

| 모드 | 토큰 필요 | 핸들러가 ID를 수신함 | 를 플랫폼에 광고합니다. |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL 없음]** | 아니요 | 호출자가 유효한 토큰을 제공할 때만 | `noauth` |
| **[!UICONTROL 필수]** | 예. 나열된 모든 범위가 있음 | 항상 | `oauth2` |
| **[!UICONTROL 선택 사항]** | 아니요 | 유효한 토큰이 있는 경우 | `noauth` 및 `oauth2` |

**[!UICONTROL Required]** 작업의 처리기는 올바른 범위의 올바른 토큰이 없으면 실행되지 않습니다. **[!UICONTROL Optional]** 작업의 처리기는 항상 실행되며 `extra.challengeAuth()`(으)로 로그인을 요청할 수 있습니다.

따라서 인증되지 않은 호출자를 거부하는 모드는 **[!UICONTROL Required]**&#x200B;뿐입니다. 앱의 모든 작업이 **[!UICONTROL 필수]**&#x200B;인 경우에만 앱이 완전히 제어됩니다. 하나 이상의 작업에 대해 익명 호출이 계속 성공하기 때문에 단일 **[!UICONTROL 없음]** 또는 **[!UICONTROL 선택 사항]** 작업으로 앱이 혼합됩니다.

인증 모드는 **[!UICONTROL 인증 사용]**&#x200B;이 설정된 경우에만 적용됩니다. 변경 사항은 앱의 다음 배포에 적용됩니다.

스위치를 전환하면 작업별 모드가 재작성됩니다.

| 전환 변경 | 작업별 모드에 대한 효과 |
|---------------|----------------------------|
| 켜기/끄기 | **[!UICONTROL 없음]** 작업마다 **[!UICONTROL 필수]**&#x200B;이 됩니다. 이미 **[!UICONTROL 필수]** 또는 **[!UICONTROL 선택적]** 작업이 해당 모드를 유지합니다. |
| 켜기/끄기 | 모든 작업의 모드와 범위가 해당 환경에 대해 지워집니다. 스위치를 다시 켜면 구성이 복원되지 않습니다 |

**[!UICONTROL Workspace]**&#x200B;을(를) 전환하면 모드가 다시 작성되지 않습니다. 다른 환경의 저장된 구성이 그대로 로드됩니다.

**[!UICONTROL 모든 작업이**&#x200B;[!UICONTROL &#x200B;없음&#x200B;]&#x200B;**(으)로 설정된 상태에서 인증 사용]**&#x200B;은(는) 유효하지만 비활성 조합입니다. 호출이 거부된 적이 없지만 앱을 통해 검색을 위해 인증 서버가 게시됩니다. 앱을 완전히 공개하려면 스위치를 끄십시오.

하나의 앱에서 모드를 자유롭게 혼합할 수 있습니다. 각 플랫폼이 적용되는 방법은 [LLM 플랫폼 동작](/help/reference/authentication-reference.md#platform-behavior)을 참조하세요.

## 토큰 요구 사항 {#token-requirements}

ID 공급자는 다음 사항을 모두 충족하는 액세스 토큰을 발급해야 합니다. 검사에 실패한 토큰은 없는 것으로 처리됩니다. 호출자는 인증되지 않았으며 **[!UICONTROL 필수]** 작업은 로그인하도록 요구합니다.

| 요구 사항 | 세부 사항 |
|-------------|--------|
| 포맷 | 서명된 JWT. 불투명 토큰은 지원되지 않습니다. |
| 서명 알고리즘 | `RS256`, `RS384`, `RS512`, `ES256`, `ES384`, `ES512`, `PS256`, `PS384` 또는 `PS512`. `HS256` 같은 HMAC 알고리즘이 거부되었습니다. |
| `iss` | **[!UICONTROL 발급자]**&#x200B;와(과) 일치해야 함 |
| `aud` | 앱의 리소스 식별자(해당 환경의 MCP 서버 URL)가 포함되어야 합니다. |
| `exp` | 은(는) 미래여야 합니다 |
| `scope` 또는 `scp` | 공백으로 구분된 문자열 또는 문자열 배열. 각 작업의 요구 사항에 대해 확인된 범위를 제공합니다. |
| `sub` | 처리기가 `getAuthenticatedUser`을(를) 통해 읽는 사용자 식별자 |
| 전송 | `Authorization: Bearer <token>` 요청 헤더 |

공급자에 포함된 추가 플랫 클레임(예: `tenant` 또는 `email`)이 처리기에 전달됩니다. 중첩된 오브젝트가 삭제되고 긴 문자열 값이 잘립니다.

## ID 공급자 검색 {#discovery}

LLM 플랫폼에서 인증 서버를 찾을 수 있도록 앱에서 자체 [RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728) 보호된 리소스 메타데이터를 게시합니다. 이를 위해 어떤 것도 만들거나, 호스팅하거나, 구성하지 않습니다.

직접 검색을 제공해야 합니다.

| 요구 사항 | 세부 사항 |
|-------------|--------|
| 인증 서버 메타데이터 | 발급자는 `/.well-known/` 경로에서 자체 [RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414) 메타데이터 또는 [!DNL OpenID Connect] 검색을 제공해야 합니다. 앱에서 서명 키를 찾기 위해 읽습니다. |
| 경로가 있는 발급자 | 잘 알려진 세그먼트는 경로 다음에 가는 것이 아니라 경로 앞에 갑니다. `https://auth.example.com/oauth2/default`의 발급자가 `https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default`에서 해당 메타데이터를 제공합니다. |
| 다른 곳에서 호스팅된 키 | 서명 키가 해당 메타데이터에 광고되지 않는 경우 **[!UICONTROL JWKS URI]**&#x200B;을(를) 설정합니다. |

## 핸들러 인증 API {#handler-auth-api}

`@adobe/llm-apps-runtime`에서 내보냈습니다. 각 도우미는 핸들러에 수신되는 두 번째 인수인 `extra`을(를) 사용합니다.

| 도우미 | 반환 |
|--------|---------|
| `getAuthenticatedUser(extra)` | 로그인한 사용자의 `sub` 클레임 또는 호출이 인증되지 않은 경우 `undefined` |
| `hasScope(extra, scope)` | 호출자의 토큰에 `scope`이(가) 포함된 경우 `true` |

원시 확인 토큰 정보가 인증되지 않은 호출의 `undefined`인 `extra.authInfo`에 있습니다.

| 속성 | 설명 |
|----------|-------------|
| `authInfo.token` | 원시 전달자 토큰. 기록하지 않거나 클라이언트에 반환하지 않음 |
| `authInfo.clientId` | `client_id` 또는 `azp` 클레임 또는 `unknown` |
| `authInfo.scopes` | 부여된 범위 배열 |
| `authInfo.expiresAt` | `exp` 클레임과 같은 토큰 만료 |
| `authInfo.resource` | 토큰의 유효성을 검사한 앱의 리소스 식별자 |
| `authInfo.extra` | `sub`에 ID 공급자가 포함한 기타 일반 클레임이 포함되어 있습니다. |

`extra.challengeAuth(options)`은(는) **[!UICONTROL 선택적]** 작업에서만 사용할 수 있습니다. 핸들러의 결과를 반환하여 사용자에게 컨텐츠를 반환하는 대신 로그인하도록 요청합니다.

| 옵션 | 설명 |
|--------|-------------|
| `error` | [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750) 전달자 오류 코드: `invalid_token`, `insufficient_scope` 또는 `invalid_request`. 기본값은 `insufficient_scope`입니다. |
| `errorDescription` | 사용자에게 표시되는 메시지. 기본값은 일반 로그인 프롬프트입니다. |
| `scope` | 요청할 공백으로 구분된 범위입니다. 생략하여 플랫폼이 앱의 지원되는 범위로 대체되도록 합니다. |

>[!IMPORTANT]
>
>항상 `error`을(를) 명시적으로 설정하십시오. 올바른 세션이 없는 호출자의 경우 `invalid_token`을(를) 사용하고, 토큰은 유효하지만 필수 범위가 없는 호출자의 경우에만 `insufficient_scope`을(를) 사용하십시오. 이 값은 LLM 플랫폼으로 전달되며, 이 플랫폼은 사용자에게 표시되는 프롬프트를 발화하는 방법을 자체적으로 결정합니다. 프롬프트에서 원하는 코드가 아니라 조건을 정확하게 설명하는 코드를 전송하십시오.

## LLM 플랫폼 동작 {#platform-behavior}

개별 작업 인증에 대한 지원은 플랫폼마다 다릅니다. 두 가지 모두에 대해 동일한 방식으로 구성하십시오. 차이점은 사용자가 경험하는 것입니다.

| 비헤이비어 | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| 세부기간 | 작업당 | 커넥터별 |
| 앱이 완전히 게이팅되지 않은 혼합 인증 | 지원됨 **[!UICONTROL 필수]** 작업만 로그인을 요청합니다. | 지원되지 않습니다. 잠기지 않은 작업을 포함하여 전체 커넥터가 로그인을 묻는 메시지를 표시합니다 |
| 커넥터 설정 | 모든 작업이 **[!UICONTROL 없음]**&#x200B;인 경우 **[!UICONTROL 인증]**&#x200B;을 **[!UICONTROL 인증 없음]**, 모든 작업이 **[!UICONTROL 필수]**&#x200B;인 경우 **[!UICONTROL OAuth]**, 그렇지 않은 경우 **[!UICONTROL 혼합]**(으)로 설정하십시오. | 인증 선택이 없습니다. **[!UICONTROL 연결]**&#x200B;에서 로그인이 시작됩니다. |
| 재인증 | 제어된 작업이 호출될 때 대화에서 메시지 표시 | 커넥터를 입력하라는 메시지가 표시됨 |


## 관련됨

- [자체 ID 공급자로 최종 사용자 인증](/help/guides/authentication.md)
- [작업 및 위젯 필드](/help/reference/reference-docs.md)
- [문제 해결](/help/reference/troubleshooting.md#authentication)
