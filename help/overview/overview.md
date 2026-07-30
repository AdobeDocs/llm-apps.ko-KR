---
title: Adobe LLM 앱 개요
description: Adobe LLM 앱의 정의, 작동 방식 및 시작하는 데 필요한 사항에 대해 알아봅니다.
source-git-commit: 1d677c4e21963d1b126abb6287fccedfc1933c1a
workflow-type: tm+mt
source-wordcount: '938'
ht-degree: 1%

---


# Adobe LLM 앱 - 개요 {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

## [!DNL Adobe LLM Apps]이란?

[!DNL Adobe LLM Apps]을(를) 사용하면 브랜드에서 [!DNL ChatGPT]과(와) 같은 AI 지원 내에서 제품 검색, 가용성 확인 또는 서비스 예약과 같은 유용한 작업을 제공할 수 있습니다.

[!DNL LLM Apps]은(는) [experience.adobe.com](https://experience.adobe.com/#/@llmapps/llm-apps/)에서 사용할 수 있습니다.

## [!DNL LLM Apps]&#x200B;(으)로 수행할 수 있는 작업

- **브랜드 소유 LLM 작업 만들기** — AI Assistant 내에서 활성화할 특정 비즈니스 흐름을 정의합니다(예: *테스트 드라이브 예약*, *제품 비교*, *서비스 예약*).
- **대화형 LLM 위젯 빌드** - [!DNL GitHub] 리포지토리에서 AEM 구성 요소로 관리되는 시각적 UI 구성 요소(제품 카드, 예약 양식, 스토어 로케이터)를 만듭니다.
- **중앙 집중식 브랜드 거버넌스 유지** - 작성자와 개발자가 AEM을 통해 관리되는 승인을 통해 LLM 플랫폼 내에 노출된 모든 콘텐츠, 복사 및 시각화에 대한 모든 권한을 유지합니다.
- **스테이징 및 프로덕션에 배포** - 제어된 배포 파이프라인을 사용하면 프로덕션으로 승격하기 전에 스테이징 환경에서 경험을 테스트할 수 있습니다.
- **작업 수준에서 가시성 제어** - 배포 후 전체 앱을 다시 배포하지 않고 개별 작업을 켜거나 끌 수 있습니다.
- **의사 결정을 유도하는 요소 측정** — 내장된 분석(Adobe Customer Journey Analytics 제공) 표면 작업 트리거 카운트, 성공률, 포기 비율, 상위 사용자 프롬프트 및 가시성 점수.

## [!DNL LLM Apps]이(가) 중요한 이유

LLM 상호 작용은 기존의 검색과는 근본적으로 다르다. 평균 LLM 세션은 기존 검색 세션보다 4배 더 오래 지속됩니다. 소비자의 40% 이상이 복잡한 구매 결정을 위해 AI 도구에 의존하고 있다. [!DNL LLM Apps]이(가) 없으면 언급에서 승리할 수 있지만 고객을 잃을 수 있습니다. [!DNL LLM Apps]은(는) 사용자가 결정할 준비가 된 정확한 순간에 브랜드가 표시되기만 하는 것이 아니라 실행할 수 있도록 합니다.

## 주요 개념 {#key-concepts}

### LLM 앱

사용자가 [!DNL ChatGPT] 또는 다른 LLM 플랫폼 내에서 상호 작용하는 브랜드 도우미입니다. 모든 작업을 함께 그룹화하고 단일 단위로 배포합니다.

### 작업 {#actions}

앱에서 제공하는 기능(예: *배포자 찾기* 또는 *제품 찾아보기*). LLM 플랫폼은 요청이 해당 설명과 일치하면 작업을 호출합니다. 작업 메타데이터는 [!DNL LLM Apps]에서 관리되지만 해당 처리기는 [!DNL GitHub] 저장소의 코드입니다.

### 작업 핸들러

작업을 호출할 때 실행되는 서버측 함수입니다. 입력의 유효성을 검사하고, API를 호출하고, 텍스트 및 구조화된 데이터를 반환할 수 있습니다.

### 위젯 {#widgets-eds}

카드, 회전 메뉴 또는 표와 같이 LLM의 회신과 함께 표시되는 시각적 응답입니다. 생성된 위젯은 사용자가 소유한 [!DNL Edge Delivery Services]&#x200B;(EDS) 저장소의 블록입니다.

### MCP 서버

배포 후 노출된 끝점입니다. 지원되는 LLM 플랫폼은 이 끝점에 연결하여 작업을 검색하고 호출합니다.

## 작동 방식

아래 다이어그램은 UI에서 앱을 정의하는 것부터 LLM 플랫폼에서 결과를 라이브로 확인하는 것까지 각 조각이 어떻게 서로 연결되는지 보여 줍니다.

```
┌─────────────────────────────────────────────────────────────┐
│                      LLM Apps UI                            │
│  ┌──────────┐   ┌──────────┐   ┌───────────────────────┐    │
│  │   App    │──▶│ Actions  │──▶│ Metadata + Widget cfg │    │
│  └──────────┘   └──────────┘   └───────────┬───────────┘    │
└─────────────────────────────────────────── │ ────────────-──┘
                                             │ deploy
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Adobe I/O Runtime                          │
│               MCP Server (auto-generated)                   │
│  ┌───────────────┐ ┌──────────────────┐ ┌───────────────┐   │
│  │ search-       │ │ get-product-     │ │ find-where-   │   │
│  │ products      │ │ details          │ │ to-buy        │   │
│  └───────────────┘ └──────────────────┘ └───────────────┘   │
└──────────────────────────────┬──────────────────────────────┘
                               │ MCP protocol
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                        ChatGPT                              │
│  Conversation                                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  EDS Widget                                           │  │
│  │  Product carousel, store locator, detail card ...     │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## 요구 사항 {#requirements}

앱을 만들기 전에 다음 요구 사항을 모두 완료하십시오.

### Adobe Developer Console

Adobe IMS 조직은 [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)에 액세스할 수 있어야 합니다. **개발자** 또는 **시스템 관리자** 역할이 필요합니다.

액세스 권한을 확인하려면 [Adobe Developer Console](https://developer.adobe.com/console)을 여세요. 빠른 시작 화면에서 필요한 액세스 권한이 있음을 확인합니다.

![Adobe Developer Console — 개발자 액세스를 확인하는 빠른 시작 화면](/help/assets/overview/dev-console-access-granted.png)

**제한된 액세스**&#x200B;가 표시되면 IMS 조직 관리자에게 연락하여 개발자 역할을 요청하세요.

![Adobe Developer Console — 제한된 액세스 메시지](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

**can**&#x200B;에서 다음을 수행하는 [!DNL GitHub] 계정이 필요합니다. 권한 확인입니다. 아직 아무 것도 설치하지 마십시오.

- 앱을 소유할 계정 또는 조직에서 두 개의 저장소를 만듭니다.
- [!DNL GitHub] 앱을 나중에 설치 프로세스에서 설치하거나 승인할 수 있는 조직 관리자가 있습니다.

저장소 생성 액세스를 확인하려면 [github.com/new](https://github.com/new)을(를) 열고 의도한 계정이나 조직이 **소유자**&#x200B;에 표시되는지 확인하십시오.

![GitHub — 저장소 소유자 선택](/help/assets/overview/github-repo-owner-dropdown.png)

조직 소유 저장소의 경우 조직 관리자가 [!DNL GitHub] 앱을 승인해야 할 수 있습니다.

>[!NOTE]
>
>이는 설정 단계가 아닌 권한 확인입니다. 아직 [!DNL GitHub] 앱을 설치하지 마십시오. [첫 번째 앱을 자동으로 만들기](/help/guides/create-app.md)에서는 각 앱을 설치하는 과정을 안내합니다. 필요한 시점에 만든 정확한 저장소 범위를 지정합니다.

### 웹 사이트

앱이 지원해야 하는 제품, 서비스 또는 작업을 나타내는 공개 HTTPS 웹 사이트가 필요합니다. 플랫폼은 이 웹사이트를 분석해 액션을 제안하고 대표 샘플 데이터를 생성한다.

기밀 또는 액세스 제어 정보를 노출하는 웹 사이트를 사용하지 마십시오.

### 테스트용 [!DNL ChatGPT] 또는 [!DNL Claude]

시작 자습서를 완료하려면 개발자 모드가 활성화된 지원되는 [!DNL ChatGPT] 계획 또는 사용자 지정 커넥터가 활성화된 지원되는 [!DNL Claude] 계획을 사용하십시오. Workspace 또는 조직 관리자가 액세스를 제한할 수 있습니다. [Test in ChatGPT](/help/guides/test-in-chatgpt.md#plan-requirements) 또는 [Test in Cloud](/help/guides/test-in-claude.md#plan-requirements)를 참조하십시오.

## 여정 선택 {#choose-your-journey}

### &#x200B;1. 첫 번째 앱 빌드 및 실행

[첫 번째 앱을 빌드하고 시작](/help/guides/create-app.md)합니다. 이 여정은 빈 저장소 2개로 시작하여 [!DNL ChatGPT]과(와) 같은 지원되는 LLM 플랫폼에서 플러그인으로 테스트된 프로덕션 준비 앱으로 끝납니다.

### &#x200B;2. 생성된 앱 사용자 지정

플랫폼이 앱을 자동으로 만들고 샘플 동작을 바꾸려는 경우 이 여정을 선택합니다.

1. [생성된 처리기를 사용자 지정](/help/guides/customize-handler.md)하여 API를 연결하고 각 작업에서 반환되는 데이터를 정의합니다.
2. 해당 데이터를 사용하고 상호 작용과 디자인을 적용하려면 [생성된 위젯을 사용자 지정](/help/guides/widgets.md)합니다.

### &#x200B;3. 처음부터 새 작업 추가

[새 작업을 처음부터 추가](/help/guides/create-action.md)를 선택하여 새 메타데이터를 정의하고, 핸들러를 작성하고, 위젯을 연결하고, 작업을 테스트하고, 배포합니다.

### &#x200B;4. 기존 EDS 프로젝트 연결

이미 EDS 사이트가 있거나 앱을 자동으로 빌드하지 않은 경우 [기존 EDS 프로젝트 연결](/help/guides/bring-your-own-eds.md)을 선택합니다.

모든 여정은 공유 [배포](/help/guides/deploy-your-app.md) 단계를 사용한 다음 [ChatGPT 플러그인 테스트](/help/guides/test-in-chatgpt.md) 또는 [클라우드 커넥터 테스트](/help/guides/test-in-claude.md)를 사용합니다.

