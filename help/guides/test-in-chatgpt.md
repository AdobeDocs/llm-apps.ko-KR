---
title: LLM 앱을 ChatGPT 플러그인으로 테스트
description: Adobe LLM 앱 MCP 서버 URL에서 ChatGPT 플러그인을 만들고 대화에서 테스트합니다.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '378'
ht-degree: 1%
---

# LLM 앱을 [!DNL ChatGPT] 플러그 인으로 테스트합니다. {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

배포 후 LLM 앱은 MCP 서버 URL을 노출합니다. 이 URL을 [!DNL ChatGPT]에 플러그인으로 추가한 다음 생성된 작업 및 위젯을 테스트합니다.

앱을 빌드하거나, 사용자 정의하거나, 확장한 후의 최종 확인 단계입니다.

## 플랜 요구 사항

개발자 모드는 웹에서 Pro, Plus, Business, Enterprise 및 Education 계정용으로 사용할 수 있습니다. Workspace 관리자는 액세스를 제한할 수 있습니다.

## 개발자 모드 활성화

[!DNL ChatGPT]에서:

1. [!UICONTROL 보안 및 로그인&#x200B;]**→**[!UICONTROL &#x200B;설정]을 엽니다.
2. **[!UICONTROL 개발자 모드]**&#x200B;를 켭니다.

Plugins 페이지의 더하기 버튼은 개발자 모드가 활성화된 후에만 MCP 지원 플러그인을 만듭니다. [ChatGPT 개발자 모드](https://developers.openai.com/api/docs/guides/developer-mode)를 참조하세요.

## MCP 서버 URL 복사

[!DNL LLM Apps]에서:

1. 앱 세부 사항 페이지를 엽니다.
2. **[!UICONTROL 앱을 테스트]**&#x200B;하세요.
3. **[!UICONTROL 스테이징 환경]**&#x200B;에서 **[!UICONTROL URL 복사]**&#x200B;를 선택합니다.

## 플러그인 만들기

1. [chatgpt.com/plugins](https://chatgpt.com/plugins)을(를) 엽니다.
2. **[!UICONTROL 플러그인]** 탭에서 검색 필드 옆의 **+**&#x200B;을(를) 선택합니다.

   ![ChatGPT — 플러그인 페이지](/help/assets/guide-onboarding-agent/chatgpt-plugins-page.png)

3. **[!UICONTROL 새 플러그 인]**&#x200B;에서 다음을 입력하십시오.
   - **[!UICONTROL 이름]** — 플러그 인 이름입니다.
   - **[!UICONTROL 설명]** — 선택 사항입니다.
   - **[!UICONTROL 연결]** — **[!UICONTROL 서버 URL]**&#x200B;을(를) 선택하고 MCP 서버 URL을 붙여넣습니다.
   - **[!UICONTROL 인증]** — **[!UICONTROL 인증 안 함]**&#x200B;을 선택합니다.

   >[!NOTE]
   >
   >앱의 모든 작업이 공개되는 동안에는 **[!UICONTROL 인증 없음]**&#x200B;이 적용됩니다. 최종 사용자 인증을 설정한 경우 모든 작업이 **[!UICONTROL 필수]**(으)로 설정되어 있으면 **[!UICONTROL OAuth]**&#x200B;을(를) 선택하고, 다른 조합을 사용하려면 **[!UICONTROL 혼합]**&#x200B;을(를) 선택합니다. [자체 ID 공급자로 최종 사용자 인증](/help/guides/authentication.md)을(를) 참조하십시오.

4. **[!UICONTROL 이해했고 계속 진행하겠습니다]**.
5. **[!UICONTROL 만들기]**&#x200B;를 선택합니다.

   ![ChatGPT — MCP 서버 URL로 플러그인 만들기](/help/assets/guide-onboarding-agent/chatgpt-new-plugin.png)

6. 확인 대화 상자에서 **[!UICONTROL 연결]**&#x200B;을 선택합니다.

   ![ChatGPT — 새 플러그 인을 연결합니다](/help/assets/guide-onboarding-agent/chatgpt-plugin-connect.png)

## 플러그인 테스트

1. 새 채팅을 시작합니다.
2. 플러스 메뉴에서 **[!UICONTROL 개발자 모드]**&#x200B;를 선택하고 플러그인을 선택합니다.
3. 생성된 작업 중 하나와 일치하는 질문을 합니다. 예: *커피 좀 보여 주세요.*

![ChatGPT — 생성된 LLM 앱 플러그인 응답](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

다음을 확인합니다.

- [!DNL ChatGPT]에서 필요한 작업을 호출합니다.
- 위젯에 예상 샘플 데이터가 표시됩니다.
- 텍스트 응답은 위젯과 일치합니다.
- 위젯 컨트롤은 예상대로 작동합니다.

## 다음 단계

- [생성된 위젯을 사용자 지정](/help/guides/widgets.md)합니다.
- [작업을 처음부터 만듭니다](/help/guides/create-action.md).
