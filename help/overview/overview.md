---
title: Adobe LLM 앱 개요
description: Adobe LLM 앱의 정의, 작동 방식 및 시작하는 데 필요한 사항에 대해 알아봅니다.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '873'
ht-degree: 1%

---


# Adobe LLM 앱 - 개요 {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

## [!DNL Adobe LLM Apps]이란?

[!DNL Adobe LLM Apps]을(를) 사용하면 브랜드에서 제품 검색, 가용성 확인 또는 서비스 예약과 같은 주요 작업을 [!DNL ChatGPT] 또는 Cloud와 같은 AI 지원 내에서 직접 노출할 수 있습니다. 브랜드는 AI가 생성한 답변에 소극적으로 언급되는 대신 고객이 대화에서 나가지 않아도 실제 비즈니스 흐름을 안내할 수 있다.

[!DNL LLM Apps]은(는) [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)에서 사용할 수 있습니다.

## [!DNL LLM Apps]&#x200B;(으)로 수행할 수 있는 작업

- **브랜드 소유 LLM 작업 만들기** — AI Assistant 내에서 활성화할 특정 비즈니스 흐름을 정의합니다(예: *테스트 드라이브 예약*, *제품 비교*, *서비스 예약*).
- **대화형 LLM 위젯 빌드** - [!DNL GitHub] 리포지토리에서 AEM 구성 요소로 관리되는 시각적 UI 구성 요소(제품 카드, 예약 양식, 스토어 로케이터)를 만듭니다.
- **중앙 집중식 브랜드 거버넌스 유지** - 작성자와 개발자가 AEM을 통해 관리되는 승인을 통해 LLM 플랫폼 내에 노출된 모든 콘텐츠, 복사 및 시각화에 대한 모든 권한을 유지합니다.
- **스테이징 및 프로덕션에 배포** - 제어된 배포 파이프라인을 사용하면 프로덕션으로 승격하기 전에 스테이징 환경에서 경험을 테스트할 수 있습니다.
- **작업 수준에서 가시성 제어** - 배포 후 전체 앱을 다시 배포하지 않고 개별 작업을 켜거나 끌 수 있습니다.
- **의사 결정을 유도하는 요소 측정** — 내장된 분석(Adobe Customer Journey Analytics 제공) 표면 작업 트리거 카운트, 성공률, 포기 비율, 상위 사용자 프롬프트 및 가시성 점수.

## [!DNL LLM Apps]이(가) 중요한 이유

LLM 상호 작용은 기존의 검색과는 근본적으로 다르다. 평균 [!DNL ChatGPT] 세션은 기존 검색 세션보다 4배 더 오래 지속됩니다. 소비자의 40% 이상이 복잡한 구매 결정을 위해 AI 도구에 의존하고 있다. [!DNL LLM Apps]이(가) 없으면 언급에서 승리할 수 있지만 고객을 잃을 수 있습니다. [!DNL LLM Apps]은(는) 사용자가 결정할 준비가 된 정확한 순간에 브랜드가 표시되기만 하는 것이 아니라 실행할 수 있도록 합니다.

## 주요 개념

**LLM 앱** - 사용자가 [!DNL ChatGPT] 또는 다른 LLM 플랫폼 내에서 상호 작용하는 브랜드 도우미입니다. 모든 작업을 함께 그룹화하고 단일 단위로 배포합니다.

**작업** — 앱에서 제공하는 기능입니다. 예를 들어 &quot;배포자 찾기&quot; 또는 &quot;제품 찾아보기&quot;가 있습니다. 사용자가 관련 질문을 할 때 LLM에서 각 작업을 호출합니다. 모든 작업에는 [!DNL LLM Apps] UI에서 관리되는 메타데이터(이름, 설명, 매개 변수)와 [!DNL GitHub]의 처리기(사용자 코드)의 두 부분이 있습니다.

**Action 처리기** — 작업을 호출할 때 실행되는 코드입니다. API를 호출하거나, 라이브 데이터를 가져오거나, 정적 데이터를 반환할 수 있습니다. 처리기는 `actions/<name>/index.js`의 [!DNL GitHub] 리포지토리에 있습니다.

