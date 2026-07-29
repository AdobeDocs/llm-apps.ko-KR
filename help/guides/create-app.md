---
title: 첫 번째 LLM 앱을 자동으로 만들기
description: 웹 사이트에서 Adobe LLM 앱을 만들고, 생성된 작업을 검토하고, 배포하고, ChatGPT와 같은 지원되는 LLM 플랫폼에서 테스트합니다.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '1217'
ht-degree: 0%

---


# 첫 번째 앱을 자동으로 만들기 {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

플랫폼은 웹 사이트를 작동 중인 앱 스캐폴드로 전환합니다. 작업을 제안하고, 핸들러 코드 및 테스트를 쓰고, EDS 위젯을 만들고, 생성된 파일을 자신이 소유한 두 개의 [!DNL GitHub] 리포지토리에 보냅니다.

생성하는 데 약 15분이 소요됩니다. 이 자습서를 마치면 [!DNL ChatGPT]과(와) 같은 지원되는 LLM 플랫폼에서 테스트할 수 있는 배포된 앱이 제공됩니다.

**여정:** 두 개의 저장소→ 만들고 앱→ 만들고 생성된 작업→ 검토하여 스테이징→ 배포하고 프로덕션 시스템→ 연결하기 → 플러그인을 테스트하기 위한 요구 사항을 확인합니다.

## 시작하기에 앞서

