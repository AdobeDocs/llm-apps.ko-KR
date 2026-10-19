---
title: 자체 ID 공급자로 최종 사용자 인증
description: Adobe LLM 앱에 대한 최종 사용자 인증을 켜면 지원되는 LLM 플랫폼이 보호된 작업을 호출하기 전에 ID 공급자에 로그인합니다.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '2136'
ht-degree: 0%
---

# 자체 ID 공급자로 최종 사용자 인증 {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

기본적으로 앱의 모든 작업은 공개입니다. MCP 서버 URL이 있는 모든 LLM 플랫폼은 이러한 작업을 호출할 수 있으며 핸들러가 최종 사용자를 식별할 수 없습니다.

작업에서 주문, 권한 또는 계정 세부 사항을 반환하는 등 어떤 최종 사용자가 요청하는지 알아야 할 때 인증을 켭니다. LLM 플랫폼은 **ID 공급자(IdP)를 사용하여 사용자에게 로그인하고, 모든 호출과 함께 결과 액세스 토큰을 전송하며, 핸들러가 확인된 ID를 수신합니다.**

**이 안내서:** ID 공급자를 연결하고 로그인이 필요한 작업을 선택하는 방법에 대해 알아봅니다. 핸들러에서 확인된 ID를 사용하고, 앱을 배포하고, 로그인 및 보호된 작업을 테스트합니다.

인증 설정은 고급 작업입니다. 먼저 [인프라 설정](/help/guides/create-app.md), [작업 만들기](/help/guides/creating-actions.md) 및 [앱을 배포하세요](/help/guides/deploy-your-app.md).

## 작동 방식

자체 ID 공급자를 가져옵니다. 배포된 앱은 OAuth 2.1 **리소스 서버**&#x200B;일 뿐입니다. 인증 서버가 발급된 토큰을 확인합니다. 토큰을 발급하지 않으며, [!DNL Adobe]에서 클라이언트 ID 또는 클라이언트 암호를 저장하지 않습니다.

```
┌── Your identity provider ───────────────────────────────────────────────┐
│  Authorization server — you own it                                      │
│  Issues access tokens, holds the user directory, defines the scopes     │
└─────────────────────────────────────────────────────────────────────────┘
        ▲  2  user signs in, platform gets an access token
        │                                    │
        │  1  platform discovers your        │  3  every tools/call carries
        │     authorization server from      │     Authorization: Bearer <token>
        │     your app's metadata            ▼
┌── LLM platform (ChatGPT, Claude, …) ────────────────────────────────────┐
└─────────────────────────────────────────────────────────────────────────┘
                                             │
                                             ▼
┌── Your LLM App on Adobe I/O Runtime ────────────────────────────────────┐
│  Resource server — verifies the token's signature, issuer, audience,    │
│  and expiry, then enforces the auth mode you set for each action        │
│                                                                         │
│  Your handler reads the verified identity from its second argument      │
└─────────────────────────────────────────────────────────────────────────┘
```

**환경별** 인증을 구성하십시오. **[!UICONTROL 단계]** 및 **[!UICONTROL 프로덕션]**&#x200B;은(는) 독립적인 설정을 유지합니다. **[!UICONTROL 프로덕션]**&#x200B;에서 인증을 활성화하기 전에 개발 IdP 테넌트에 대해 구성을 확인하십시오.

## 시작하기에 앞서

