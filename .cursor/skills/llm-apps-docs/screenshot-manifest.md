---
source-git-commit: 03c918b1643d9c4e8ebee40fd67694acb6751a14
workflow-type: tm+mt
source-wordcount: '1080'
ht-degree: 0%
---
# 스크린샷 매니페스트

받은 편지함 캡처: `docs-captures/<YYYY-MM-DD>/`

사용자가 결정 또는 상태를 확인하는 데 실질적으로 도움이 되는 체크포인트만 캡처합니다.

Source 파일 이름은 최종 파일 이름과 일치하지 않아도 됩니다. 이 기술은 스크린샷을 가시 UI 상태별로 매핑하고, 원시 파일을 유지하며, 아래 이름을 사용하여 정리된 복사본을 만듭니다.

아래의 각 안내서는 자체 출력 디렉터리를 선언합니다. 캡처가 속한 섹션에 사용합니다.

&#x200B;# 온보딩 안내서

출력 디렉터리: `help/assets/guide-onboarding-agent/`

## 필수 캡처

### `app-details-onboarding.png`

- 상태: 앱 이름, 분석 지역 및 **내 앱을 자동으로 빌드**&#x200B;를 선택했습니다.
- 포함: 앱 세부 정보, 분석 영역 및 내 앱 빌드 시작.
- 대체 텍스트: `Create LLM App — app details and Build My App enabled`

### `install-aem-code-sync.png`

- 상태: AEM 보일러판으로 초기화된 빈 EDS 저장소입니다. AEM 코드 동기화가 필요합니다.
- 포함: EDS 저장소 유효성 검사 메시지 및 설치 링크.
- 대체 텍스트: `Create LLM App — empty EDS repository initialized and AEM Code Sync required`

### `eds-admin-required.png`

- 상태: AEM 코드 동기화가 설치되었지만 현재 사용자는 EDS 사이트 관리자가 아닙니다.
- 포함: 유효성 검사 메시지 완료 및 **AEM Live 관리자 열기**.
- 대체 텍스트: `Create LLM App — EDS administrator access required`

### `actions-generating.png`

- 상태: 온보딩이 활성 상태인 동안의 작업 페이지
- 포함: 진행 메시지 및 생성 단계.
- 대체 텍스트: `Actions — generating recommendations`

### `actions-ready-for-review.png`

- 상태: 온보딩이 완료된 후 승인 전에 생성된 작업 목록입니다.
- 조치 이름, 생성/검토 상태 및 검토 통제를 포함합니다.
- 고정장치 컨텐츠만 사용합니다.
- 대체 텍스트: `Actions — generated actions ready for review`

### `generated-action-review.png`

- 주: 생성된 대표 작업 1개.
- 포함: 작업 및 위젯 메타데이터 탐색, 핸들러 생성 결과 및 **검토됨으로 표시**.
- 마스크: 필요한 경우 저장소 소유자.
- 대체 텍스트: `Generated action — ready to mark as reviewed`

### `actions-reviewed.png`

- 주: 생성된 모든 작업이 검토되었습니다.
- 포함: **모든 작업이 검토됨**, 작업 배지 및 **앱 페이지로 이동**.
- 대체 텍스트: `Actions — all generated actions reviewed`

### `deploy-stage.png`

- 상태: 시작하기 전에 배포 대화 상자.
- 포함: 대상 환경 스테이징 및 **배포**.
- 대체 텍스트: `Deploy — select the Stage environment`

### `deploy-running.png`

- 상태: 배포 파이프라인이 실행 중입니다.
- 포함: 준비, 시작, 빌드 및 게시 단계.
- 대체 텍스트: `Deploy — deployment pipeline running`

### `deploy-successful.png`

- 상태: 스테이징 배포에 성공했습니다.
- 포함: 환경 및 성공 상태.
- 마스크: 런타임 네임스페이스, 전체 MCP URL, ID, 식별 시 타임스탬프.
- 대체 텍스트: `Deploy — successful staging deployment`

### `app-mcp-url.png`

- 상태: 배포 후 앱 섹션을 테스트합니다.
- 포함: 스테이징 환경, **URL 복사** 및 성공적인 배포 기록.
- 마스크: MCP 서버 URL.
- 대체 텍스트: `App Detail — copy the staging MCP server URL`

### `chatgpt-plugins-page.png`

- 상태: ChatGPT 플러그인 페이지.
- 포함: 플러그인 탭, 검색 및 만들기 버튼.
- 대체 텍스트: `ChatGPT — Plugins page`

