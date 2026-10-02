---
title: 자체 ID 공급자로 최종 사용자 인증
description: Adobe LLM 앱에 대한 최종 사용자 인증을 켜면 지원되는 LLM 플랫폼이 보호된 작업을 호출하기 전에 ID 공급자에 로그인합니다.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '2149'
ht-degree: 0%
---

# 자체 Id 공급자를 통해 최종 사용자 인증 {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

기본적으로 앱의 모든 작업은 공개입니다. MCP 서버 URL이 있는 모든 LLM 플랫폼은 이를 호출할 수 있으며 핸들러가 최종 사용자를 식별할 수 없습니다.

작업에서 주문, 권한 또는 계정 세부 사항을 반환하는 등 어떤 최종 사용자가 요청하는지 알아야 할 때 인증을 켭니다. LLM 플랫폼은 **ID 공급자(IdP)를 사용하여 사용자에게 로그인하고, 모든 호출과 함께 결과 액세스 토큰을 전송하며, 핸들러가 확인된 ID를 수신합니다.**

**여정:** 리소스 식별자→ 복사하여 ID 공급자→ 구성하고 인증→ 사용하도록 설정하고 각 작업→ 대해 인증 모드→ 설정하여 핸들러에서 ID를 읽고 보호된 앱→ 테스트합니다.

이는 처음 실행되는 여정의 일부가 아닌 고급 분기입니다. [첫 번째 앱을 자동으로 만들기](/help/guides/create-app.md)를 완료하고 먼저 [앱을 배포](/help/guides/deploy-your-app.md)하세요.

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

인증이 **환경당** 구성되었습니다. **[!UICONTROL Stage]** 및 **[!UICONTROL Production]**&#x200B;에는 독립 설정이 있으므로 **[!UICONTROL Production]**&#x200B;에서 활성화하기 전에 개발 IdP 테넌트에 대해 구성을 확인할 수 있습니다.

## 시작하기에 앞서

