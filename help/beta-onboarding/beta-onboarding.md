---
title: Adobe LLM 앱용 Beta 온보딩
description: Adobe LLM Apps as a Beta 프로그램 참가자로 시작하십시오.
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '1551'
ht-degree: 0%

---


# Beta 온보딩 {#beta-onboarding}

>[!IMPORTANT]
>
>**면책조항:** [!DNL LLM Apps]의 베타 릴리스입니다. 여기에 표시된 기능, 워크플로우 및 UI가 반드시 애플리케이션 또는 제품의 최종 상태를 나타내지는 않습니다.

>[!NOTE]
>
>시작하기 전에 [필수 구성 요소](/help/beta-onboarding/prerequisites.md)가 모두 충족되었는지 확인하세요.

Beta 프로그램 참가자는 두 개의 zip 아카이브와 앱 구성 참조가 포함된 이메일을 받게 됩니다. 앱을 라이브로 다운로드하려면 아래 단계를 따르십시오.

## 시작하기에 앞서

단계로 이동하기 전에 이 안내서 전체에서 사용되는 주요 개념을 숙지하십시오. 그러면 시간을 절약하고 모든 것이 제대로 작동하는 데 도움이 됩니다.

**LLM 앱** - 사용자가 [!DNL ChatGPT] 또는 다른 LLM 플랫폼 내에서 상호 작용하는 브랜드 도우미입니다.

**작업** — 앱에서 제공하는 기능입니다. 예를 들어 &quot;배포자 찾기&quot; 또는 &quot;제품 찾아보기&quot;가 있습니다. 사용자가 관련 질문을 할 때 LLM에서 각 작업을 호출합니다.

**Action 처리기** — 작업을 호출할 때 실행되는 코드입니다. API를 호출하거나, 라이브 데이터를 가져오거나, 정적 데이터를 반환할 수 있습니다. Adobe에서 제공한 샘플 처리기는 실제 백엔드를 연결하기 전에 설치를 끝까지 확인할 수 있도록 하드코딩된 데이터를 반환합니다.

**위젯** - 사용자에게 표시되는 시각적 응답(카드, 회전 메뉴, 테이블 또는 LLM의 텍스트 회신과 함께 렌더링된 사용자 지정 UI).

**앱 구성 참조** — 앱을 설정할 때 각 작업에 대해 입력할 내용을 정확히 알려주는 Adobe의 파일입니다.


## 1단계: 제공된 아카이브를 [!DNL GitHub]에 푸시

Adobe은 이메일로 두 개의 zip 아카이브를 제공합니다.

- **응용 프로그램 코드**(`<project-name>.zip`) — [!DNL Adobe I/O Runtime]에서 실행되고 앱의 논리를 실행하는 작업 처리기입니다. 이러한 를 있는 그대로 배포하여 앱이 처음부터 끝까지 작동하도록 한 다음 나중에 업데이트하여 실제 백엔드를 연결합니다.
- **EDS 프로젝트**(`<project-name>-eds.zip`) — 위젯의 프론트엔드 코드입니다. Adobe은 사용자를 위해 사전 빌드했습니다. 이것은 브랜드에 맞게 소유, 사용자 지정 및 스타일을 하는 코드 베이스입니다.

[!DNL GitHub]에 **새 빈 저장소 두 개**&#x200B;를 만든 다음(보관 파일당 한 개) 각 보관 파일의 압축을 풀고 푸시합니다. 해당 zip 파일 뒤에 각 저장소 이름을 지정하는 것이 좋습니다(응용 프로그램 코드의 경우 `<project-name>`, EDS 프로젝트의 경우 `<project-name>-eds`).

`<your-github-org>`은(는) 개인 [!DNL GitHub] 사용자 이름 또는 [!DNL GitHub] 조직(저장소를 소유하는 계정)을 참조합니다.

**응용 프로그램 코드 저장소** — 보관 파일의 압축을 풀고 로컬 git 저장소를 초기화하고 [!DNL GitHub]에 푸시합니다.

```bash
# Unzip and enter the folder
unzip <project-name>.zip
cd <project-name>

# Initialize and push
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-repo>.git
git push -u origin main
```

**EDS 저장소** - EDS 보관에 대해 두 번째 저장소를 가리키도록 동일한 단계를 반복합니다.

```bash
unzip <project-name>-eds.zip
cd <project-name>-eds

git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-eds-repo>.git
git push -u origin main
```

## 2단계: LLM 앱 만들기

