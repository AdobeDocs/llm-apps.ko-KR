---
title: 기존 앱 업그레이드
description: Adobe Analytics 또는 Customer Journey Analytics을 활성화하기 전에 이전 Adobe LLM 앱의 런타임과 EDS SDK을 업데이트합니다.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '793'
ht-degree: 0%
---

# 기존 앱 업그레이드 {#upgrade-existing-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

앱에서 `@adobe/llm-apps-runtime`을(를) 1.2.0 이전 버전에서 사용하거나 EDS 위젯에서 LLM 앱 SDK을 0.1.13 이전 버전에서 사용하는 경우에만 이 안내서를 사용하십시오. 이미 두 요구 사항을 모두 충족하는 앱은 [Adobe Analytics 및 Customer Journey Analytics 설정](/help/guides/analytics-setup.md)(으)로 바로 진행할 수 있습니다.

**이 안내서에서:** 이전 앱의 런타임과 EDS SDK을 업데이트하고 확인하는 방법을 알아봅니다. 그런 다음 Analytics 설정 안내서로 돌아가서 Analytics를 활성화하고 컬렉션을 확인합니다.

## 시작하기에 앞서 {#before-you-begin}

핸들러 저장소 및 EDS 위젯이 있는 앱의 경우 앱에 연결된 EDS 저장소에 액세스해야 합니다. 앱에서 Adobe 관리 인프라를 사용하는 경우 업데이트하기 전에 코드 액세스를 요청합니다. [Adobe 관리 인프라 사용](/help/guides/create-app.md#adobe-managed-infrastructure)을 참조하세요.

다른 앱을 만들 필요는 없습니다. 지원되는 버전 아래의 구성 요소만 업데이트합니다. [분석 설정 요구 사항](/help/guides/analytics-setup.md#setup-checklist)을 충족하는 기존 Adobe 구성을 다시 사용할 수 있습니다.

EDS 위젯이 있는 앱의 경우 먼저 SDK 업데이트를 게시하고 확인한 다음 런타임 업데이트를 병합하고 앱을 재배포합니다. 두 저장소 업데이트는 개별적으로 준비할 수 있지만 분석 사용 배포를 테스트하기 전에 두 저장소 업데이트를 모두 사용할 수 있어야 합니다.

## LLM 앱 작업 저장소 확인 및 업데이트 {#upgrade-runtime}

1. 앱에 연결된 핸들러 저장소를 열고 해당 종속성을 설치합니다.

   ```bash
   npm install
   ```

1. 설치된 런타임을 확인합니다.

   ```bash
   npm ls @adobe/llm-apps-runtime
   ```

1. 런타임이 1.2.0 이전인 경우 현재 버전을 설치합니다.

   ```bash
   npm install @adobe/llm-apps-runtime@latest
   npm ls @adobe/llm-apps-runtime
   ```

   설치된 버전이 1.2.0 이상인지 확인합니다. 앱에서 이미 지원하는 버전을 사용하고 있는 경우 이후 버전을 유지합니다.

1. 런타임 종속성 및 해결된 버전이 최소 요구 사항을 충족하는지 확인합니다. 응용 프로그램의 자체 버전은 런타임 종속성과 별개입니다.
1. 저장소의 검사를 실행합니다. LLM 앱 보일러플레이트의 경우:

   ```bash
   npm test -- --runInBand
   npm run build
   npm run lint
   ```

   배포하기 전에 실패를 조사합니다. 원본 버전에서 검사가 이미 실패한 경우 업그레이드에 의해 도입된 실패와 별도로 해당 기준선을 기록합니다. 성공적인 로컬 빌드는 Analytics 컬렉션을 확인하지 않습니다.

1. 종속성 업데이트와 잠금 파일을 커밋한 다음 업데이트를 밀어내어 처리기 저장소의 기본 분기(일반적으로 `main`)에 병합합니다. 배포는 해당 분기를 복제합니다. 로컬이나 병합되지 않은 기능 분기에만 존재하는 업데이트는 배포에 포함되지 않습니다.

   잠금 파일이 있으면 배포는 기록된 버전을 사용하는 `npm ci`과(와) 종속성을 설치합니다. 재배포는 런타임을 자동으로 업그레이드하지 않습니다. 재배포하기 전에 GitHub의 기본 분기에 대해 업그레이드된 종속성 및 해결된 버전을 확인하십시오.

## LLM 앱 위젯 저장소 확인 및 업데이트 {#upgrade-sdk}

EDS 위젯이 있는 앱의 경우 EDS 리포지토리와 서버 런타임에서 SDK을 확인합니다. 한 저장소를 업데이트해도 다른 저장소는 업데이트되지 않습니다.

일부 EDS 저장소에는 npm 종속성 없이 복사된 SDK 스크립트가 포함됩니다. 브라우저는 제공된 스크립트를 직접 로드하므로, 패키지 항목이 없다고 해서 SDK이 누락되는 것은 아닙니다. 프로젝트의 SDK 업데이트 프로세스를 사용하여 복사된 SDK 배포를 새로 고칩니다. SDK을 복사된 스크립트로 유지 관리하는 프로젝트에는 영구 npm 종속성을 추가할 필요가 없습니다.

npm을 통해 SDK을 관리하는 프로젝트의 경우:

1. EDS 저장소 루트에서 현재 SDK을 설치하고 버전을 확인합니다.

   ```bash
   npm install --save-dev @adobe/llmapps-sdk@latest
   npm ls @adobe/llmapps-sdk
   ```

   0.1.13 이상을 사용합니다. 프로젝트에서 이미 지원된 버전을 사용하고 있는 경우 이후 버전을 유지합니다.

1. 설치 시 제공된 SDK 배포가 새로 고쳐졌는지 확인합니다. 설치 후크는 복사된 SDK 스크립트를 덮어쓰므로, 커밋하기 전에 로컬 사용자 지정을 검토하십시오. 종속성 선언만 업데이트해도 EDS에서 제공하는 스크립트는 업데이트되지 않습니다. [LLM 앱 SDK 설치](/help/guides/bring-your-own-eds.md#install-the-llm-apps-sdk)를 참조하십시오.
1. `npm run lint`과(와) 같은 EDS 저장소의 검사를 실행하고 기존 위젯이 여전히 렌더링하고 상호 작용에 응답하는지 확인합니다.
1. 종속성 및 잠금 파일 업데이트와 함께 새로 고친 SDK 배포를 커밋합니다. EDS는 설치된 npm 패키지가 아니라 복사된 스크립트를 제공합니다.

두 업데이트 메서드 후 작업의 **[!UICONTROL 스크립트 URL]**&#x200B;에서 사용하는 EDS 분기에 업데이트를 푸시하고 병합합니다. URL이 업데이트된 SDK에 제공되고 해당 지원 라이브러리가 성공적으로 로드되는지 확인합니다. URL이 EDS 라이브 사이트를 사용하는 경우 해당 사이트에 대한 게시 단계를 완료합니다.

## 다음 단계 {#next-step}

[Adobe Analytics 및 Customer Journey Analytics 설정](/help/guides/analytics-setup.md)(으)로 돌아갑니다. 모든 앱은 이벤트를 수집하려면 Analytics를 활성화하고 배포해야 합니다. 런타임 또는 SDK만 업그레이드하면 수집이 활성화되지 않습니다.

Analytics 설정을 저장한 후 프로덕션에 배포하기 전에 스테이지에 배포하고 [새 작업 호출 및 위젯 상호 작용 이벤트를 확인](/help/guides/analytics-setup.md#verify)합니다. 작업과 위젯이 계속 작동하고 단일 링크 선택이 하나의 위젯 상호 작용 이벤트를 생성하는지 확인합니다.

Analytics는 Analytics가 활성화된 배포 후 새 상호 작용을 수집하기 시작합니다. 업그레이드는 수집이 활성화되기 전에 발생한 상호 작용을 재구성하지 않습니다.