이 자습서를 시작하기 전에 모든 [LLM 앱 요구 사항](/help/overview/overview.md#requirements)을 완료하십시오.

이 자습서에서는 [Frescopa Coffee](https://frescopa.coffee/)용 LLM 앱을 만듭니다.

## 두 개의 빈 저장소 만들기

플랫폼에는 두 개의 빈 저장소가 필요합니다. 동일한 [!DNL GitHub] 계정 또는 조직에 두 계정을 모두 만듭니다.

- **처리기 저장소** - 작업 처리기 및 테스트를 저장합니다. 예: `my-brand-llm-app`
- **EDS 저장소** — 생성된 위젯 블록 및 스타일을 저장합니다. 예: `my-brand-llm-app-eds`

각 저장소의 [github.com/new](https://github.com/new)&#x200B;(으)로 이동합니다.

README, `.gitignore` 또는 라이선스를 사용하여 저장소를 초기화하지 마십시오. 플랫폼은 필요한 프로젝트 구조를 준비합니다.

>[!TIP]
>
>앱과 각 저장소의 용도를 식별하는 저장소 이름을 사용합니다. 이렇게 하면 앱 만들기 대화 상자에서 쉽게 인식할 수 있습니다.

## 앱 시작

1. [Adobe LLM 앱](https://experience.adobe.com/#/@llmapps/llm-apps/)을 열고 **[!UICONTROL 앱 만들기]**&#x200B;를 선택합니다.
2. **[!UICONTROL LLM 앱 이름]** 및 선택적 설명을 입력하십시오.
3. **[!UICONTROL Analytics 영역]**&#x200B;을 선택하세요.

   >[!IMPORTANT]
   >
   >앱을 만든 후에는 분석 영역을 변경할 수 없습니다.

4. **[!UICONTROL 내 앱 빌드]**&#x200B;에서 **[!UICONTROL 내 앱 자동 빌드]**&#x200B;를 선택합니다.
5. **[!UICONTROL 웹 사이트]**&#x200B;에서 `https://` 프로토콜을 포함한 웹 사이트 URL을 입력합니다. 플랫폼은 이 사이트를 분석하여 유용한 조치와 대표적인 샘플 결과를 결정한다.

![LLM 앱 만들기 — 앱 세부 정보 및 내 앱 빌드 사용](/help/assets/guide-onboarding-agent/app-details-onboarding.png)

## 저장소에 대한 [!DNL LLM Apps] 액세스 권한 부여

Adobe LLM 앱 [!DNL GitHub] 앱을 사용하면 [!DNL LLM Apps]에게 선택한 저장소에 액세스할 수 있습니다.

>[!NOTE]
>
>[!DNL GitHub] 조직에 연결하는 것은 일회성 설정입니다. 조직이 대화 상자에 이미 표시되어 있는 경우 다시 연결하는 대신 **[!UICONTROL GitHub에서 저장소 관리]**&#x200B;를 사용하십시오.

### 연결된 조직

저장소를 만들기 전에 Adobe LLM 앱 [!DNL GitHub] 앱이 이미 설치된 경우:

1. 연결된 조직을 선택합니다.
2. **[!UICONTROL GitHub에서 저장소 관리]**&#x200B;를 선택합니다.
3. 기존 [!DNL GitHub] 앱 설치에 두 저장소를 추가합니다.
4. [!DNL LLM Apps]&#x200B;(으)로 돌아가서 저장소 목록을 새로 고칩니다.

### 최초 연결만

조직이 대화 상자에 나타나지 않는 경우:

1. **[!UICONTROL GitHub 조직 연결]**&#x200B;을 선택합니다.
2. Adobe LLM 앱 [!DNL GitHub] 앱을 설치합니다.
3. **[!UICONTROL 저장소만 선택]**&#x200B;을 선택하고 두 개의 저장소를 선택합니다.
4. LLM 앱 만들기 대화 상자로 돌아갑니다.

[!DNL GitHub] 앱을 설치하거나 업데이트할 수 없는 경우 조직 관리자에게 문의하십시오.

## 저장소 선택

1. **[!UICONTROL Boilerplate 저장소]**&#x200B;에서 조직 및 빈 처리기 저장소를 선택합니다.
2. **[!UICONTROL EDS 저장소]**&#x200B;에서 조직 및 빈 EDS 저장소를 선택합니다.

   ![내 앱 빌드 — GitHub 조직, Boilerplate 저장소 및 EDS 저장소를 선택합니다](/help/assets/guide-onboarding-agent/repos-selected.png)

3. **[!UICONTROL 약관]**&#x200B;에서 **[!UICONTROL Adobe Developer 약관에 동의함]**&#x200B;을 확인하세요.
4. **[!UICONTROL 앱 만들기]**&#x200B;를 선택합니다.

## EDS 설정 완료

선택한 EDS 저장소가 비어 있으면 [!DNL LLM Apps]이(가) AEM 보일러판으로 초기화합니다. 그런 다음 대화 상자에서 앱을 다시 만들기 전에 AEM 코드 동기화를 설치하라는 메시지가 표시됩니다.

1. EDS 리포지토리 아래의 메시지에서 **[!UICONTROL AEM 코드 동기화 설치]**&#x200B;를 선택합니다.
2. [!DNL GitHub]에서 AEM 코드 동기화를 설치하고 EDS 저장소에 대한 액세스 권한을 부여합니다.
3. LLM 앱 만들기 대화 상자로 돌아갑니다.

![LLM 앱 만들기 — 빈 EDS 리포지토리가 초기화되었으며 AEM 코드 동기화가 필요합니다.](/help/assets/guide-onboarding-agent/install-aem-code-sync.png)

EDS 사이트의 관리자여야 합니다. 대화 상자에서 관리자가 아닌 것으로 보고하는 경우:

![LLM 앱 만들기 — EDS 관리자 액세스 필요](/help/assets/guide-onboarding-agent/eds-admin-required.png)

1. **[!UICONTROL AEM Live 관리자 열기]**&#x200B;를 선택합니다.
2. **[!UICONTROL + 사용자 추가]** 단추를 클릭하여 EDS 사이트의 관리자로 자신을 추가하십시오.

   ![LLM 앱 만들기 — EDS 관리자로 추가](/help/assets/guide-onboarding-agent/add-eds-admin.png)

3. [!DNL LLM Apps]&#x200B;(으)로 돌아가서 EDS 저장소를 새로 고치고 **[!UICONTROL 앱 만들기]**&#x200B;를 다시 선택합니다.

저장소 및 관리자 검사를 통과한 후 [!DNL LLM Apps]이(가) 앱을 만들고 작업 생성을 시작합니다.

## 작업 생성 대기

왼쪽에서 **[!UICONTROL 작업]** 페이지로 이동합니다. 에이전트가 웹 사이트를 분석하고 앱을 생성하는 동안 작업 페이지에 **대화 환경에 대한 작업 검색**&#x200B;이 표시됩니다. 생성에는 보통 약 15분이 소요됩니다. 이 페이지에서 나갔다가 나중에 다시 돌아올 수 있습니다.

![작업 — 권장 사항 생성](/help/assets/guide-onboarding-agent/actions-generating.png)

생성 중 [!DNL LLM Apps]:

1. 웹 사이트를 분석하고 유용한 고객 의도를 파악합니다.
2. 설명 및 입력 매개 변수를 포함한 작업 메타데이터를 만듭니다.
3. 처리기 저장소의 각 작업에 대한 처리기 및 테스트를 생성합니다.
4. EDS 저장소의 각 작업에 대한 EDS 위젯을 생성합니다.
5. 검토를 위해 작업을 준비합니다.

생성된 처리기는 처음에 웹 사이트에서 파생된 샘플 데이터를 사용합니다. 완벽한 경험을 보여 주지만 프로덕션 시스템에는 연결되지 않습니다.

## 생성된 작업 검토

생성이 완료되면 작업 페이지에 생성된 작업과 위젯 미리 보기가 표시됩니다. 각 작업에는 **[!UICONTROL AI 생성 작업이 있으며]** 배지를 검토해야 합니다.

![작업 — 검토 준비된 작업 생성됨](/help/assets/guide-onboarding-agent/actions-ready-for-review.png)

각 작업에 대해:

1. **[!UICONTROL 검토]**&#x200B;를 선택합니다.
2. 이름, 설명, 매개 변수, 주석, 생성된 핸들러 및 위젯을 검토합니다.
3. **[!UICONTROL 검토됨으로 표시]**&#x200B;를 선택합니다. 이렇게 하면 생성된 끌어오기 요청이 병합됩니다.
4. 작업 페이지로 돌아가서 나머지 작업에 대해 를 반복합니다.

![생성된 작업 — 검토됨으로 표시할 준비 완료](/help/assets/guide-onboarding-agent/generated-action-review.png)

모든 작업을 검토하면 **[!UICONTROL 앱 페이지로 이동]**&#x200B;을 선택합니다.

![작업 — 생성된 모든 작업을 검토함](/help/assets/guide-onboarding-agent/actions-reviewed.png)

>[!NOTE]
>
>생성된 코드는 사용자가 소유한 시작점입니다. 검토 후 작업 메타데이터, 핸들러, 테스트, 위젯 JavaScript 및 위젯 스타일을 변경할 수 있습니다.

## 앱 배포

1. 앱 세부 정보 페이지로 돌아갑니다.
2. **[!UICONTROL 배포]**&#x200B;를 선택하십시오.
3. **[!UICONTROL 단계]**&#x200B;을(를) 대상 환경으로 선택합니다.
4. **[!UICONTROL 배포]**&#x200B;를 선택하십시오.

![배포 — 스테이징 환경 선택](/help/assets/guide-onboarding-agent/deploy-stage.png)

[!DNL LLM Apps]이(가) 앱을 준비하고 빌드하고 게시하는 동안 잠시 기다려 주십시오.

![배포 — 배포 파이프라인 실행 중](/help/assets/guide-onboarding-agent/deploy-running.png)

![배포 — 스테이징 배포에 성공했습니다](/help/assets/guide-onboarding-agent/deploy-successful.png)

배포 후 **[!UICONTROL 앱 테스트]** 섹션에 스테이징 MCP 서버 URL이 표시됩니다. **[!UICONTROL URL 복사]**&#x200B;를 선택합니다.

![앱 세부 정보 — 스테이징 MCP 서버 URL 복사](/help/assets/guide-onboarding-agent/app-mcp-url.png)

## [!DNL ChatGPT]에서 테스트

스테이징 MCP 서버 URL을 사용하여 플러그인을 만들려면 [Test in ChatGPT](/help/guides/test-in-chatgpt.md)를 따르십시오.

생성된 작업 중 하나와 일치하는 질문을 합니다. 다음을 확인합니다.

- [!DNL ChatGPT]이(가) 필요한 작업을 선택합니다.
- 위젯은 필요한 샘플 데이터를 렌더링하고 포함합니다.
- 위젯 컨트롤은 예상되는 후속 동작을 생성합니다.
- 텍스트 응답은 결과를 정확하게 요약합니다.

![ChatGPT — 생성된 LLM 앱 플러그인 응답](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

이제 작동하는 엔드 투 엔드 스캐폴드를 보유하고 있습니다.

## 앱 제작 준비 완료

생성된 앱은 샘플 데이터를 사용합니다. 고객과 함께 사용하기 전에:

1. **시스템에 연결** — [생성된 각 처리기를 사용자 지정](/help/guides/customize-handler.md)하여 샘플 데이터를 API 또는 데이터 소스에 대한 호출로 바꿉니다.
2. **자격 증명 보호** — API URL 및 자격 증명을 소스 코드 또는 위젯 JavaScript에 저장하지 않고 관리되는 런타임 구성에 저장합니다.
3. **데이터 유효성 검사** — 작업 인수 및 API 응답을 확인하고, 요청 시간 초과를 추가하고, 안전 오류 메시지를 반환합니다.
4. **위젯을 업데이트** — 각 위젯을 해당 처리기의 `structuredContent`에 맞게 정렬한 다음 브랜딩 및 접근성 요구 사항을 적용합니다. [생성된 위젯 사용자 지정](/help/guides/widgets.md)을 참조하십시오.
5. **처리기를 테스트합니다** — 올바른 입력, 잘못된 입력, 빈 결과, API 오류 및 위젯에 필요한 데이터 셰이프를 포함합니다.
6. **스테이지에서 확인** — [!DNL ChatGPT] 플러그인을 통해 모든 작업을 다시 배포하고 테스트합니다.
7. **프로덕션에 배포** — 단계 테스트가 성공하면 프로덕션에 배포하고 프로덕션 MCP 서버 URL로 플러그인을 만들거나 업데이트합니다.

플랫폼에서 만들지 않은 기능을 추가하려면 [처음부터 작업 만들기](/help/guides/create-action.md)를 참조하십시오.