- 비대칭 알고리즘으로 서명된 **JWT** 액세스 토큰을 발급하는 OAuth 2.1 또는 OpenID Connect ID 공급자입니다. 불투명 토큰 및 HMAC 서명 토큰은 지원되지 않습니다. [토큰 요구 사항](/help/reference/authentication-reference.md#token-requirements)을 참조하세요.
- API 및 클라이언트를 등록할 수 있도록 해당 ID 공급자에 대한 관리자 액세스 권한.
- 앱이 구성 중인 환경에 한 번 이상 배포되었습니다. 배포된 MCP 서버 URL은 토큰의 범위가 지정되어야 하는 값입니다.

## 리소스 식별자 복사

앱의 **리소스 식별자**&#x200B;은(는) MCP 서버 URL입니다. ID 공급자가 이 앱에 대해 발급하는 모든 액세스 토큰은 정확한 URL의 이름을 대상으로 지정해야 합니다. 이러한 바인딩은 다른 서비스에 대해 지정된 토큰이 앱에 대해 재생되지 않도록 하는 것입니다.

1. 앱 세부 사항 페이지를 엽니다.
2. **[!UICONTROL 앱 테스트]**(으)로 스크롤합니다.
3. 구성하고 있는 환경에서 **[!UICONTROL URL 복사]**&#x200B;를 선택합니다.

![앱 세부 정보 — 스테이징 MCP 서버 URL 복사](/help/assets/guide-onboarding-agent/app-mcp-url.png)

이 값 유지: 다음 단계의 ID 공급자에 필요합니다. 복사한 URL을 다시 입력하는 대신 붙여넣습니다. 대상 검사는 경로 구성 요소를 포함한 정확한 문자열 일치이므로 단일 문자 차이로 인해 모든 토큰이 유효성 검사에 실패합니다.

>[!NOTE]
>
>각 환경에는 고유한 MCP 서버 URL이 있으므로 자체 대상이 있습니다. **[!UICONTROL Stage]** 및 **[!UICONTROL Production]**&#x200B;을(를) 별도로 구성하십시오.

## ID 공급자 구성

정확한 단계는 공급자마다 다르지만 모든 공급자는 동일한 4가지를 필요로 합니다.

1. **앱을 API(리소스)로 등록합니다.** 해당 식별자(공급자가 토큰의 `aud` 클레임에 넣는 값)를 복사한 MCP 서버 URL로 설정합니다. 공급자는 이 필드에 다양하게 레이블을 지정합니다(일반적으로 *식별자* 또는 *대상*). `api`과(와) 같은 제네릭 값을 사용하지 마십시오. 식별자는 이 앱에 대해 고유해야 합니다. 그렇지 않으면 다른 서비스용으로 발급된 토큰을 이 앱에 대해 재생할 수 있습니다.
2. `orders:read` 또는 `profile:read`과(와) 같이 작업을 시작할 **범위를 정의합니다**. 의미 있는 권한당 하나의 범위를 사용하므로 작업은 필요한 사항만 요청합니다.
3. **지원 PKCE.** LLM 플랫폼은 모든 인증 요청에서 `code_challenge_method=S256`과(와) 함께 `code_challenge`을(를) 전송하므로 인증 서버는 S256 PKCE를 지원하고 메타데이터에 `"code_challenge_methods_supported": ["S256"]`을(를) 광고해야 합니다.
4. **LLM 플랫폼에서 클라이언트로 등록할 수 있도록 허용합니다.** 지원되는 LLM 플랫폼은 인증 서버에 대해 자체 OAuth 클라이언트를 생성하므로 공급자가 제공하는 경우 동적 클라이언트 등록을 사용할 수 있습니다. 그렇지 않으면 공용 클라이언트를 수동으로 만들고 공급자에게 비밀 클라이언트 인증이 필요한 경우에만 해당 클라이언트 ID와 암호를 플랫폼에 커넥터를 설정하는 동안 제공합니다. 플랫폼 문서에서 리디렉션 URI를 등록합니다. [!DNL Claude]의 호스팅된 표면(`https://claude.ai/api/mcp/auth_callback`)에 대해 등록하십시오. 일부 플랫폼은 사용자가 만드는 각 커넥터에 대해 고유한 리디렉션 URI([!DNL ChatGPT])를 발행하므로 커넥터 설정 화면에서 값을 읽고 첫 번째 로그인 전에 등록하십시오. 등록되지 않은 리디렉션 URI로 인해 권한 부여 서버가 권한 부여 요청을 즉시 거부합니다.

>[!IMPORTANT]
>
>ID 공급자의 발급자, JWKS, 인증 및 토큰 종단점은 모두 공개 HTTPS를 통해 접근 가능해야 합니다. LLM 플랫폼과 배포된 앱은 모두 공급자로부터 메타데이터를 직접 가져오므로 VPN 또는 IP 허용 목록에 추가하다 뒤에 있는 ID 공급자는 로그인을 완료할 수 없습니다. 공급자 앞에 있는 방화벽 또는 웹 애플리케이션 방화벽이 일반적인 원인이며 앱 자체에 연결할 수 있는 경우에도 흐름이 끊어질 수 있습니다.

## 인증 켜기

1. 왼쪽 탐색에서 **[!UICONTROL 설정]**&#x200B;을 선택한 다음 **[!UICONTROL 인증]** 탭을 엽니다.
2. **[!UICONTROL Workspace]**&#x200B;에서 **[!UICONTROL 단계]** 또는 **[!UICONTROL 프로덕션]**&#x200B;을 선택하세요.
3. **[!UICONTROL 인증 사용]**&#x200B;을 켭니다.
4. **[!UICONTROL 핵심 설정]**&#x200B;에서 다음을 입력하십시오.
   - **[!UICONTROL 발급자]** — ID 공급자의 발급자 URL입니다. 이 URL은 각 토큰의 `iss` 클레임에 넣는 값입니다. 필수 항목이며, HTTPS여야 합니다. 또한 앱의 인증 서버로 게시되어 LLM 플랫폼에서 사용자를 보낼 위치를 검색할 수 있습니다. 앱당 하나의 ID 공급자만 지원됩니다.
   - **[!UICONTROL 지원되는 범위]** — 이 앱의 작업에 필요한 모든 범위입니다. ID 공급자에서 정의한 범위를 미러링합니다.
5. **[!UICONTROL 고급 설정]**&#x200B;은(는) 선택 사항입니다. 서명 키가 인증 서버의 메타데이터에 광고되는 위치에 있지 않은 경우에만 **[!UICONTROL JWKS URI]**&#x200B;을(를) 설정합니다. 그렇지 않으면 앱에서 자동으로 검색합니다.
6. **[!UICONTROL 저장]**&#x200B;을 선택합니다.

![인증 — 인증을 활성화하고 핵심 설정을 완료합니다](/help/assets/guide-authentication/auth-core-settings.png)

각 필드에서 허용하는 내용은 [인증 설정](/help/reference/authentication-reference.md#authentication-settings)을 참조하십시오.

## 각 작업에 대한 인증 모드 선택

**[!UICONTROL 인증 사용]**&#x200B;을 켜면 현재 **[!UICONTROL 없음]**(으)로 설정된 모든 작업이 **[!UICONTROL 필수]**(으)로 변경됩니다. **[!UICONTROL 작업별 구성]**&#x200B;에서 해당 할당을 검토하고 각 작업에 필요한 모드를 설정하십시오.

| 모드 | 비헤이비어 |
|------|----------|
| **[!UICONTROL 없음]** | 공개. 작업은 토큰 없이 호출할 수 있습니다. |
| **[!UICONTROL 필수]** | 게이티드. 작업은 나열된 모든 범위를 전달하는 유효한 토큰으로만 호출할 수 있습니다. 인증되지 않은 발신자는 로그인해야 합니다. |
| **[!UICONTROL 선택 사항]** | 익명으로 호출할 수 있지만 해당 작업에서는 로그인을 지원한다고 광고하기도 합니다. 처리기가 호출별로 일반 결과를 제공할지 또는 사용자에게 개인화된 결과에 로그인하도록 요청할지 여부를 결정합니다. |

![인증 — 각 작업에 대한 인증 모드 및 범위를 설정합니다](/help/assets/guide-authentication/auth-per-action.png)

작업이 이미 **[!UICONTROL 필수]** 또는 **[!UICONTROL 선택적]**(으)로 설정되어 있으면 기존 모드를 유지합니다.

**[!UICONTROL 필수]** 또는 **[!UICONTROL 선택적]** 작업의 경우 필요한 **[!UICONTROL 범위]**&#x200B;를 추가하십시오. 모든 범위는 위의 **[!UICONTROL 지원되는 범위]**&#x200B;에 이미 표시되어야 합니다. 그렇지 않으면 앱에서 LLM 플랫폼에 알리지 않는 권한이 필요합니다. 불일치가 해결될 때까지 저장이 차단됩니다.

**[!UICONTROL 지원되는 범위]**&#x200B;은(는) 이 목록에 대한 권한입니다. 여기에서 범위를 제거하면 변경하는 즉시 필요한 모든 작업에서 해당 범위가 제거됩니다. 따라서 먼저 해당 범위를 추가한 다음 작업에 할당합니다.

**[!UICONTROL 모든 작업에 대한 인증 필요]**&#x200B;모든 작업을 **[!UICONTROL 필수]**(으)로 설정합니다. 지우는 중 **[!UICONTROL 없음]**&#x200B;에 모든 작업이 반환됩니다.

완료되면 **[!UICONTROL 저장]**&#x200B;을 선택하세요. 인증 모드 및 범위 변경 사항은 앱 수준 설정과 함께 저장됩니다.

>[!IMPORTANT]
>
>**[!UICONTROL 인증 사용]**&#x200B;을 해제하면 선택한 환경에 대해 이 작업별 구성이 삭제됩니다. 모든 작업의 모드와 범위는 지워지고 기억되지 않습니다. 다시 켜는 작업은 모두-**[!UICONTROL 필수]**&#x200B;부터 다시 시작됩니다.

>[!NOTE]
>
>모든 작업을 **[!UICONTROL 없음]**(으)로 설정해도 인증이 비활성화되지 않습니다. 이 상태에서는 호출이 거부되지 않지만 앱은 여전히 인증 서버를 LLM 플랫폼에 광고하므로 클라이언트는 사용자에게 추가 액세스 권한을 부여하지 않는 로그인을 제공할 수 있습니다. 앱을 완전히 공개하려면 **[!UICONTROL 인증 사용]**&#x200B;을 해제하고 배포하십시오.

앱 간 혼합 모드(일부 공개 작업, 다른 작업 게이트)가 지원되며 [!DNL ChatGPT]은(는) 각 작업의 모드를 개별적으로 적용합니다. 제어된 작업만 사용자에게 로그인하도록 메시지를 표시합니다.

>[!IMPORTANT]
>
>[!DNL Claude]은(는) 예외입니다. 작업별로 인증을 적용하므로 앱의 작업이 **[!UICONTROL 필수]** 또는 **[!UICONTROL 선택 사항]**(으)로 설정된 경우 [!DNL Claude]은(는) **[!UICONTROL 없음]**(으)로 설정된 작업을 포함하여 커넥터를 사용하기 전에 사용자에게 로그인하도록 요청합니다. [!DNL Claude]명의 사용자에 대해 작업을 공개로 유지하려면 별도의 앱에서 호스팅하십시오.

## 변경 사항 배포

인증 변경 사항은 이 앱의 다음 배포에 적용됩니다. 구성한 환경에 **앱을 다시 배포**&#x200B;합니다. [앱 배포](/help/guides/deploy-your-app.md)를 참조하세요.

MCP 서버 URL은 변경되지 않으므로 이미 만든 플러그인이나 커넥터는 계속 작동합니다. 이제 게이팅되었으므로 해당 사용자는 다음에 해당 앱을 사용할 때 로그인하라는 요청을 받습니다.

## 핸들러에서 ID 읽기

확인된 ID가 두 번째 인수로 처리기에 도달합니다. 호출자가 작업의 인증 모드와 관계없이 유효한 토큰을 보낼 때마다 토큰이 존재하므로 **[!UICONTROL Optional]** 작업은 토큰이 있을 때 결과를 개인화할 수 있고 없을 때는 결과를 반환할 수 있습니다.

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

토큰을 직접 확인할 필요는 없습니다. **[!UICONTROL Required]** 작업의 경우 런타임은 사용자가 지정한 범위를 포함하는 올바른 토큰이 없는 모든 호출을 차단하므로 처리기가 승인된 호출자에 대해서만 실행됩니다. 예를 들어 **[!UICONTROL Optional]** 작업에서 게이트에 의존하지 않고 사용 권한에 분기하려면 `hasScope`을(를) 사용합니다.

**[!UICONTROL 선택적]** 작업은 `extra.challengeAuth()`을(를) 반환하여 사용자에게 대화 중간에 로그인하도록 요청할 수 있습니다. **[!UICONTROL 선택 사항]** 작업에서만 사용할 수 있습니다.

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

`signIn`이(가) 여기서 수행하는 것처럼 사용자의 단어를 검사하는 대신 명시적 입력 매개 변수에서 에스컬레이션할지 여부를 결정합니다.

보고 중인 조건과 일치하도록 `error`을(를) 설정하십시오. 위의 예처럼 호출자에게 올바른 세션이 없어 로그인해야 하는 경우 `invalid_token`을(를) 사용하고, 호출자가 이미 로그인했지만 토큰에 필요한 범위가 없는 경우 `insufficient_scope`을(를) 사용합니다. LLM 플랫폼은 사용자에게 표시되는 프롬프트의 문구를 선택하고 플랫폼별로 해당 문구가 이 값에 따라 얼마나 달라지는지 선택합니다. 따라서 조건을 정확하게 설명하는 코드를 전송합니다.

`!extra.authInfo` 검사가 여기서 수행하는 것처럼 필요한 ID가 실제로 누락된 경우에만 도전하십시오. 무조건 도전하는 핸들러는 로그인하면 충족할 수 없으므로 모든 호출 시 다시 인증하라는 메시지가 표시됩니다.

>[!NOTE]
>
>[!DNL ChatGPT]에서 이 방식으로 발생한 로그인은 사용자에게 추가 권한을 부여하지 않고 커넥터를 다시 연결하도록 요청합니다. [!DNL Claude]에서 사용자가 로그인하면 작업이 실행되므로 작업을 실행할 필요가 없습니다.

ID 서버측을 유지합니다. 위젯에 필요한 내용만 `structuredContent`에 전달하고 액세스 토큰을 여기에 추가하지 마십시오. [생성된 처리기 사용자 지정](/help/guides/customize-handler.md)을 참조하십시오.

전체 계약에 대해서는 [처리기 인증 API](/help/reference/authentication-reference.md#handler-auth-api)를 참조하십시오.

## 보호된 앱 테스트

기존 플러그인 또는 커넥터가 배포 후 변경 사항을 선택합니다. 처음부터 설정하려면:

### [!DNL ChatGPT]

**[!UICONTROL 새 플러그 인]** 대화 상자에서 앱의 작업을 구성한 방식과 일치하도록 **[!UICONTROL 인증]**&#x200B;을 설정하십시오.

| 앱의 작업 | 선택 |
|--------------------|--------|
| 모두 **[!UICONTROL 없음]**(으)로 설정 | **[!UICONTROL 인증 없음]** |
| 모두 **[!UICONTROL 필수]**(으)로 설정됨 | **[!UICONTROL OAuth]** |
| 기타 조합 | **[!UICONTROL 혼합]** |

![ChatGPT — 플러그 인의 인증 모드를 선택합니다](/help/assets/guide-authentication/chatgpt-authentication-mode.png)

**[!UICONTROL Optional]** 액션은 항상 익명 호출을 허용하므로 이 액션을 포함하는 앱은 완전히 제어되지 않습니다. 모든 액션이 **[!UICONTROL Optional]**(으)로 설정되어 있더라도 **[!UICONTROL Mixed]**&#x200B;을(를) 선택하십시오. 인증되지 않은 발신자는 **[!UICONTROL Required]**&#x200B;만 거부합니다.

나머지 대화 상자에 대해서는 [ChatGPT 플러그인 테스트](/help/guides/test-in-chatgpt.md)를 참조하십시오.

### [!DNL Claude]

사용자 지정 커넥터를 추가한 다음 **[!UICONTROL 연결]**&#x200B;을 선택하고 ID 공급자가 제공하는 로그인을 완료합니다. 인증 옵션을 선택할 수 없습니다. [!DNL Claude]은(는) 동작이 제어될 때마다 전체 커넥터를 게이트합니다. [클라우드 커넥터 테스트](/help/guides/test-in-claude.md)를 참조하십시오.

### 확인

- 플랫폼은 사용자를 자체 ID 공급자의 로그인 페이지로 리디렉션합니다.
- 보호된 작업은 로그인 후 사용자별 데이터를 반환합니다.
- 로그아웃하면 보호된 작업에 로그인하라는 메시지가 표시됩니다.
- [!DNL ChatGPT]에서 **[!UICONTROL 없음]**(으)로 설정된 작업이 로그인하지 않고 계속 응답합니다. [!DNL Claude]에서 전체 커넥터가 게이트됩니다.

로그인이 시작되지 않거나 토큰이 거부되면 [문제 해결](/help/reference/troubleshooting.md#authentication)을 참조하세요.

## 보안 지침

- 각 작업에 필요한 가장 좁은 범위를 부여합니다. 모든 작업에서 하나의 넓은 범위를 재사용하지 마십시오.
- ID 공급자와 LLM 플랫폼의 커넥터 구성에서 클라이언트 암호를 유지하십시오. 작업 메타데이터, 핸들러 코드, 위젯 JavaScript 또는 소스 제어에 배치하지 마십시오.
- 토큰 클레임을 외부 시스템의 입력으로 처리합니다. 쿼리에서 사용하기 전에 `authInfo.extra`에서 읽은 모든 항목의 유효성을 검사하십시오.
- 인증뿐 아니라 인증도 수행합니다. 유효한 토큰은 사용자가 누구인지 증명하며, 특정 레코드가 표시되는지 증명합니다. 데이터를 반환하기 전에 핸들러에서 소유권을 확인합니다.
- 토큰, 전체 클레임 세트 또는 사용자 식별자를 기록하지 마십시오.
- 안전 오류를 반환합니다. 사용자에게 업스트림 ID 공급자 응답 또는 스택 추적을 표시하지 마십시오.
- **[!UICONTROL 프로덕션]**&#x200B;에서 인증을 활성화하기 전에 비프로덕션 ID 공급자 테넌트에 대해 **[!UICONTROL Stage]**&#x200B;을(를) 설정하고 확인하십시오.

## 다음 단계

- [인증 참조](/help/reference/authentication-reference.md) — 필드, 토큰 요구 사항 및 플랫폼 동작.
- [생성된 처리기를 사용자 지정](/help/guides/customize-handler.md) — 처리기에서 보호된 업스트림 API를 호출합니다.
- [앱 배포](/help/guides/deploy-your-app.md).
