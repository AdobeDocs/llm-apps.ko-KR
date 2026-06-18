---
title: 앱 만들기
description: 첫 번째 LLM 앱을 만들어 GitHub 저장소에 연결하는 방법에 대해 알아봅니다.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '745'
ht-degree: 0%

---


# 앱 만들기

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

>[!NOTE]
>
>**Beta 프로그램 참가자**&#x200B;인 경우 대신 [Beta 온보딩 가이드](/help/beta-onboarding/beta-onboarding.md)를 사용하십시오. 이 가이드는 특정 앱에 대한 전체 설정을 포괄합니다.

>[!NOTE]
>
>시작하기 전에 [필수 구성 요소](/help/overview/overview.md#prerequisites)가 모두 충족되었는지 확인하세요.

이 안내서에서는 빈 상태에서 [!DNL GitHub] 리포지토리에 연결된 완전히 구성된 프로젝트까지 첫 번째 [!DNL Adobe LLM Apps]을(를) 만드는 과정을 안내합니다.

## [!DNL LLM Apps] 열기

[experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)&#x200B;(으)로 이동합니다. 앱을 아직 만들지 않은 경우 첫 번째 앱을 만들라는 메시지가 표시된 첫 번째 로드 페이지가 표시됩니다.

![앱 페이지 — 아직 만들어진 앱이 없습니다](/help/assets/guide-create-app/first-load.png)

왼쪽 사이드바를 통해 **[!UICONTROL 앱]**&#x200B;에서 **[!UICONTROL 작업]** 사이를 이동할 수 있습니다. 시작하려면 **[!UICONTROL 앱 만들기]**&#x200B;를 클릭하세요.

## 앱 세부 정보 입력

앱 만들기 대화 상자가 전체 화면으로 열립니다.

![앱 만들기 대화 상자](/help/assets/guide-create-app/app-details-1.png)

다음을 입력하십시오.

- **[!UICONTROL LLM 앱 이름]**(필수) - 앱의 표시 이름입니다. 문자, 숫자 및 공백만 허용됩니다.
- **[!UICONTROL LLM 앱 설명]** - 앱의 기능에 대한 간략한 설명입니다. 예를들어, *사용자가 LLM 플랫폼을 통해 제품을 검색하고 서비스를 예약할 수 있도록 지원합니다*.
- **[!UICONTROL 웹 사이트]**(필수) - 브랜드 웹 사이트의 URL. [!DNL LLM Apps]은(는) 이 옵션을 사용하여 미리 구성된 작업을 자동으로 만듭니다.

## 분석 데이터 영역 선택

이 앱에 대한 분석 데이터를 저장할 지역을 선택하십시오.

>[!IMPORTANT]
>
>앱을 만든 후에는 Analytics 데이터 영역을 변경할 수 없습니다.

![Analytics 데이터 영역 드롭다운](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

**Analytics 지역** 드롭다운의 기본값은 **미국(US)**&#x200B;입니다. 사용 가능한 옵션은 **미국(US)** 및 **유럽(EU)**&#x200B;입니다. 계속하기 전에 데이터 상주 요구 사항과 가장 적합한 지역을 선택하십시오.

## [!DNL GitHub] 리포지토리 연결

앱 세부 정보 아래에서 [!DNL GitHub] 리포지토리를 연결할 수 있습니다. 이 리포지토리는 작업 처리기 코드가 있는 곳입니다. LLM 플랫폼이 앱을 호출할 때 [!DNL Adobe I/O Runtime]에서 실행되는 `actions/` 폴더 아래에 JavaScript이 작동합니다.

처음이라면 목록에 저장소가 나타나지 않습니다. 조직에 **[!DNL Adobe LLM Apps Link]** [!DNL GitHub] 앱을 설치해야 합니다.

1. 대화 상자 하단의 **Github에서 저장소 관리**&#x200B;를 클릭합니다.
2. 새 탭에서 [!DNL Adobe LLM Apps Link] [!DNL GitHub] 앱 페이지가 열립니다.

   ![Adobe LLM 앱 링크 - GitHub 앱 설치 페이지](/help/assets/guide-create-app/github-app-install.png)

3. **[!UICONTROL 설치]**&#x200B;를 클릭하고 [!DNL GitHub] 조직을 선택합니다.
4. **[!UICONTROL 저장소 액세스]**&#x200B;에서 **저장소만 선택**&#x200B;을(를) 선택하고 앱 코드를 호스트할 저장소를 선택합니다.

   ![Adobe LLM 앱 링크 — 저장소 액세스](/help/assets/guide-create-app/github-repo-access.png)

5. **[!UICONTROL 저장]**&#x200B;을 클릭합니다. 앱 만들기 대화 상자로 돌아가기 — 이제 저장소가 **저장소 선택** 드롭다운에 나타납니다.
6. 사용할 저장소를 선택합니다.

![앱 만들기 대화 상자 — 저장소 연결됨](/help/assets/guide-create-app/app-details-repo-linked.png)

>[!NOTE]
>
>앱 생성 중에 저장소 연결을 건너뛰고 나중에 앱 설정에서 해당 연결을 수행할 수 있습니다. 그러나 저장소가 연결될 때까지 배포할 수 없습니다.

## 앱 만들기

**[!UICONTROL 앱 만들기]**&#x200B;를 클릭합니다. Developer Console에서 프로젝트를 만드는 동안 로드 화면이 표시됩니다.

![앱 만들기 — 화면 로드](/help/assets/guide-create-app/app-loading.png)

완료되면 **앱 세부 정보** 페이지로 리디렉션됩니다.

## 앱 세부 정보 페이지

앱 세부 정보 페이지는 앱을 관리하기 위한 중앙 허브입니다.

![앱 세부 정보 페이지 — 상위 섹션](/help/assets/guide-create-app/app-detail-top.png)

### 앱 배너

![앱 배너](/help/assets/guide-create-app/app-banner.png)

맨 위에 있는 색상 배너에는 앱 아바타, 이름, 설명 및 앱 간 전환을 위한 드롭다운을 포함하여 현재 선택한 앱이 표시됩니다. 스크롤할 때 배너는 상단에 고정된 상태를 유지합니다.

### 페이지 제목 및 작업

![앱 배너](/help/assets/guide-create-app/page-title.png)

배너 아래에 다음 작업 버튼이 있는 앱 이름이 머리글로 표시됩니다.

- **..**(추가 작업) — 새 앱을 만들거나 현재 앱을 삭제합니다.
- **[!UICONTROL 설정]** — 연결된 저장소 및 기타 옵션을 구성합니다.
- **[!UICONTROL 배포]** — [!DNL Adobe I/O Runtime]에 앱을 배포합니다(리포지토리가 링크될 때까지 사용 안 함).

### 앱 정보 카드

![앱 정보 카드](/help/assets/guide-create-app/app-info-card.png)

이 카드는 앱의 주요 메타데이터(이름, 설명, 상태 배지(**배포되지 않음** 또는 **배포됨**), 앱 ID 및 생성 날짜)를 요약합니다. 또한 두 개의 연결된 저장소가 표시됩니다.

- **처리기 리포지토리** — 작업 처리기 코드가 있는 위치입니다([!DNL Adobe I/O Runtime]의 JavaScript 함수).
- **EDS 저장소** — 위젯 UI가 있는 위치([!DNL Edge Delivery Services]에서 제공하는 블록 및 스타일).

### 작업, 앱 테스트 및 배포 내역

![앱 세부 정보 페이지 — 하위 섹션](/help/assets/guide-create-app/app-detail-bottom.png)

정보 카드 아래에는 세 개의 섹션이 있습니다.

- **[!UICONTROL 작업]** — 앱에 대해 정의된 작업 처리기를 나열합니다. **작업으로 이동**&#x200B;을 클릭하여 작업 페이지로 이동합니다.
- **[!UICONTROL 앱을 테스트합니다]** — 배포 후 스테이징 및 프로덕션 환경에 대한 MCP 서버 URL을 표시합니다.
- **배포 기록** - 상태 및 날짜가 있는 환경에서 모든 배포를 추적합니다.

## 다음 단계

- [안내서: 동작 만들기](/help/guides/create-action.md) — 메타데이터 및 위젯 설정을 사용하여 동작을 정의합니다.

