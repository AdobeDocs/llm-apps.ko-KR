---
title: ChatGPT 제출을 위한 앱 준비
description: Adobe LLM 앱을 사용하여 앱에 대한 제출 패키지, 준비 보고서 및 테스트 녹화본을 준비한 다음 OpenAI 포털에서 앱을 제출합니다.
source-git-commit: faa25ef012ea10a592fcc79460aa5ca7ad537cf8
workflow-type: tm+mt
source-wordcount: '1453'
ht-degree: 0%
---

# ChatGPT 제출을 위한 앱 준비 {#prepare-submission}

>[!IMPORTANT]
>
>LLM 앱은 모든 AEM Sites Cloud Service 고객이 자체 앱을 개발하고 관리할 수 있습니다. 계량이 시작되면 각 앱 호출은 고객의 AEM Sites 콘텐츠 요청에 계산됩니다. Adobe은 계량 시행 전 계량 요율과 시작 날짜를 발표합니다.

[!DNL ChatGPT] 플러그 인 디렉터리에 앱을 나열하려면 OpenAI에 제출 패키지를 업로드합니다. [!DNL LLM Apps]이(가) 준비하는 데 도움이 됩니다. 앱에서 패키지 ZIP을 만들고 배포된 앱에 테스트 요청을 보내고, 결과를 기록하여 확인할 수 있습니다. 그런 다음 패키지를 업로드하고 OpenAI 포털에서 제출을 완료합니다.

**이 안내서에서:** [!DNL LLM Apps]에서 앱에 대한 제출 패키지를 준비하는 방법과 OpenAI 포털에서 다음에 수행할 작업에 대해 알아봅니다.

