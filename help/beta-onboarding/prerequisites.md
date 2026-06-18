---
title: Adobe LLM 앱 사전 요구 사항
description: Adobe LLM 앱 Beta 온보딩 세션 전에 설정해야 할 사항입니다.
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '539'
ht-degree: 2%

---


# Adobe LLM 앱 사전 요구 사항 {#prerequisites-for-adobe-llm-apps}

Adobe으로 온보딩 세션을 수행하기 전에 다음을 준비했는지 확인하십시오. 가능한 경우 아래 확인 단계를 실행합니다. 이 결과는 진행 여부가 아니라 회의실에 있어야 할 사람을 알려줍니다.

## Adobe Developer Console

Adobe IMS 조직에서 **개발자** 역할(또는 **시스템 관리자** 역할)을 가진 [Adobe Developer Console](https://developer.adobe.com/console)에 액세스해야 합니다. 조직에서 [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)에 액세스할 수 있는지 확인하십시오.

확인하려면 [developer.adobe.com/console](https://developer.adobe.com/console)&#x200B;(으)로 이동하십시오. 빠른 시작 화면이 표시되면 사용 권한이 올바르게 설정된 것입니다.

![Adobe Developer Console — 개발자 액세스를 확인하는 빠른 시작 화면](/help/assets/overview/dev-console-access-granted.png)

대신 **제한된 액세스** 메시지가 표시되면 개발자 역할이 없습니다. IMS 조직 관리자를 온보딩 세션에 초대합니다.

![Adobe Developer Console — 제한된 액세스 메시지](/help/assets/overview/dev-console-access-denied.png)

## [!DNL GitHub]

조직에 다음 권한이 있는 [!DNL GitHub] 계정이 필요합니다.

- **저장소 만들기** - 조직에서 응용 프로그램 코드와 EDS 프로젝트에 대해 각각 하나씩 두 개의 저장소를 만들어야 합니다. 확인하려면 [github.com/new](https://github.com/new)&#x200B;(으)로 이동하십시오. **소유자** 드롭다운에서 조직을 선택할 수 있는 경우 권한이 있습니다.

  ![조직 선택을 표시하는 GitHub 새 저장소 소유자 드롭다운](/help/assets/overview/github-repo-owner-dropdown.png)

- **앱 [!DNL GitHub]개 설치** — 조직에 앱 [!DNL GitHub]개를 설치하려면 적절한 권한이 필요합니다. [GitHub 앱 설치 요구 사항](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app)을 참조하세요.

**온보딩 세션 전에 사용 권한 확인**

Adobe을 만나기 전에 이 빠른 검사를 실행하십시오. 그 결과, 작업 진행 여부가 아닌 작업 공간에 있어야 하는 사람이 표시됩니다.

1. [github.com/new](https://github.com/new)&#x200B;(으)로 이동하여 조직을 소유자로 선택하고 `llm-apps-test`(이)라는 리포지토리를 만듭니다.
2. [Adobe LLM 앱 권한 검사기](https://github.com/apps/adobe-llm-apps-permission-checker/installations/new) 설치 페이지로 이동하여 `llm-apps-test` 저장소용 앱을 설치하세요.

| 결과 | 의미 | 작업 |
|---|---|---|
| 두 단계 모두 성공 | 필요한 권한이 있습니다. | 온보딩 세션을 수행할 준비가 되었습니다. |
| 2단계에서 **Install** 대신 **Request**&#x200B;이(가) 표시됨 | [!DNL GitHub] 앱을 설치할 권한이 없습니다. | 온보딩 모임에 [!DNL GitHub] 조직 관리자 초대 |

완료되면 `llm-apps-test` 저장소를 삭제하고 조직 설정에서 권한 검사기 앱을 제거하십시오.

## [!DNL Edge Delivery Services]&#x200B;(으)로 AEM Sites

작업 위젯은 **Adobe Experience Manager [!DNL Edge Delivery Services]&#x200B;(EDS)**&#x200B;에서 호스팅됩니다. 조직에 [!DNL Edge Delivery Services]을(를) 포함하는 AEM Sites 라이선스가 필요합니다. EDS 조직에 **관리자** 역할이 있어야 합니다.

확인하려면 [EDS 사용자 관리 도구](https://tools.aem.live/tools/user-admin/index.html)&#x200B;(으)로 이동하여 조직 이름을 입력하고 **사이트**&#x200B;를 비워 두고 **사용자 가져오기**&#x200B;를 클릭하세요. 목록에서 계정을 찾아 **관리자** 배지가 표시되는지 확인합니다.

![관리자 역할을 가진 사용자를 표시하는 EDS 사용자 관리 도구](/help/assets/overview/eds-user-admin.png)

아직 EDS 조직이 없는 경우 작업이 필요하지 않습니다. 온보딩 프로세스 중에 작업이 생성됩니다.

## LLM 플랫폼(테스트용)

배포된 앱을 테스트하려면 사용자 지정 MCP 앱과 **개발자 모드**&#x200B;를 사용할 수 있는 지원되는 구독 계층이 필요합니다. 예를 들어 [!DNL ChatGPT]에는 **Pro**, **Business** 또는 **Enterprise/Edu** 구독이 필요합니다.
