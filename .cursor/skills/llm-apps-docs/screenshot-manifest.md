---
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '398'
ht-degree: 0%

---
# 온보딩 스크린샷 매니페스트

받은 편지함 캡처: `docs-captures/<YYYY-MM-DD>/`

출력 디렉터리: `help/assets/guide-onboarding-agent/`

사용자가 결정 또는 상태를 확인하는 데 실질적으로 도움이 되는 체크포인트만 캡처합니다.

Source 파일 이름은 최종 파일 이름과 일치하지 않아도 됩니다. 이 기술은 스크린샷을 가시 UI 상태별로 매핑하고, 원시 파일을 유지하며, 아래 이름을 사용하여 정리된 복사본을 만듭니다.

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
