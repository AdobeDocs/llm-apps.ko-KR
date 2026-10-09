---
title: 앱 배포
description: LLM 앱 UI를 사용하여 Adobe LLM 앱을 스테이징 및 프로덕션에 배포하는 방법에 대해 알아봅니다.
source-git-commit: 4e447562c5d38f68c209ded7370e9d384a7c9701
workflow-type: tm+mt
source-wordcount: '352'
ht-degree: 0%
---

# 앱 배포 {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

처리기 코드를 작성하여 연결된 리포지토리에 푸시하면 [!DNL LLM Apps] UI에서 앱을 배포할 수 있습니다.

모든 여정에 대해 공유된 단계입니다. 배포 후 [ChatGPT 플러그 인을 테스트하거나](/help/guides/test-in-chatgpt.md) [Cloud 커넥터를 테스트합니다](/help/guides/test-in-claude.md).

## 배포 시작

앱 세부 정보 페이지를 열고 **[!UICONTROL 배포]**&#x200B;를 선택합니다.

대상 환경을 선택한 다음 **[!UICONTROL 배포]**&#x200B;를 선택하십시오.

핸들러가 [앱 변수](/help/guides/app-variables.md)를 사용하는 경우 배포하기 전에 대상 환경에 대해 처리기를 구성하십시오. 추가, 업데이트 또는 삭제된 변수는 이 배포에 적용됩니다. 단계 및 프로덕션은 독립적인 값을 갖습니다.

![배포 — 대상 환경 선택](/help/assets/guide-onboarding-agent/deploy-stage.png)

배포는 다음 네 단계를 실행합니다.

1. **준비 중** — 앱을 배포하는 데 필요한 구성을 검색합니다.
2. **배포 시작** — 백그라운드 배포 프로세스를 시작합니다.
3. **앱 빌드** — 종속성을 설치하고 최신 저장소 코드를 빌드합니다.
4. **게시** — [!DNL Adobe I/O Runtime]에 앱을 게시합니다.

![배포 — 배포 파이프라인 실행 중](/help/assets/guide-onboarding-agent/deploy-running.png)

>[!NOTE]
>
>작업에 UI에 메타데이터가 있지만 저장소에 일치하는 핸들러 파일이 없는 경우 해당 작업은 계속 등록됩니다. 호출에서는 실제 코드를 추가할 때까지 기본 스텁 처리기를 사용합니다.

## 성공적인 배포 후

모든 단계가 완료되면 대화 상자에 **배포가 성공함**&#x200B;이 표시됩니다.

![배포 — 배포 성공](/help/assets/guide-onboarding-agent/deploy-successful.png)

대화 상자를 닫으려면 **닫기**&#x200B;를 클릭하십시오. 앱 세부 정보 페이지에서 **[!UICONTROL 앱 테스트]** 섹션으로 스크롤합니다.

![앱 세부 정보 — MCP 서버 URL 복사](/help/assets/guide-onboarding-agent/app-mcp-url.png)

배포된 각 환경에는 MCP 서버 URL이 표시됩니다. **[!UICONTROL URL 복사]**&#x200B;를 선택하고 이를 사용하여 대상 LLM 플랫폼에서 플러그인을 만드십시오.

**배포 기록** 섹션에는 마지막 10개의 배포가 표시됩니다.

![배포 기록](/help/assets/guide-deploy/deployment-history.png)

각 행에는 대상 **환경**(단계 또는 프로덕션), **상태**(성공 또는 실패) 및 **배포된 날짜**&#x200B;가 표시됩니다. 이 테이블을 사용하여 배포가 언제 발생했는지 추적하고 다음을 확인할 수 있습니다.
최신 배포에 성공했습니다.

## 다음 단계

- [배포된 앱을 ChatGPT 플러그 인으로 테스트합니다](/help/guides/test-in-chatgpt.md).
- [배포된 앱을 클라우드 커넥터로 테스트합니다](/help/guides/test-in-claude.md).
