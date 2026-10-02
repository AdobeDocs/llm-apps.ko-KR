---
title: LLM 앱을 클라우드 커넥터로 테스트
description: Adobe LLM 앱 MCP 서버 URL에서 클라우드 커넥터를 만들고 대화에서 테스트합니다.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '448'
ht-degree: 1%
---

# LLM 앱을 [!DNL Claude] 커넥터로 테스트 {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

배포 후 LLM 앱은 MCP 서버 URL을 노출합니다. 이 URL을 [!DNL Claude]에 사용자 지정 커넥터로 추가한 다음 생성된 작업 및 위젯을 테스트합니다.

앱을 빌드하거나, 사용자 정의하거나, 확장한 후의 최종 확인 단계입니다.

이 안내서에서는 앱의 작업이 공개라고 가정합니다. 앱에 최종 사용자 인증이 활성화되어 있으면 커넥터를 사용하기 전에 [!DNL Claude]에서 앱의 ID 공급자로 로그인하라는 메시지가 표시되며, 로그인할 때까지 도구는 표시되지 않습니다. [자체 ID 공급자를 사용하여 최종 사용자 인증](/help/guides/authentication.md)을 참조하십시오.

## 플랜 요구 사항

원격 MCP를 사용하는 사용자 지정 커넥터는 [!DNL Claude], [!DNL Claude] 데스크톱에서 사용할 수 있으며 무료, Pro, Max, Team 및 Enterprise 요금제에 사용할 수 있습니다. 무료 플랜 계정은 하나의 사용자 정의 커넥터로 제한됩니다. 팀 및 기업 조직의 경우 소유자 또는 기본 소유자가 커넥터를 활성화해야 다른 구성원이 사용할 수 있습니다.

## MCP 서버 URL 복사

[!DNL LLM Apps]에서:

1. 앱 세부 사항 페이지를 엽니다.
2. **[!UICONTROL 앱을 테스트]**&#x200B;하세요.
3. **[!UICONTROL 스테이징 환경]**&#x200B;에서 **[!UICONTROL URL 복사]**&#x200B;를 선택합니다.

## 사용자 지정 커넥터 추가

1. [claude.ai/new?modal=add-custom-connector](https://claude.ai/new?modal=add-custom-connector#settings/customize-connectors)을(를) 엽니다. **[!UICONTROL 사용자 지정 커넥터 추가]** 대화 상자가 바로 열립니다.
2. 다음을 입력합니다.
   - **[!UICONTROL 이름]** — 커넥터 이름입니다.
   - **[!UICONTROL 원격 MCP 서버 URL]** — 복사한 MCP 서버 URL입니다.
3. **[!UICONTROL 추가]**&#x200B;를 선택합니다.

   ![클라우드 — 사용자 지정 커넥터 추가 대화 상자](/help/assets/guide-test-claude/claude-add-custom-connector.png)

>[!NOTE]
>
>신뢰할 수 있는 개발자의 커넥터만 사용합니다. Anthropic은 개발자가 사용할 수 있는 도구를 제어하지 않으며, 의도한 대로 작동할지 또는 변경되지 않는지 확인할 수 없습니다.

## 생성된 도구 허용

생성된 각 작업은 커넥터 페이지의 **[!UICONTROL 도구 권한]** 아래에 나열됩니다. 기본적으로 새 도구는 **[!UICONTROL 승인이 필요합니다]**(으)로 설정되어 테스트 중에 모든 호출을 승인하라는 메시지가 표시됩니다.

승인 프롬프트에 의해 테스트가 중단되지 않도록 각 도구(또는 전체 **[!UICONTROL 대화형 도구]** 그룹)를 **[!UICONTROL 항상 허용]**&#x200B;으로 설정하십시오.

![클라우드 — 도구 권한을 항상 허용으로 설정](/help/assets/guide-test-claude/claude-tool-permissions.png)

## 커넥터 테스트

1. 새 채팅을 시작합니다.
2. 메시지 상자에서 **+**&#x200B;을(를) 선택하거나 `/` 유형을 선택하고 **[!UICONTROL 커넥터]**&#x200B;를 마우스로 가리킨 다음 이 대화에 추가한 커넥터를 켭니다.

   ![클로드 — 대화에 커넥터를 사용하도록 설정](/help/assets/guide-test-claude/claude-enable-connector-chat.png)

3. 생성된 작업 중 하나와 일치하는 질문을 합니다. 예: *커피 좀 보여 주세요.*

다음을 확인합니다.

- [!DNL Claude]에서 필요한 작업을 호출합니다.
- 위젯에 예상 샘플 데이터가 표시됩니다.
- 텍스트 응답은 위젯과 일치합니다.
- 위젯 컨트롤은 예상대로 작동합니다.

## 다음 단계

- [생성된 위젯을 사용자 지정](/help/guides/widgets.md)합니다.
- [작업을 처음부터 만듭니다](/help/guides/create-action.md).