- 비대칭 알고리즘으로 서명된 **JWT** 액세스 토큰을 발급하는 OAuth 2.1 또는 OpenID Connect ID 공급자입니다. 불투명 토큰 및 HMAC 서명 토큰은 지원되지 않습니다. [토큰 요구 사항](/help/reference/authentication-reference.md#token-requirements)을 참조하세요.
- API 및 클라이언트를 등록하기 위해 해당 ID 공급자에 대한 관리자 액세스 권한.
- 앱이 구성 중인 환경에 한 번 이상 배포되었습니다. 배포된 MCP 서버 URL은 토큰의 범위가 지정되어야 하는 값입니다.

## 리소스 식별자 복사

앱의 **리소스 식별자**&#x200B;은(는) MCP 서버 URL입니다. ID 공급자가 앱에 대해 발급하는 모든 액세스 토큰은 해당 정확한 URL의 이름을 대상자로 지정해야 합니다. 이 대상 바인딩은 다른 서비스에 대해 발급된 토큰을 사용할 수 없도록 합니다.

1. 앱 세부 사항 페이지를 엽니다.
2. **[!UICONTROL 앱 테스트]**(으)로 스크롤합니다.
3. 구성하고 있는 환경에서 **[!UICONTROL URL 복사]**&#x200B;를 선택합니다.

![앱 세부 정보 — 스테이징 MCP 서버 URL 복사](/help/assets/guide-onboarding-agent/app-mcp-url.png)

다음 단계에서는 ID 공급자 구성을 위해 복사된 MCP 서버 URL을 유지합니다. URL을 다시 입력하는 대신 붙여 넣습니다. 대상 검사는 경로를 포함하여 정확한 문자열 일치를 요구합니다. 단일 문자 차이로 인해 모든 토큰이 유효성 검사에 실패합니다.

>[!NOTE]
>
>각 환경에는 고유한 MCP 서버 URL이 있으므로 자체 대상이 있습니다. **[!UICONTROL Stage]** 및 **[!UICONTROL Production]**&#x200B;을(를) 별도로 구성하십시오.

## ID 공급자 구성

단계는 공급자에 따라 다릅니다. 다음 네 가지 항목을 구성합니다.

1. **앱을 API(리소스)로 등록합니다.** 해당 식별자(공급자가 토큰의 `aud` 클레임에 넣는 값)를 복사된 MCP 서버 URL로 설정합니다. 공급자는 일반적으로 이 필드 *식별자* 또는 *대상*&#x200B;에 레이블을 지정합니다. 다른 서비스에 대해 발급된 토큰을 사용하지 않도록 하려면 `api`과(와) 같은 일반 값이 아닌 앱에 고유한 식별자를 사용하십시오.
2. **작업을 보호하는 범위를 정의**&#x200B;합니다(예: `orders:read` 또는 `profile:read`). 각 작업이 필요한 액세스만 요청하도록 의미 있는 권한당 하나의 범위를 사용합니다.
3. **지원 PKCE.** LLM 플랫폼은 모든 인증 요청에서 `code_challenge_method=S256`과(와) 함께 `code_challenge`을(를) 전송하므로 인증 서버는 S256 PKCE를 지원하고 메타데이터에 `"code_challenge_methods_supported": ["S256"]`을(를) 광고해야 합니다.
4. **LLM 플랫폼이 OAuth 클라이언트를 식별하는 방법을 선택하십시오.** 공급자가 플랫폼의 요구 사항, 동적 클라이언트 등록(DCR) 또는 사전 등록된 OAuth 클라이언트를 지원하는 경우 CIMD(클라이언트 ID 메타데이터 문서)를 사용합니다. 선택한 방법에 필요한 경우에만 클라이언트 ID와 암호를 제공하십시오. 플랫폼이 문서화하거나 표시하는 정확한 리디렉션 URI를 등록합니다. [!DNL Claude]의 호스팅된 표면(`https://claude.ai/api/mcp/auth_callback`)에 대해 등록하십시오. [!DNL ChatGPT]은(는) 공급자의 광고 기능에 따라 안정적이거나 커넥터별 콜백을 사용할 수 있으므로 추측하는 대신 표시된 URI를 복사하십시오. 현재 [ChatGPT 인증 설명서](https://developers.openai.com/plugins/build/auth) 및 [Claude 인증 설명서](https://claude.com/docs/connectors/building/authentication)를 참조하세요.

>[!IMPORTANT]
>
>ID 공급자의 발급자, JWKS, 인증 및 토큰 종단점은 모두 공개 HTTPS를 통해 접근 가능해야 합니다. LLM 플랫폼과 배포된 앱 모두 공급자로부터 직접 메타데이터를 가져옵니다. 이러한 요청을 차단하는 IP VPN, 방화벽 또는 웹 애플리케이션 방화벽은 앱 자체가 연결 가능한 상태를 유지하는 경우에도 로그인을 방지합니다.

## 인증 켜기

1. 왼쪽 탐색에서 **[!UICONTROL 설정]**&#x200B;을 선택한 다음 **[!UICONTROL 인증]** 탭을 엽니다.
2. **[!UICONTROL Workspace]**&#x200B;에서 **[!UICONTROL 단계]** 또는 **[!UICONTROL 프로덕션]**&#x200B;을 선택하세요.
3. **[!UICONTROL 인증 사용]**&#x200B;을 켭니다.
4. **[!UICONTROL 핵심 설정]**&#x200B;에서 다음을 입력하십시오.
   - **[!UICONTROL 발급자]** — ID 공급자의 HTTPS 발급자 URL입니다. 이 URL은 각 토큰의 `iss` 클레임에 넣는 값입니다. 플랫폼에서는 이 URL이 필요하며 발급자를 LLM 클라이언트 검색을 위한 앱의 인증 서버로 게시합니다. 각 앱은 하나의 ID 공급자를 지원합니다.
   - **[!UICONTROL 지원되는 범위]** — 이 앱의 작업에 필요한 모든 범위입니다. ID 공급자에서 정의한 범위를 미러링합니다.
5. **[!UICONTROL 고급 설정]**&#x200B;은(는) 선택 사항입니다. 서명 키가 인증 서버의 메타데이터에 광고되는 위치에 있지 않은 경우에만 **[!UICONTROL JWKS URI]**&#x200B;을(를) 설정합니다. 그렇지 않으면 앱에서 자동으로 검색합니다.
6. **[!UICONTROL 저장]**&#x200B;을 선택합니다.

![인증 — 인증을 활성화하고 핵심 설정을 완료합니다](/help/assets/guide-authentication/auth-core-settings.png)

필드 요구 사항에 대해서는 [인증 설정](/help/reference/authentication-reference.md#authentication-settings)을 참조하십시오.

## 각 작업에 대한 인증 모드 선택

**[!UICONTROL 인증 사용]**&#x200B;을 켜면 현재 **[!UICONTROL 없음]**(으)로 설정된 모든 작업이 **[!UICONTROL 필수]**(으)로 변경됩니다. **[!UICONTROL 작업별 구성]**&#x200B;에서 해당 할당을 검토하고 각 작업에 필요한 모드를 설정하십시오.

| 모드 | 비헤이비어 |
|------|----------|
| **[!UICONTROL 없음]** | 공개. 발신자는 토큰이 필요하지 않습니다. |
| **[!UICONTROL 필수]** | 보호됨. 발신자는 나열된 모든 범위를 전달하는 유효한 토큰이 필요합니다. 런타임에서는 인증되지 않은 호출자가 로그인하도록 합니다. |
| **[!UICONTROL 선택 사항]** | 익명으로 호출할 수 있지만 해당 작업에서는 로그인을 지원한다고 광고하기도 합니다. 처리기가 호출별로 일반 결과를 제공할지 또는 사용자에게 개인화된 결과에 로그인하도록 요청할지 여부를 결정합니다. |

![인증 — 각 작업에 대한 인증 모드 및 범위를 설정합니다](/help/assets/guide-authentication/auth-per-action.png)

작업이 이미 **[!UICONTROL 필수]** 또는 **[!UICONTROL 선택적]**(으)로 설정되어 있으면 기존 모드를 유지합니다.

**[!UICONTROL 필수]** 또는 **[!UICONTROL 선택적]** 작업의 경우 필요한 **[!UICONTROL 범위]**&#x200B;를 추가하십시오. 모든 범위는 **[!UICONTROL 지원되는 범위]**&#x200B;에 이미 표시되어야 앱에서 LLM 플랫폼에 필요한 각 권한을 광고합니다. 플랫폼은 범위 불일치를 해결할 때까지 저장을 차단합니다.

**[!UICONTROL 지원되는 범위]**&#x200B;은(는) 사용 가능한 범위를 제어합니다. 해당 목록에서 범위를 제거하면 필요한 모든 작업에서 범위가 즉시 제거됩니다. 작업에 범위를 할당하기 전에 각 범위를 목록에 추가합니다.

**[!UICONTROL 모든 작업에 대해 인증 필요]**&#x200B;를 선택하면 **[!UICONTROL 없음]** 작업이 **[!UICONTROL 필수]**(으)로 변경되지만 이미 **[!UICONTROL 선택 사항]**(으)로 설정된 작업은 그대로 유지됩니다. 모든 작업에 로그인하려면 나머지 **[!UICONTROL 선택적]** 행을 개별적으로 **[!UICONTROL 필수]**(으)로 변경하십시오. 확인란을 지우면 모든 작업이 **[!UICONTROL 없음]**(으)로 반환됩니다.

완료되면 **[!UICONTROL 저장]**&#x200B;을 선택하세요. 인증 모드 및 범위 변경 사항은 앱 수준 설정과 함께 저장됩니다.

>[!IMPORTANT]
>
>**[!UICONTROL 인증 사용]**&#x200B;을 끄면 모든 작업의 인증 모드와 선택한 환경에 대한 범위가 지워집니다. 플랫폼은 이러한 설정을 유지하지 않습니다. 인증을 다시 켜면 모든 작업이 **[!UICONTROL 필수]**(으)로 설정됩니다.

>[!NOTE]
>
>모든 작업을 **[!UICONTROL 없음]**(으)로 설정해도 인증이 비활성화되지 않습니다. 해당 상태에서는 로그인할 필요가 없지만 앱에서는 여전히 인증 서버를 LLM 플랫폼에 광고합니다. 클라이언트는 추가 액세스 권한을 부여하지 않는 로그인을 제공할 수 있습니다. 앱을 완전히 공개하려면 **[!UICONTROL 인증 사용]**&#x200B;을 해제하고 배포하십시오.

[!DNL ChatGPT]은(는) 하나의 앱 내에서 혼합 인증 모드를 지원하고 각 작업의 모드를 개별적으로 적용합니다. 공개 작업은 로그인을 묻는 메시지를 표시하지 않습니다. 보호된 작업은 필요에 따라 메시지를 표시합니다.

[!DNL Claude]은(는) **지연 인증**&#x200B;을 통해 혼합 앱도 지원합니다. 커넥터 설정 중에 **[!UICONTROL 필요한 경우 로그인]**&#x200B;을 선택하면 공개 작업이 익명으로 사용 가능하며 **[!UICONTROL 필수]** 작업이 호출되면 플랫폼에서 로그인을 요청합니다. 작업이 모두 **[!UICONTROL 필수]**&#x200B;인 앱은 사용하기 전에 로그인해야 합니다. 필요한 작업과 로그인을 요청하는 선택적 처리기를 구분하려면 [LLM 플랫폼 동작](/help/reference/authentication-reference.md#platform-behavior)을 참조하세요.

>[!NOTE]
>
>현재 런타임에서는 버전 1.1.5에 대해 검증된 이러한 혼합 인증 동작을 지원합니다. 배포된 이전 앱은 런타임 업데이트 및 재배포가 필요할 수 있습니다. 일부 UI 빌드에는 여전히 Cloud가 전체 커넥터에 대한 인증을 요구한다는 이전 경고가 표시됩니다. 배포된 런타임을 확인하고 경고에 의존하지 않고 Claude의 현재 **[!UICONTROL 필요할 때 로그인]** 옵션을 사용하십시오.

## 변경 사항 배포

인증 변경 사항은 이 앱의 다음 배포에 적용됩니다. 구성한 환경에 **앱을 다시 배포**&#x200B;합니다. [앱 배포](/help/guides/deploy-your-app.md)를 참조하세요.

MCP 서버 URL은 변경되지 않습니다. 연결의 메타데이터를 새로 고치고 해당 인증 설정이 앱의 새 구성과 일치하는지 확인합니다. 인증 방법이 변경될 때 플랫폼을 통해 연결을 다시 연결하거나 다시 만들어야 할 수 있습니다.

## 핸들러에서 ID 읽기

확인된 ID는 작업의 인증 모드에 관계없이 호출자가 유효한 토큰을 보낼 때마다 핸들러에 두 번째 인수로 도달합니다. **[!UICONTROL Optional]** 액션은 해당 ID를 사용하여 사용 가능한 경우 결과를 개인화하고 그렇지 않은 경우 익명 결과를 반환할 수 있습니다.

`getAuthenticatedUser`을(를) 사용하여 로그인한 사용자를 읽으십시오.

```javascript
const { getAuthenticatedUser } = require('@adobe/llm-apps-runtime');

module.exports = async ({ orderId }, extra) => {
  const userId = getAuthenticatedUser(extra);

  if (!userId) {
    return { content: [{ type: 'text', text: 'Sign in to see your orders.' }] };
  }

  const order = await fetchOrderForUser(userId, orderId);

  return {
    content: [{ type: 'text', text: `Order ${order.id} is ${order.status}.` }],
    structuredContent: order
  };
};
```

런타임에서 토큰을 확인합니다. **[!UICONTROL Required]** 작업의 경우 나열된 범위가 있는 유효한 토큰이 없는 모든 호출을 차단합니다. 처리기는 승인된 호출자에 대해서만 실행됩니다. `hasScope`을(를) 사용하여 권한에 따라 핸들러 동작을 선택합니다(예: **[!UICONTROL Optional]** 작업).

대화 중 로그인을 요청하려면 처리기에서 `extra.challengeAuth()`을(를) 반환하세요. 도우미는 **[!UICONTROL 선택적]** 작업에서만 사용할 수 있습니다.

```javascript
module.exports = async ({ signIn }, extra) => {
  if (signIn && !extra.authInfo) {
    return extra.challengeAuth({
      error: 'invalid_token',
      errorDescription: 'Sign in to see member pricing.'
    });
  }

  return {
    content: [{
      type: 'text',
      text: extra.authInfo ? await memberDeals() : await publicDeals()
    }]
  };
};
```

이 예제에서는 `signIn`과(와) 같은 명시적 입력 매개 변수를 사용하여 인증을 요청할 시기를 결정합니다. 사용자의 표현에서 해당 결정을 유추하지 마십시오.

보고된 조건과 일치하도록 `error`을(를) 설정하십시오. 위의 예제와 같이 호출자에게 올바른 세션이 없어 로그인해야 하는 경우 `invalid_token`을(를) 사용합니다. 호출자가 로그인되어 있지만 토큰에 필수 범위가 없는 경우 `insufficient_scope`을(를) 사용합니다. 각 LLM 플랫폼은 프롬프트 문구와 오류 코드에 응답하는 방법을 선택합니다. 조건을 정확하게 설명하는 코드를 보냅니다.

이 예제에서 `!extra.authInfo` 검사가 수행하는 것처럼 필수 ID가 누락된 경우에만 인증을 요청합니다. 로그인해도 무조건적인 문제가 해결되지 않습니다. 처리기가 모든 호출에 대해 인증을 다시 요청합니다.

>[!NOTE]
>
>`extra.challengeAuth()`이(가) [!DNL ChatGPT]에 대한 도구 결과 챌린지를 반환합니다. 대신 처리기를 실행하기 전에 [!DNL Claude]에 HTTP 인증 문제가 필요합니다. 지원되는 플랫폼에서 로그인해야 하는 작업의 경우 **[!UICONTROL 필수]** 작업을 사용하십시오. 필요한 경우 공용 환경에 대해 별도의 **[!UICONTROL 없음]** 동작을 유지하세요.

ID 서버측을 유지합니다. 위젯에 필요한 데이터만 `structuredContent`에 전달합니다. `structuredContent`에 액세스 토큰을 포함하지 마십시오. [생성된 처리기 사용자 지정](/help/guides/customize-handler.md)을 참조하십시오.

전체 계약에 대해서는 [처리기 인증 API](/help/reference/authentication-reference.md#handler-auth-api)를 참조하십시오.

## 보호된 앱 테스트

기존 플러그인 또는 커넥터는 배포 후 업데이트된 구성을 사용합니다. 새 연결을 만들려면 플랫폼에 대한 지침을 따르십시오.

### [!DNL ChatGPT]

**[!UICONTROL 새 플러그 인]** 대화 상자에서 앱의 작업을 구성한 방식과 일치하도록 **[!UICONTROL 인증]**&#x200B;을 설정하십시오.

| 앱의 작업 | 선택 |
|--------------------|--------|
| 모두 **[!UICONTROL 없음]**(으)로 설정 | **[!UICONTROL 인증 없음]** |
| 모두 **[!UICONTROL 필수]**(으)로 설정됨 | **[!UICONTROL OAuth]** |
| 기타 조합 | **[!UICONTROL 혼합]** |

**[!UICONTROL Optional]** 액션은 항상 익명 호출을 허용하므로 이 액션을 포함하는 앱은 완전히 제어되지 않습니다. 모든 액션이 **[!UICONTROL Optional]**(으)로 설정되어 있더라도 **[!UICONTROL Mixed]**&#x200B;을(를) 선택하십시오. 인증되지 않은 발신자는 **[!UICONTROL Required]**&#x200B;만 거부합니다.

나머지 대화 상자에 대해서는 [ChatGPT 플러그인 테스트](/help/guides/test-in-chatgpt.md)를 참조하십시오.

### [!DNL Claude]

사용자 지정 커넥터를 추가하고 해당 인증 설정을 검토합니다. 공용/보호된 혼합 앱의 경우 **[!UICONTROL 필요한 경우 로그인]**&#x200B;을 선택합니다(가능한 경우). 커넥터를 사용하기 전에 로그인을 요청하려면 **[!UICONTROL 지금 로그인]**&#x200B;을 선택하세요. 대화 상자는 계정 또는 조직에 따라 다릅니다. [클라우드 커넥터 테스트](/help/guides/test-in-claude.md)를 참조하세요.

### 확인

- 플랫폼은 사용자를 자체 ID 공급자의 로그인 페이지로 리디렉션합니다.
- 보호된 작업은 로그인 후 사용자별 데이터를 반환합니다.
- 로그아웃하면 보호된 작업에 로그인하라는 메시지가 표시됩니다.
- 혼합 앱에서는 **[!UICONTROL 없음]**(으)로 설정된 작업이 로그인하지 않고 계속 응답합니다. [!DNL Claude]에서 **[!UICONTROL 필요한 경우 로그인]**&#x200B;을 사용하여 공개 작업을 확인합니다.
- 로그인 후 보호된 작업을 다시 시도하고 런타임에서 예상 권한을 적용하는지 확인하십시오.

로그인이 시작되지 않거나 토큰이 거부되면 [문제 해결](/help/reference/troubleshooting.md#authentication)을 참조하세요.

## 보안 지침

- 각 작업에 필요한 가장 좁은 범위를 부여합니다. 모든 작업에서 하나의 넓은 범위를 재사용하지 마십시오.
- ID 공급자와 LLM 플랫폼의 커넥터 구성에서 클라이언트 암호를 유지하십시오. 작업 메타데이터, 핸들러 코드, 위젯 JavaScript 또는 소스 제어에 배치하지 마십시오.
- 토큰 클레임을 외부 시스템의 입력으로 처리합니다. 쿼리에 해당 값을 사용하기 전에 `authInfo.extra`의 값을 확인합니다.
- 인증뿐 아니라 인증도 수행합니다. 유효한 토큰은 특정 레코드를 읽을 수 있는 권한이 아니라 사용자의 ID를 증명합니다. 데이터를 반환하기 전에 핸들러에서 소유권을 확인하십시오.
- 토큰, 전체 클레임 세트 또는 사용자 식별자를 기록하지 마십시오.
- 안전 오류를 반환합니다. 사용자에게 업스트림 ID 공급자 응답 또는 스택 추적을 표시하지 마십시오.
- **[!UICONTROL 프로덕션]**&#x200B;에서 인증을 활성화하기 전에 비프로덕션 ID 공급자 테넌트에 대해 **[!UICONTROL Stage]**&#x200B;을(를) 설정하고 확인하십시오.

## 다음 단계

- [인증 참조](/help/reference/authentication-reference.md) — 필드, 토큰 요구 사항 및 플랫폼 동작.
- [생성된 처리기를 사용자 지정](/help/guides/customize-handler.md) — 처리기에서 보호된 업스트림 API를 호출합니다.
- [앱 배포](/help/guides/deploy-your-app.md).
