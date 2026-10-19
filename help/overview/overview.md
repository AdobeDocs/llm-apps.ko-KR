---
title: Adobe LLM 앱 개요
description: Adobe LLM 앱의 정의, 작동 방식 및 앱 빌드를 시작하는 방법에 대해 알아봅니다.
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '621'
ht-degree: 0%
---

# Adobe LLM 앱 - 개요 {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

## [!DNL Adobe LLM Apps]이란?

[!DNL Adobe LLM Apps]을(를) 사용하면 브랜드에서 [!DNL ChatGPT] 및 [!DNL Claude]과(와) 같은 지원되는 AI 지원 내에서 제품 검색, 가용성 확인 또는 서비스 예약과 같은 유용한 작업을 제공할 수 있습니다.

[!DNL LLM Apps]은(는) [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)에서 사용할 수 있습니다.

## [!DNL LLM Apps]&#x200B;(으)로 수행할 수 있는 작업

- **브랜드 소유 LLM 작업 만들기** — AI Assistant 내에서 활성화할 특정 비즈니스 흐름을 정의합니다(예: *테스트 드라이브 예약*, *제품 비교*, *서비스 예약*).
- **대화형 LLM 위젯 빌드** - [!DNL GitHub] 리포지토리에서 AEM 구성 요소로 관리되는 시각적 UI 구성 요소(제품 카드, 예약 양식, 스토어 로케이터)를 만듭니다.
- **중앙 집중식 브랜드 거버넌스 유지** - 작성자와 개발자가 AEM을 통해 관리되는 승인을 통해 LLM 플랫폼 내에 노출된 모든 콘텐츠, 복사 및 시각화에 대한 모든 권한을 유지합니다.
- **스테이징 및 프로덕션에 배포** - 제어된 배포 파이프라인을 사용하면 프로덕션으로 승격하기 전에 스테이징 환경에서 경험을 테스트할 수 있습니다.
- **작업 수준에서 가시성 제어** - 개별 작업을 활성화하거나 비활성화한 다음, 앱을 배포하여 변경 내용을 적용합니다.
- **의사 결정을 유도하는 요소 측정** — 내장된 분석(Adobe Customer Journey Analytics 제공) 표면 작업 트리거 카운트, 성공률, 포기 비율, 상위 사용자 프롬프트 및 가시성 점수.

## [!DNL LLM Apps]이(가) 중요한 이유

LLM 상호 작용은 기존의 검색과는 근본적으로 다르다. 평균 LLM 세션은 기존 검색 세션보다 4배 더 오래 지속됩니다. 소비자의 40% 이상이 복잡한 구매 결정을 위해 AI 도구에 의존하고 있다. [!DNL LLM Apps]이(가) 없으면 언급에서 승리할 수 있지만 고객을 잃을 수 있습니다. [!DNL LLM Apps]은(는) 사용자가 결정할 준비가 된 정확한 순간에 브랜드가 표시되기만 하는 것이 아니라 실행할 수 있도록 합니다.

## 주요 개념 {#key-concepts}

### LLM 앱

사용자가 [!DNL ChatGPT] 또는 다른 LLM 플랫폼 내에서 상호 작용하는 브랜드 도우미입니다. 모든 작업을 함께 그룹화하고 단일 단위로 배포합니다.

### 작업 {#actions}

앱에서 제공하는 기능(예: *배포자 찾기* 또는 *제품 찾아보기*). LLM 플랫폼은 요청이 해당 설명과 일치하면 작업을 호출합니다. [!DNL LLM Apps]에서 작업 메타데이터를 관리하고 [!DNL GitHub] 리포지토리에서 처리기 코드를 저장합니다.

### 작업 핸들러

LLM 플랫폼이 작업을 호출할 때 실행되는 서버측 함수입니다. 핸들러를 사용하여 입력의 유효성을 검사하고, API를 호출하고, 텍스트 및 구조화된 데이터를 반환합니다.

### 위젯 {#widgets-eds}

카드, 회전 메뉴 또는 표와 같이 LLM의 회신과 함께 표시되는 시각적 응답입니다. 생성된 위젯은 [!DNL Edge Delivery Services]&#x200B;(EDS) 저장소의 블록입니다. Adobe 관리 인프라를 사용하여 Adobe은 평가판을 위해 저장소를 준비하고 호스팅합니다. 또는 자체 저장소를 연결합니다.

### MCP 서버

배포 후 노출된 끝점입니다. 지원되는 LLM 플랫폼은 이 끝점에 연결하여 작업을 검색하고 호출합니다.

## 작동 방식

[!DNL LLM Apps]에서 브랜드의 기능을 정의합니다. 플랫폼은 이러한 기능을 동작으로 사용할 수 있도록 하며, AI 비서가 이 액션을 사용하여 대화에서 고객 요청에 응답합니다.

```
┌────────────────────┐          ┌────────────────────┐          ┌────────────────────┐
│     Your brand     │          │      LLM Apps      │          │    AI assistant    │
│                    │          │                    │          │                    │
│   What you offer   │   ───▶   │  Turns that into   │   ───▶   │ Answers with your  │
│  and how you help  │          │    something AI    │          │    brand, live     │
│  customers today   │          │     can act on     │          │  inside the chat   │
└────────────────────┘          └────────────────────┘          └────────────────────┘
```

빌드 방법 및 구성 요소가 함께 작동하는 방법에 대한 기술적인 세부 정보는 [앱이 함께 연결되는 방법](/help/guides/app-architecture.md)을 참조하십시오.

## 시작하기 {#choose-your-journey}

앱을 만들고 작업을 만드는 단계는 서로 다릅니다.

1. [인프라 설정](/help/guides/create-app.md). Adobe 관리 인프라를 사용하여 체험판을 만들거나 앱 만들기 마법사를 통해 빈 [!DNL GitHub] 저장소를 두 개 제공하십시오. 플랫폼은 작업 처리기에 대해 하나를 초기화하고 위젯에 대해 다른 하나를 초기화합니다.
2. [작업을 만듭니다](/help/guides/creating-actions.md). 플랫폼이 웹 사이트를 분석하고 작업을 생성하거나 직접 생성하도록 합니다.
3. [앱 배포 및 테스트](/help/guides/deploy-your-app.md). 생성된 작업을 검토하고, 스테이지로 배포하고, 지원되는 LLM 플랫폼에서 테스트합니다.

자동 생성은 샘플 비헤이비어의 시작점을 제공합니다. 고객이 앱을 사용할 수 있게 하기 전에 [처리기를 시스템에 연결](/help/guides/customize-handler.md)하고 [위젯을 사용자 지정](/help/guides/widgets.md)합니다.