>[!NOTE]
>
>[!DNL LLM Apps]이(가) OpenAI에 앱을 제출하지 않습니다. 개발자 및 도메인 인증, 연결 테스트, 검토 및 게시는 OpenAI 포털에서 수행됩니다. [플러그 인 업로드 및 제출](https://developers.openai.com/plugins/deploy/submission)을 참조하세요.

## 시작하기에 앞서

- [앱을 **프로덕션**&#x200B;에 배포](/help/guides/deploy-your-app.md)합니다. [!DNL LLM Apps]이(가) 테스트 요청을 앱의 프로덕션 버전에 보냅니다.
- 게시할 작업을 검토하고 활성화했는지 확인하십시오.
- 게시자 이름, 지원 페이지, 개인정보 처리방침 및 서비스 약관의 URL을 준비하십시오.
- 두 개의 아이콘, 즉 로고와 작성기 아이콘을 준비하십시오. 각 PNG는 사각형이고 애니메이션이 적용되지 않은 PNG, 64-4096픽셀, 1MiB 미만이어야 합니다. 게시할 권한이 있는 이미지만 사용하십시오.
- 앱 [최종 사용자 인증](/help/guides/authentication.md)의 경우 ID 공급자가 DCR(동적 클라이언트 등록) 또는 CIMD(클라이언트 ID 메타데이터 문서)를 지원하지 않는 한 ID 공급자에서 [!DNL ChatGPT]에 대한 OAuth 클라이언트를 등록하십시오.

## 제출 준비 열기

앱을 프로덕션에 처음 배포하면 앱 페이지에 **[!UICONTROL 앱 테스트]** 아래의 **[!UICONTROL 제출 준비]** 카드가 표시됩니다.

![제출 준비 - 프로덕션 준비 상태 및 설정](/help/assets/guide-submission/submission-card.png)

**[!UICONTROL 설정]**&#x200B;을 선택합니다. 나중에 카드의 단추가 **[!UICONTROL 진행률 보기]** 또는 **[!UICONTROL 결과 보기]**&#x200B;와 같은 다음 단계를 표시하도록 변경됩니다.

**[!UICONTROL 제출 준비]** 대화 상자가 열립니다. 여기에는 **[!UICONTROL 설정 및 준비]**, **[!UICONTROL 결과]** 및 **[!UICONTROL 실행 기록]**&#x200B;의 세 가지 섹션이 있습니다.

## 제출 세부 정보 추가

**[!UICONTROL 설정 및 준비]**&#x200B;에서:

1. **[!UICONTROL 게시자 및 법적 정보]**&#x200B;에서 **[!UICONTROL 게시자 이름]**, **[!UICONTROL 지원 URL]**, **[!UICONTROL 개인정보 처리방침 URL]** 및 **[!UICONTROL 서비스 약관 URL]**&#x200B;을 입력하십시오.
   - **[!UICONTROL 게시자 이름]**&#x200B;에 대해 OpenAI 포털에서 게시자로 확인할 공개 이름을 입력하십시오.
   - **[!UICONTROL 지원 URL]**&#x200B;의 경우 지원 URL을 입력하거나 페이지에 문의하십시오. 이메일 주소가 허용되지 않습니다.
   - 개인정보 처리방침은 앱, 인증, 위젯 및 분석이 수집하고 사용하는 데이터를 설명해야 합니다.
2. **[!UICONTROL 승인된 아이콘]**&#x200B;에서 **[!UICONTROL 로고 선택]**&#x200B;을 선택한 다음 **[!UICONTROL 작성기 아이콘 선택]**&#x200B;을 선택하고 각각에 대해 PNG 파일을 선택합니다.
3. 앱에서 인증을 사용하는 경우 **[!UICONTROL OAuth 클라이언트 등록]**&#x200B;을 열고 [!DNL ChatGPT]이(가) OAuth 클라이언트를 가져오는 방법을 선택하십시오. ID 공급자가 지원하는 옵션만 선택하십시오.
   - **[!UICONTROL 제공]** - [!DNL ChatGPT]에 클라이언트를 등록했습니다. **[!UICONTROL 등록된 클라이언트 ID]**&#x200B;를 입력하십시오. Adobe IMS 클라이언트 ID를 입력하지 마십시오.
   - **[!UICONTROL DCR]** - [!DNL ChatGPT]에서 동적 클라이언트 등록을 사용하여 클라이언트를 등록합니다.
   - **[!UICONTROL CIMD]** - [!DNL ChatGPT]은(는) 클라이언트 ID 메타데이터 문서로 자신을 식별합니다.

   [!DNL LLM Apps]이(가) 클라이언트 암호를 요청하지 않습니다.
4. 필요한 경우 **[!UICONTROL 추가 제출 세부 정보]**&#x200B;를 열고 목록 텍스트, 패키지 이름 및 버전, 구매 세부 정보, 호스트된 데모 URL, 지원되는 국가 및 릴리스 정보를 입력합니다. 필드를 비워 두면 [!DNL LLM Apps]이(가) 가능한 위치에 텍스트를 초안합니다. 초안을 작성할 수 없는 모든 항목은 나중에 **[!UICONTROL 제출하기 전에]**&#x200B;에 나타납니다.
5. **[!UICONTROL 변경 내용 저장]**&#x200B;을 선택합니다.

![설정 및 준비 - 게시자 및 법적 세부 정보, 승인 아이콘 및 변경 내용 저장](/help/assets/guide-submission/submission-setup.png)

준비는 저장한 세부 정보와 아이콘만 사용합니다.

>[!TIP]
>
>OpenAI 검토자는 열 수 있는 URL의 [!DNL ChatGPT]에 이상적으로 녹화된 앱의 데모 비디오가 필요합니다. [!DNL LLM Apps]의 테스트 레코딩은 사용자 확인을 위한 것이며 이 데모를 대체하지 않습니다. 패키지를 준비하기 전에 데모를 기록하고 검토자가 액세스할 수 있는 위치에 업로드한 다음 **[!UICONTROL 호스팅된 데모 URL]**&#x200B;에 해당 URL을 입력하십시오.

## 패키지 준비

**[!UICONTROL 패키지 준비]**&#x200B;에서 **[!UICONTROL 준비]**&#x200B;를 확인하세요. **[!UICONTROL 준비 완료]**&#x200B;는 저장된 세부 정보가 요구 사항을 충족함을 의미합니다. 그렇지 않으면 필수 세부 정보 또는 성공적인 프로덕션 배포와 같이 누락된 항목이 섹션에 나열됩니다.

1. **[!UICONTROL 준비할 때 수행되는 작업]**&#x200B;을 읽어 보세요. [!DNL LLM Apps]이(가) 패키지를 만들고 배포된 앱에 테스트 요청을 보냅니다. 몇 분 정도 소요될 수 있으며 테스트 요청이 앱의 분석에 표시될 수 있습니다.
2. **[!UICONTROL 을(를) 선택하십시오. 검토 증거를 준비하기 위해 배포된 앱을 호출하게 됩니다.]** 확인란.
3. **[!UICONTROL 패키지 준비]**&#x200B;를 선택합니다.

![패키지 준비 - 준비 완료, 확인 확인란 및 패키지 준비](/help/assets/guide-submission/submission-prepare.png)

준비가 실행되는 동안 대화 상자에 **[!UICONTROL 준비 진행 중]**&#x200B;이 표시됩니다. [!DNL LLM Apps]은(는) **[!UICONTROL 패키지 준비]**&#x200B;를 선택했을 때와 마찬가지로 앱과 세부 정보를 사용합니다.

![준비 진행 중 - 진행률 표시줄 및 시작 시간](/help/assets/guide-submission/submission-progress.png)

대화 상자를 닫고 나중에 다시 돌아올 수 있습니다. 준비가 백그라운드에서 계속되며 앱 페이지의 카드에 완료될 때까지 **[!UICONTROL 패키지 준비]**&#x200B;가 표시됩니다. 준비가 실패하면 저장된 세부 정보가 유지됩니다.

## 결과 검토

준비가 완료되면 **[!UICONTROL 결과]**&#x200B;를 엽니다. 섹션 맨 위에 결과가 요약되어 있습니다. **[!UICONTROL 준비에 주의가 필요합니다]**&#x200B;은(는) [!DNL LLM Apps]이(가) 초안 패키지를 만들었지만 자리 표시자 법률 링크와 같이 먼저 해결해야 하는 문제를 발견했음을 의미합니다.

![결과 - 준비 요약, 보고서 다운로드, 패키지 다운로드 및 녹화 다운로드](/help/assets/guide-submission/submission-results.png)

- **[!UICONTROL 패키지 및 보고서]** - **[!UICONTROL 패키지 다운로드]**&#x200B;를 선택하여 제출 ZIP을 가져오고 **[!UICONTROL 보고서 다운로드]**&#x200B;를 선택하여 준비 보고서를 가져옵니다. **[!UICONTROL 패키지 다운로드]**&#x200B;는 준비에서 전체 패키지를 생성하는 경우에만 사용할 수 있습니다.
- **[!UICONTROL 녹음/녹화 검토]** - **[!UICONTROL 녹음/녹화 다운로드]**&#x200B;를 선택하고 비디오를 시청하여 앱이 의도한 대로 작동하는지 확인합니다. 하나의 비디오에는 여러 테스트 사례가 포함될 수 있습니다.
- **[!UICONTROL 테스트 사례]** - 각 테스트 사례에 대해 전송된 요청과 그 상태를 확인하십시오. 앱이 수행할 작업을 보려면 **[!UICONTROL 예상 동작]**&#x200B;을 여십시오. 일부 테스트 사례에서는 앱이 지원하지 않는 사항을 요구합니다. 이에 대해 앱은 작업을 호출하지 않고 요청을 거부해야 합니다.

![테스트 사례 - 요청, 예상 동작 및 기록된 상태가 있는 검토 사례](/help/assets/guide-submission/submission-test-cases.png)

녹화는 [!DNL LLM Apps] 테스트 환경에서 가져왔습니다. [!DNL ChatGPT]이(가) 앱을 확인했거나 OpenAI가 앱을 승인했다는 의미는 아닙니다.

**[!UICONTROL 제출하기 전에]**&#x200B;패키지 버전, 앱에 구매 관련 여부, 호스트된 데모 URL, 지원되는 국가, 릴리스 정보 등 확인하거나 입력해야 하는 세부 정보를 나열합니다. 목록 아래에 각 검사에 **PASS**, **WARN** 또는 **FAIL**&#x200B;이 표시됩니다. 세부 정보를 보려면 검사를 확장합니다. 세부 정보를 수정하려면 **[!UICONTROL 세부 정보 업데이트]**&#x200B;를 선택하고 변경 내용을 저장한 다음 패키지를 다시 준비하십시오.

![제출하기 전에 - 남은 세부 정보, 업데이트 세부 정보 및 패키지 확인](/help/assets/guide-submission/submission-before-submit.png)

준비 후 앱 또는 세부 정보를 변경하면 **[!UICONTROL 결과]**&#x200B;에 이 실행 이후에 변경된 **[!UICONTROL 앱 또는 입력이 표시됩니다]**. 현재 앱 및 세부 정보에서 새 패키지를 만들려면 **[!UICONTROL 다시 준비]**&#x200B;를 선택하십시오. **[!UICONTROL 실행 기록]**&#x200B;은(는) 이전 준비를 나열합니다. 하나를 열어도 저장된 세부 정보가 변경되지 않습니다.

## OpenAI 포털에서 제출

패키지가 완료되면 다운로드하여 OpenAI 설명서의 [업로드 및 플러그인 제출](https://developers.openai.com/plugins/deploy/submission)을(를) 따라 다음을 수행합니다.

1. ZIP을 업로드하고 자동 검사에서 발견되는 모든 문제를 해결합니다.
2. MCP 서버를 연결하고 도메인 확인을 완료합니다.
3. 앱에 로그인이 필요한 경우 검토자를 위한 로그인 세부 정보를 포함한 검토 정보를 입력하고 검토를 위해 앱을 제출합니다.
4. 승인된 플러그인을 게시합니다.

도메인을 확인하려면:

1. 패키지를 업로드한 후 OpenAI 포털에 표시되는 챌린지 토큰을 복사합니다.
2. [!DNL LLM Apps]에서 앱의 **[!UICONTROL 설정]** > **[!UICONTROL 제출]**&#x200B;을 엽니다.
3. **[!UICONTROL 도메인 확인]**&#x200B;에서 토큰을 **[!UICONTROL OpenAI 챌린지 토큰]**&#x200B;에 붙여 넣고 **[!UICONTROL 저장]**&#x200B;을 선택합니다.
4. [앱을 프로덕션에 다시 배포](/help/guides/deploy-your-app.md). 토큰은 다음 배포 후에만 게시됩니다.
5. OpenAI 포털로 돌아가서 확인을 완료합니다.

[!DNL LLM Apps]은(는) 앱 도메인의 `/.well-known/openai-apps-challenge`에서 토큰을 제공하므로 직접 호스팅할 필요가 없습니다.

### 원활한 리뷰를 위한 팁

- **모든 작업의 인증을 확인하십시오.** OpenAI 포털이 MCP 서버를 스캔하면 모든 작업을 호출합니다. 작업이 인증이 필요한 것으로 표시되었지만 호출을 인증할 수 없는 경우 검사가 실패합니다. 그런 다음 앱을 수정하고 새 패키지를 준비한 다음 다시 업로드해야 합니다.
- **검토할 시간을 허용합니다.** 제출 후 OpenAI는 앱을 게시하기 전에 앱을 검토하고 이메일을 통해 피드백을 전송합니다. 릴리스 날짜를 계획할 때 이 시간을 고려하십시오.
- **변경 내용을 나열할 때 새 패키지를 업로드합니다.** 게시 후 [!DNL ChatGPT]은(는) 다시 배포할 때 MCP 서버에 대한 변경 내용을 자동으로 선택합니다. 목록 세부 정보 또는 기타 패키지 메타데이터를 변경하려면 새 패키지가 필요합니다. [!DNL LLM Apps]에서 다시 준비하고 OpenAI 포털에 업로드하십시오.

## 관련됨

- [앱 배포](/help/guides/deploy-your-app.md)
- [LLM 앱을 ChatGPT 플러그인으로 테스트](/help/guides/test-in-chatgpt.md)
- [ChatGPT 확장 구성](/help/guides/chatgpt-extensions.md)