[experience.adobe.com/llm-apps/](https://experience.adobe.com/llm-apps/)&#x200B;(으)로 이동한 다음 **[!UICONTROL LLM 앱 만들기]**&#x200B;를 클릭합니다.

![앱 페이지 — 아직 만들어진 앱이 없습니다](/help/assets/guide-create-app/first-load.png)

앱 구성 참조의 **[!UICONTROL 앱 세부 정보]** 섹션에 있는 값을 사용하여 **[!UICONTROL 앱 세부 정보]**&#x200B;를 입력합니다.

- **[!UICONTROL LLM 앱 이름]**
- **[!UICONTROL LLM 앱 설명]**
- **[!UICONTROL 웹 사이트]**

![앱 만들기 대화 상자](/help/assets/guide-create-app/app-details-1.png)

**[!UICONTROL Analytics 데이터 영역]**&#x200B;에서 Analytics 데이터가 저장될 영역을 선택하십시오. 앱을 만든 후에는 이 **을(를) 변경할 수 없습니다**.

>[!IMPORTANT]
>
>앱을 만든 후에는 Analytics 데이터 영역을 변경할 수 없습니다.

![Analytics 데이터 영역 드롭다운](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

**저장소**&#x200B;에서 이전에 푸시한 [!DNL GitHub] 조직과 **응용 프로그램 코드 저장소**&#x200B;를 선택합니다.

>[!NOTE]
>
>앱을 처음 설정하는 경우 [!DNL GitHub] 조직이 아직 목록에 표시되지 않습니다. 조직을 연결하고 저장소에 대한 액세스 권한을 부여하려면 **[!UICONTROL 다른 GitHub 조직 연결]**&#x200B;을 클릭하십시오.

![앱 만들기 대화 상자 — 저장소 연결됨](/help/assets/guide-create-app/app-details-repo-linked.png)

**[!UICONTROL 웹 사이트에 따라 자동으로 작업 제안]**&#x200B;을 선택하지 않은 상태로 둡니다. 작업을 수동으로 구성합니다.

**[!UICONTROL Adobe Developer 약관]**&#x200B;에 동의한 다음 **[!UICONTROL 앱 만들기]**&#x200B;를 클릭합니다.

![앱 만들기 — 화면 로드](/help/assets/guide-create-app/app-loading.png)

![앱 세부 정보 페이지](/help/assets/guide-create-app/app-detail-top.png)


## 3단계: 위젯 라이브 가져오기

이 단계에서는 Adobe이 제공한 EDS 프로젝트를 설정하고 Adobe 작성 및 CDN 계층인 [!DNL DA.live]을(를) 통해 게시합니다. 게시된 각 문서는 작업이 호출될 때 사용자에게 표시되는 위젯이 됩니다.

### 3.1단계: EDS 저장소를 [!DNL DA.live]에 연결

1. [github.com/apps/aem-code-sync](https://github.com/apps/aem-code-sync)&#x200B;(으)로 이동합니다. 앱이 아직 설치되지 않은 경우 **[!UICONTROL 설치]**&#x200B;를 클릭합니다. 이미 설치되어 있는 경우 **[!UICONTROL 구성]**&#x200B;을 클릭하고 액세스할 수 있는 저장소 목록에 `<your-eds-repo>`을(를) 추가하십시오.
2. 설치 후 등록된 **[!DNL AEM Code Sync]** 확인 페이지에 도달합니다. **다음 단계→**&#x200B;에서 [!DNL DA.live] 링크를 클릭합니다.
3. **데모 콘텐츠** 화면에서 **없음**&#x200B;을 선택하고 **멋지게 만들기**&#x200B;를 클릭합니다.
4. 사이트의 [!DNL DA.live] 작성자 보기로 이동되었습니다.

### 3.2단계: 각 작업에 대해 [!DNL DA.live] 문서 만들기

[!DNL DA.live]에서 **작업당 하나의 문서를 만들어야**. 게시되면 해당 작업이 호출될 때 각 문서가 사용자에게 표시되는 위젯이 됩니다.

각 작업에 대해:

1. [!DNL DA.live]에서 사이트의 루트에 새 문서를 만들고 앱 구성 참조에 지정된 대로 이름을 지정합니다(**[!DNL DA.live]개의 문서** 섹션 참조).
2. 문서에서 왼쪽 사이드바를 사용하고 **[!UICONTROL 블록]**&#x200B;을 클릭하여 새 블록을 삽입합니다.
3. 블록 헤더를 앱 구성 참조에 지정된 블록 이름으로 설정합니다(**[!DNL DA.live]개의 문서** 섹션 참조).
4. **[!UICONTROL 게시]** 단추(상단 도구 모음의 종이 비행기 아이콘)를 사용하여 문서를 게시합니다.

게시되면 `https://main--<your-eds-repo>--<your-github-org>.aem.live/<document-name>`에서 각 문서에 액세스할 수 있습니다. 이 URL은 4단계에서 각 작업을 구성할 때 **[!UICONTROL 위젯 URL]** 필드에 입력하는 URL입니다.


### 3.3단계: EDS 사이트에 대한 CORS 헤더 구성

LLM 플랫폼에서 위젯을 원본 간에 로드하려면 EDS 사이트에 `Access-Control-Allow-Origin` 헤더를 추가해야 합니다.

[tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html)의 **HTTP 헤더 편집기**(으)로 이동합니다.

1. **조직**(`<your-github-org>`) 및 **사이트**(EDS 저장소 이름)를 입력하고 **[!UICONTROL 가져오기]**&#x200B;를 클릭합니다. 사이트에 대한 액세스를 인증하고 승인하라는 메시지가 표시됩니다.
2. `/**` 경로에서 **[!UICONTROL 헤더 추가]**&#x200B;를 클릭합니다.
3. 헤더 이름을 `Access-Control-Allow-Origin`(으)로 설정하고 값을 `*`(으)로 설정합니다.
4. **[!UICONTROL 저장]**&#x200B;을 클릭합니다.

[!DNL AEM Edge Delivery Services]의 사용자 지정 HTTP 헤더에 대한 전체 설명서는 [aem.live/docs/custom-headers](https://www.aem.live/docs/custom-headers)을(를) 참조하십시오.

헤더를 저장한 후 코드 동기화를 트리거하여 변경 사항을 모든 파일에 전파합니다.

```bash
curl -X POST "https://admin.hlx.page/code/<your-github-org>/<your-eds-repo>/main/*"
```


## 4단계: 작업 추가

[LLM 앱 UI](https://experience.adobe.com/llm-apps/)에서 앱을 열고 왼쪽 사이드바의 **[!UICONTROL 작업]**(으)로 이동합니다. 새 작업을 만들려면 **+**&#x200B;을(를) 클릭하십시오. 앱 구성 참조에 설명된 모든 작업에 대해 이 작업을 반복합니다(**작업 1**, **작업 2**, **작업 3** 섹션 참조).

![작업 페이지 — 아직 작업이 없습니다](/help/assets/guide-create-action/actions-empty.png)

### 작업 탭

- **작업 이름** 및 **설명** — LLM 플랫폼에서 작업을 호출할 시기를 결정하는 데 사용됩니다. 앱 구성 참조의 **작업 탭** 섹션에서 정확한 값을 사용합니다.
- **입력 매개 변수** — 각 매개 변수의 이름, 유형 및 설명. 앱 구성 참조에서 **작업 탭** 섹션의 값을 사용합니다.

![작업 만들기 — 기본 정보](/help/assets/guide-create-action/action-basic-info.png)

### 위젯 메타데이터 탭

- **유형** — **[!UICONTROL EDS]** 선택.

**[!UICONTROL CSP 구성]**&#x200B;을 확장하고 다음을 채우십시오.

- **[!UICONTROL CSP — 도메인 연결]** — 앱 구성 참조에서 **위젯 메타데이터 탭** 섹션의 값을 사용합니다.
- **[!UICONTROL CSP — 리소스 도메인]** — 앱 구성 참조에서 **위젯 메타데이터 탭** 섹션의 값을 사용합니다.

![작업 만들기 — 위젯 메타데이터](/help/assets/guide-create-action/widget-metadata.png)

![작업 만들기 — 권한 및 CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

### 위젯 빌더 탭

**[!UICONTROL 위젯 원본]**&#x200B;에서 **[!UICONTROL 기존 위젯 사용]**&#x200B;을 선택한 다음 다음을 채우십시오.

- **[!UICONTROL 스크립트 URL]** — 앱 구성 참조에 있는 **위젯 메타데이터 탭** 섹션의 값을 사용합니다.
- **[!UICONTROL 위젯 URL]** — 앱 구성 참조에서 **위젯 메타데이터 탭** 섹션의 값을 사용합니다.

**[!UICONTROL 작업 만들기]**&#x200B;를 클릭합니다. 작업이 **[!UICONTROL EDS]** 배지와 매개 변수 카운트가 있는 카드로 작업 페이지에 표시됩니다.

![작업 페이지 — 작업 생성됨](/help/assets/guide-create-action/actions-with-action.png)


## 5단계: 배포

모든 작업이 구성되면 앱 세부 정보 페이지로 이동하여 오른쪽 상단의 **[!UICONTROL 배포]**&#x200B;를 클릭합니다.

![앱 세부 정보 — 배포 준비 완료](/help/assets/guide-deploy/app-detail-deploy-ready.png)

대상 환경을 선택하고 **[!UICONTROL 배포]**&#x200B;를 클릭합니다. 파이프라인은 자격 증명 준비, 배포 시작, 저장소에서 앱 빌드, [!DNL Adobe I/O Runtime]에 게시의 네 단계를 통해 실행됩니다.

![파이프라인 배포 실행 중](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

완료되면 앱 세부 정보 페이지의 **[!UICONTROL 앱 테스트]** 섹션으로 스크롤하여 **[!UICONTROL MCP 서버 URL]**&#x200B;을(를) 복사합니다. [!DNL ChatGPT]에 앱을 등록하려면 이 URL이 필요합니다.

![배포 성공](/help/assets/guide-deploy/app-detail-deploy-finish.png)

![앱 테스트 — 배포된 URL](/help/assets/guide-deploy/test-app-deployed.png)


## 6단계: [!DNL ChatGPT]에 앱 추가

[!DNL ChatGPT]에 사용자 지정 앱을 추가하려면 **Pro**, **Business** 또는 **Enterprise** 구독이 필요합니다. 무료 및 플러스 플랜은 사용자 정의 MCP 앱을 지원하지 않습니다.

1. [!DNL ChatGPT]에서 프로필 아바타를 클릭하고 **[!UICONTROL 설정]**(으)로 이동합니다.

   ![ChatGPT — 설정 메뉴](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

2. 사이드바에서 **[!UICONTROL 앱]**&#x200B;을 선택하고 **[!UICONTROL 고급 설정]**&#x200B;을 클릭한 다음 **[!UICONTROL 개발자 모드]**&#x200B;를 사용하도록 설정합니다.

   ![ChatGPT — 개발자 모드 사용](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

3. **[!UICONTROL 설정] → [!UICONTROL 앱]**(으)로 이동한 다음 **[!UICONTROL 앱 만들기]**&#x200B;를 클릭합니다.

   ![ChatGPT — 앱 대화 상자 만들기](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

4. [!DNL LLM Apps]에서 복사한 **[!UICONTROL MCP 서버 URL]**&#x200B;을(를) 붙여 넣고 **[!UICONTROL 인증]**&#x200B;을(를) *인증 안 함*(으)로 설정하고 확인 확인란을 선택한 다음 **만들기**&#x200B;를 클릭합니다.

앱이 **[!UICONTROL 활성화된 앱]** 아래에 **[!UICONTROL 개발]** 배지와 함께 표시됩니다.

![ChatGPT — 앱 사용](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

새 대화를 시작하거나 **+** 단추를 사용하거나 **@**&#x200B;을(를) 입력하고 앱 이름을 입력하여 앱을 연결하고 구성된 작업 중 하나와 일치하는 질문을 합니다.

![ChatGPT — 메뉴에서 앱 선택](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

![ChatGPT — 작업 결과](/help/assets/guide-test-chatgpt/chatgpt-response.png)

## 다음 단계

배포한 샘플 앱은 하드코딩된 데이터를 사용합니다. 프로덕션 준비 환경으로 전환하려면 다음을 수행하십시오.

- **API 연결** — 응용 프로그램 코드 저장소의 작업 처리기를 업데이트하여 실제 API, 데이터베이스 또는 서비스를 호출합니다. 각 처리기는 `actions/<action-name>/index.js`에 있습니다.
- **위젯을 검토하고 세분화합니다** — EDS 프로젝트를 열고 브랜드에 맞게 블록 스타일과 레이아웃을 조정하며 위젯이 라이브 데이터로 올바르게 렌더링되는지 확인합니다.
- **재배포** — 처리기 및 위젯이 업데이트되면 변경 내용을 [!DNL GitHub]에 푸시하고 [!DNL LLM Apps] UI에서 **[!UICONTROL 배포]**&#x200B;를 클릭하여 새 버전을 게시합니다.
- **게시용으로 제출** - 경험이 만족스러우면 [!DNL ChatGPT] 플러그 인 또는 커넥터 게시 프로세스를 통해 검토용으로 앱을 제출하세요. Adobe은 이 프로세스를 제어하지 않습니다. 제출 요구 사항 및 타임라인은 LLM 플랫폼의 설명서를 참조하십시오.
