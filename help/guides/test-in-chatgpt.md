---
title: LLM 앱을 ChatGPT 플러그인으로 테스트
description: Adobe LLM 앱 MCP 서버 URL에서 ChatGPT 플러그인을 만들고 대화에서 테스트합니다.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '546'
ht-degree: 0%
---

# LLM 앱을 [!DNL ChatGPT] 플러그인으로 테스트합니다. {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

배포 후 LLM 앱은 MCP 서버 URL을 노출합니다. 이 URL을 [!DNL ChatGPT]에 플러그인으로 추가한 다음 생성된 작업 및 위젯을 테스트합니다.

**이 안내서:** 배포된 앱을 [!DNL ChatGPT]에 연결하고 대화에서 해당 동작 및 위젯을 테스트하는 방법을 알아봅니다. 앱을 변경한 후 연결을 새로 고칩니다.

## 플랜 요구 사항

사용자 지정 MCP 서버 추가를 허용하는 계정 및 작업 영역을 사용합니다. 사용 가능 여부 및 관리자 제어는 계정 및 작업 공간에 따라 다릅니다. OpenAI의 현재 [연결 및 플러그인 테스트](https://developers.openai.com/plugins/deploy/connect-chatgpt) 설명서를 참조하십시오.

## 사용자 지정 MCP 서버에 대한 액세스 확인

[ChatGPT 플러그인](https://chatgpt.com/plugins)을 열고 계정에 사용자 지정 MCP 서버 추가가 허용되는지 확인하십시오. 계정에 개발자 모드가 필요한 경우 계정의 컨트롤을 사용하여 해당 모드를 활성화합니다. 사용자 지정 MCP 서버 옵션이 없거나 차단된 경우 작업 영역 관리자에게 문의하십시오.

## MCP 서버 URL 복사

[!DNL LLM Apps]에서:

1. 앱 세부 사항 페이지를 엽니다.
2. **[!UICONTROL 앱을 테스트]**&#x200B;하세요.
3. **[!UICONTROL 스테이징 환경]**&#x200B;에서 **[!UICONTROL URL 복사]**&#x200B;를 선택합니다.

## 플러그인 만들기

1. [chatgpt.com/plugins](https://chatgpt.com/plugins)을(를) 엽니다.
2. **+**&#x200B;을(를) 선택한 다음 **[!UICONTROL 사용자 지정 MCP 서버 추가]**&#x200B;를 선택합니다.
3. 연결을 구성합니다.
   - **[!UICONTROL 이름]** — 플러그 인 이름입니다.
   - **[!UICONTROL 설명]** — 선택 사항입니다.
   - 공개 서버 연결 방법을 선택하고 앱의 전체 HTTPS MCP 서버 URL을 입력합니다.
   - 앱과 일치하는 인증 방법을 선택합니다. 인증이 비활성화된 공용 앱의 경우 제공된 위치에서 **[!UICONTROL 인증 없음]**&#x200B;을 사용합니다.

   >[!NOTE]
   >
   >최종 사용자 인증을 설정한 경우 모든 작업이 **[!UICONTROL 필수]**(으)로 설정된 경우 **[!UICONTROL OAuth]**&#x200B;을(를) 선택하고, 공개 작업과 보호 작업의 조합으로 설정된 경우 **[!UICONTROL 혼합]**&#x200B;을(를) 선택합니다. 여기서 이러한 선택 사항이 제공됩니다. [자체 ID 공급자를 사용하여 최종 사용자 인증](/help/guides/authentication.md)을 참조하십시오.

4. **[!UICONTROL 이해했고 계속 진행하겠습니다]**.
5. **[!UICONTROL 플러그 인으로 만들기]**&#x200B;를 선택합니다.
6. 검색된 도구 및 메타데이터를 검토한 다음 결과 플러그인을 설치하거나 연결합니다. 앱이 필요한 경우 로그인을 완료합니다.

OpenAI는 설정 UI를 소유하며 레이아웃은 계정에 따라 다를 수 있습니다. 표시된 레이블이 다른 경우 현재 [연결 지침](https://developers.openai.com/plugins/deploy/connect-chatgpt)을(를) 따릅니다.

## 플러그인 테스트

1. 새 채팅을 시작합니다.
2. 프롬프트 상자에 `@`을(를) 입력하고 설치된 플러그인을 선택합니다.
3. 생성된 작업 중 하나와 일치하는 질문을 합니다. 예: *커피 좀 보여 주세요.*

![실례가 되는 ChatGPT 응답 — Frescopa 작업이 대화에서 제품 카드를 반환합니다](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

다음을 확인합니다.

- [!DNL ChatGPT]에서 필요한 작업을 호출합니다.
- 위젯에 예상 샘플 데이터가 표시됩니다.
- 텍스트 응답은 위젯과 일치합니다.
- 위젯 컨트롤은 예상대로 작동합니다.

또한 잘못된 입력, 빈 결과, 앱을 호출하지 않는 관련 없는 프롬프트 및 데이터를 변경하는 작업도 테스트합니다. 모든 요청을 미리 승인하는 대신 각 확인 및 인증 프롬프트를 검토하십시오.

## 앱 변경 후 새로 고침

작업 설명, 입력, 인증 또는 위젯 구성을 변경한 후:

1. 업데이트된 앱을 동일한 환경에 배포합니다.
2. [ChatGPT 플러그인](https://chatgpt.com/plugins)에서 연결을 열고 **[!UICONTROL 새로 고침]**&#x200B;을 선택하세요.
3. 광고된 작업이 변경되었는지 확인합니다.
4. 새 대화를 시작하고 영향을 받는 테스트를 반복합니다.

## 다음 단계

- [생성된 위젯을 사용자 지정](/help/guides/widgets.md)합니다.
- [작업을 처음부터 만듭니다](/help/guides/create-action.md).