**위젯** - 사용자에게 표시되는 시각적 응답(카드, 회전 메뉴, 테이블 또는 LLM의 텍스트 회신과 함께 렌더링된 사용자 지정 UI). 위젯은 [!DNL Edge Delivery Services]&#x200B;(EDS) 사이트에서 호스팅되는 HTML 페이지입니다.

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

## 사전 요구 사항

### Adobe Developer Console

Adobe IMS 조직에서 **개발자** 역할(또는 **시스템 관리자** 역할)을 가진 [Adobe Developer Console](https://developer.adobe.com/console)에 액세스해야 합니다. 조직에서 [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)에 액세스할 수 있는지 확인하십시오.

확인하려면 [developer.adobe.com/console](https://developer.adobe.com/console)&#x200B;(으)로 이동하십시오. 빠른 시작 화면이 표시되면 사용 권한이 올바르게 설정된 것입니다.

![Adobe Developer Console — 개발자 액세스를 확인하는 빠른 시작 화면](/help/assets/overview/dev-console-access-granted.png)

대신 **제한된 액세스** 메시지가 표시되면 개발자 역할이 없습니다. 액세스 권한을 요청하려면 IMS 조직 관리자에게 문의하십시오.

![Adobe Developer Console — 제한된 액세스 메시지](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

조직에 다음 권한이 있는 [!DNL GitHub] 계정이 필요합니다.

- **저장소 만들기** - 조직에서 응용 프로그램 코드와 EDS 프로젝트에 대해 각각 하나씩 두 개의 저장소를 만들어야 합니다. 확인하려면 [github.com/new](https://github.com/new)&#x200B;(으)로 이동하십시오. **소유자** 드롭다운에서 조직을 선택할 수 있는 경우 권한이 있습니다.

  ![조직 선택을 표시하는 GitHub 새 저장소 소유자 드롭다운](/help/assets/overview/github-repo-owner-dropdown.png)

- **앱 [!DNL GitHub]개 설치** — 조직에 앱 [!DNL GitHub]개를 설치하려면 적절한 권한이 필요합니다. [GitHub 앱 설치 요구 사항](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app)을 참조하세요.

### [!DNL Edge Delivery Services]&#x200B;(으)로 AEM Sites

작업 위젯은 **Adobe Experience Manager [!DNL Edge Delivery Services]&#x200B;(EDS)**&#x200B;에서 호스팅됩니다. 조직에 [!DNL Edge Delivery Services]을(를) 포함하는 AEM Sites 라이선스가 필요합니다. EDS 조직에 **관리자** 역할이 있어야 합니다.

확인하려면 [EDS 사용자 관리 도구](https://tools.aem.live/tools/user-admin/index.html)&#x200B;(으)로 이동하여 조직 이름을 입력하고 **사이트**&#x200B;를 비워 두고 **사용자 가져오기**&#x200B;를 클릭하세요. 목록에서 계정을 찾아 **관리자** 배지가 표시되는지 확인합니다.

![관리자 역할을 가진 사용자를 표시하는 EDS 사용자 관리 도구](/help/assets/overview/eds-user-admin.png)

### LLM 플랫폼(테스트용)

배포된 앱을 테스트하려면 사용자 지정 MCP 앱과 **개발자 모드**&#x200B;를 사용할 수 있는 지원되는 구독 계층이 필요합니다. 예를 들어 [!DNL ChatGPT]에는 **Pro**, **Business** 또는 **Enterprise/Edu** 구독이 필요합니다.

## 시작하기

상황에 맞는 경로를 선택하십시오.

| | **Beta 참가자** | **일반 가용성** |
|---|---|---|
| **다음을 보유하고 있음** | Beta 프로그램에 참여하고 Adobe에서 애플리케이션 코드 아카이브, EDS 프로젝트 아카이브 및 앱 구성 참조를 받았습니다 | 염두에 둔 사용 사례 — Adobe은 앱 빌드 및 배포를 안내합니다 |
| **여기에서 시작** | [Beta 온보딩](/help/beta-onboarding/beta-onboarding.md) | [앱 만들기](/help/guides/create-app.md) |

