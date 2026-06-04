---
title: 앱 배포
description: LLM 앱 UI를 사용하여 Adobe LLM 앱을 스테이징 및 프로덕션에 배포하는 방법에 대해 알아봅니다.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '354'
ht-degree: 0%

---


# 앱 배포

>[!IMPORTANT]
>
>**면책조항:** [!DNL LLM Apps]의 베타 릴리스입니다. 여기에 표시된 기능, 워크플로우 및 UI가 반드시 애플리케이션 또는 제품의 최종 상태를 나타내지는 않습니다.

처리기 코드를 작성하여 연결된 리포지토리에 푸시하면 [!DNL LLM Apps] UI에서 앱을 배포할 수 있습니다.

## 배포 시작

앱 세부 정보 페이지로 이동합니다. 오른쪽 상단의 **[!UICONTROL 배포]** 단추를 클릭합니다.

![앱 세부 정보 — 배포 준비 완료](/help/assets/guide-deploy/app-detail-deploy-ready.png)

이렇게 하면 배포 대화 상자가 열립니다. 드롭다운에서 타겟 환경을 선택합니다.

![배포 대화 상자 — 대상 환경 선택](/help/assets/guide-deploy/deploy-pipeline-dropdown.png)

파이프라인을 시작하려면 **[!UICONTROL 배포]**&#x200B;를 클릭하십시오. 네 단계는 다음과 같습니다.

1. **자격 증명 수집** — 앱 메타데이터를 읽고 [!DNL GitHub] 토큰을 생성하며 콘솔 API에서 런타임 자격 증명을 가져옵니다.
2. **빌드 파이프라인 트리거** — 모든 매개 변수를 빌드 파이프라인으로 보냅니다.
3. **복제 및 빌드** - 파이프라인이 리포지토리를 복제하고, UI 메타데이터에서 `actions.json`을(를) 생성하고, `npm install` 및 Webpack을 실행하여 `dist/index.js`을(를) 생성합니다.
4. **런타임에 배포** — 번들을 앱의 [!DNL Adobe I/O Runtime] 네임스페이스에 배포합니다.

시작되면 파이프라인이 자동으로 실행되고 실시간 진행률이 표시됩니다.

![파이프라인 배포 실행 중](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

>[!NOTE]
>
>작업에 UI에 메타데이터가 있지만 저장소에 일치하는 핸들러 파일이 없는 경우 해당 작업은 계속 등록됩니다. 호출에서는 실제 코드를 추가할 때까지 기본 스텁 처리기를 사용합니다.

## 성공적인 배포 후

모든 단계가 완료되면 대화 상자에 배포된 URL 및 아티팩트 세부 정보와 함께 **배포 성공** 확인이 표시됩니다.

![배포 성공](/help/assets/guide-deploy/app-detail-deploy-finish.png)

대화 상자를 닫으려면 **닫기**&#x200B;를 클릭하십시오. 앱 세부 정보 페이지에서 **[!UICONTROL 앱 테스트]** 섹션으로 스크롤합니다.

![앱 테스트 — 배포된 URL](/help/assets/guide-deploy/test-app-deployed.png)

각 환경(**스테이징** 및 **프로덕션**)에 [!DNL Adobe I/O Runtime]의 MCP 서버 URL이 표시됩니다. 앱을 등록할 때 LLM 플랫폼에 제공하는 URL입니다. 클립보드에 복사하려면 **URL 복사**&#x200B;를 클릭하세요.

아래의 **배포 기록** 섹션은 환경에 걸쳐 모든 배포에 대한 전체 로그를 유지합니다.

![배포 기록](/help/assets/guide-deploy/deployment-history.png)

각 행에는 대상 **환경**(단계 또는 프로덕션), **상태**(성공 또는 실패) 및 **배포된 날짜**가 표시됩니다. 이 테이블을 사용하여 배포가 언제 발생했는지 추적하고 다음을 확인할 수 있습니다.
최신 배포에 성공했습니다.