### `chatgpt-new-plugin.png`

- 상태: 새 플러그인 대화 상자
- 이름, 설명, 서버 URL, 인증, 확인 및 만들기가 포함됩니다.
- 마스크: MCP 서버 URL.
- 대체 텍스트: `ChatGPT — create a plugin with the MCP server URL`

### `chatgpt-plugin-connect.png`

- 상태: 플러그인 생성 후 확인.
- 포함: **추가 <plugin> &#x200B;** 연결 **&#x200B; 및 ChatGPT &#x200B;** 에 연결할 수 있습니다.
- 마스크: 브라우저 URL 및 커넥터 식별자.
- 대체 텍스트: `ChatGPT — connect the new plugin`

### `chatgpt-generated-app.png`

- 상태: ChatGPT에서 Fixture 플러그인을 호출했습니다.
- 포함: 첨부된 앱, 생성된 위젯 및 텍스트 응답.
- 제외: 대화 내역, 계정 이름 및 관련 없는 앱.
- 대체 텍스트: `ChatGPT — generated LLM App plugin response`

## 선택적 캡처

prose가 결정을 명확하게 설명할 수 없는 경우에만 캡처를 추가합니다.

- GitHub 앱 저장소 액세스 선택.
- 문제 해결을 위한 온보딩 상태 실패.
- 플러그인 아이콘 업로드

Prose에서 이미 명확한 정적 필드 목록에 대해서는 스크린샷을 추가하지 마십시오.

&#x200B;# 인증 안내서

출력 디렉터리: `help/assets/guide-authentication/`

[authentication.md](../../../help/guides/authentication.md)에서 참조합니다.

**[!UICONTROL 리소스 식별자 복사]** 단계에서 온보딩 가이드 사용
`app-mcp-url.png`. 다시 캡처하지 마십시오.

이 섹션의 모든 캡처에는 보안 구성이 표시됩니다. 저장하기 전 마스크:

- **[!UICONTROL 발급자]** URL과 ID 공급자 또는 해당 공급업체를 식별하는 호스트 이름입니다.
- MCP 서버 URL(전체, 나타나는 위치).
- 테넌트, 클라이언트 및 조직 식별자.
- 계정 이름, 아바타 및 이메일.

필드가 읽을 수 있어야 하는 중립 자리 표시자 값을 사용합니다(예: 발급자).
`https://auth.example.com`. 범위 이름은 `orders:read`과(와) 같은 일반 예제로 읽어야 합니다.

## 필수 캡처

### `auth-core-settings.png`

- 상태: **[!UICONTROL 설정]** > **[!UICONTROL 인증]**(**[!UICONTROL 인증 사용]** 및 **[!UICONTROL 핵심 설정]** 입력).
- 포함: **[!UICONTROL Workspace]** 선택기에서 **[!UICONTROL 단계]**&#x200B;를 표시하고, **[!UICONTROL 인증 사용]**&#x200B;을(를) 설정 상태로 표시하고, **[!UICONTROL 발급자]** 및 두 개 이상의 범위를 포함하는 **[!UICONTROL 지원되는 범위]**&#x200B;를 표시합니다.
- 축소된 **[!UICONTROL 고급 설정]** 컨트롤을 포함하면 **[!UICONTROL JWKS URI]**&#x200B;이(가) 선택 사항이며 현재 위치를 확인할 수 있습니다.
- 마스크: 발급자 호스트 이름입니다.
- 대체 텍스트: `Authentication — enable authentication and complete the core settings`

2026-08-25에 캡처됨. 빈 캔버스를 놓기 위해 잘렸습니다. 다음 이유로 마스크가 필요하지 않습니다.
**[!UICONTROL 발급자]**&#x200B;이(가) 다음 이전 제품에서 `https://auth.example.com`(으)로 설정되었습니다.
캡처. 나중에 이미지를 편집하는 것보다 선호합니다. **[!UICONTROL 지원되는 범위]**&#x200B;개 보류
하나의 범위(`read:all`), 두 개의 범위는 필드를 더 잘 설명하지만 이는
스스로 다시 캡처합니다.

### `auth-per-action.png`

- 상태: 인증을 사용하도록 설정한 후 **[!UICONTROL 작업별 구성]**&#x200B;이며, 모드는 의도적으로 혼합됩니다.
- 포함: 최소 세 개의 작업, 모드당 하나 — **[!UICONTROL 없음]**, **[!UICONTROL 필수]** 및 **[!UICONTROL 선택 사항]** — 및 **[!UICONTROL 범위]** 열이 제어된 범위에 채워집니다.
- 포함: **[!UICONTROL 모든 작업에 대한 인증 필요]**, 혼합 구성에서 생성되는 불확정 상태에 이상적으로 있음.
- 고정장치 작업 이름만 사용합니다.
- 대체 텍스트: `Authentication — set an auth mode and scopes for each action`

2026-08-25에 캡처됨. 잘린 것뿐이고 마스크할 것이 없습니다. 세 가지 모드 모두 표시(채워진 모델)
**[!UICONTROL 범위]** 셀 및 **[!UICONTROL 모든 작업에 대한 인증 필요]**
고정 장치 이름으로 `Test Action 1/2/3`을(를) 사용하는 알 수 없는 상태입니다.

설정 패널의 자체 컨테이너 테두리를 **내부**&#x200B;로 자릅니다. 전체 높이 1px 규칙이 각 위치에 있습니다.
캡처의 측면과 프레임 중 하나를 남겨두면 가 가장자리의 아래쪽에 있는 미늘로 표시됩니다.
이미지.

커넥터별 인증을 적용하는 [!DNL Claude]에 대한 제품 자체 경고가
**두 번의 캡처 라운드에 걸쳐 이 탭에서 관찰되지 않음**, 따라서 여기에서는 필요하지 않습니다. 다음
안내서에서는 대신 prose로 동작을 기술합니다. 경고가 이후 빌드에 있는 경우
`auth-claude-warning.png`(으)로 캡처하고 항목을 추가합니다.

### `chatgpt-authentication-mode.png`

- 상태: **[!UICONTROL 인증]** 드롭다운이 열려 있는 **[!UICONTROL 새 플러그 인]** 대화 상자
- 포함: 가이드에서 매핑 테이블을 실제 컨트롤에 대해 확인할 수 있도록 세 가지 값(**[!UICONTROL 인증 없음]**, **[!UICONTROL 혼합]**, **[!UICONTROL OAuth]**)을 모두 포함합니다.
- 마스크: MCP 서버 URL 및 브라우저 URL의 모든 커넥터 식별자.
- 대체 텍스트: `ChatGPT — select the authentication mode for the plugin`

온보딩 가이드의 `chatgpt-new-plugin.png`과(와) 같은 방식으로 프레임 설정: 대화 상자 카드
페이지의 여백은 약 40px의 왼쪽과 위쪽에 여전히 표시됩니다. 플러시 자르기 안 함
카드요

설명서에서 다른 모든 캡처와 일치하도록 2026-08-25, 라이트 모드를 캡처했습니다. 다음
드롭다운에서 **[!UICONTROL 서버 URL]** 필드를 폐색하므로 MCP URL을 읽을 수 없습니다.
그 반투명 재질은 그 장 내용물의 흐릿한 이미지가
옵션. 강조 표시되지 않은 세 개의 행은 패널 채우기와 레이블로 다시 칠해졌습니다
다시 렌더링하면 제거됩니다. 눈이 아니라 샘플링을 통해 확인합니다. 출혈이 충분히 약합니다.
miss and it is MCP 서버 URL .

Live 컨트롤에서 제공하는 **네 개** 값, 즉 **[!UICONTROL OAuth]**, **액세스
토큰/API 키&rbrack;**, &#x200B;** [!UICONTROL 인증 없음] **&#x200B; 및 &#x200B;** [!UICONTROL 혼합]**. 안내서의 매핑
표에서는 앱의 인증 모드가 매핑될 수 있는 세 가지 모드만 다룹니다. 이 모드는 올바르지만 그렇지 않습니다
드롭다운에 세 가지 옵션이 있다고 설명합니다.

## 선택적 캡처

증명이 불충분한 경우에만 추가:

- `auth-scope-blocked.png` — 작업에 **[!UICONTROL 지원되는 범위]**&#x200B;에서 누락된 범위가 필요하므로 **[!UICONTROL 저장]**&#x200B;이(가) 차단되었습니다. 문제 해결 항목에 유용합니다.
- 중간 대화 로그인 프롬프트에서 **[!UICONTROL Optional]** 작업을 발생시킵니다. 자주 변경되는 플랫폼 소유 UI이며 이미 산문에 설명되어 있습니다.

ID 공급자의 자체 로그인 페이지를 캡처하지 마십시오. 이 설명서에서 명시하지 않은 공급업체를 식별합니다.
