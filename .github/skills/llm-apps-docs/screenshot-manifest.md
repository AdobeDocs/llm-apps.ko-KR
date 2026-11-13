---
source-git-commit: 0276ae4e4627e49157911ad579a42c0e77045eee
workflow-type: tm+mt
source-wordcount: '2314'
ht-degree: 1%
---
# 스크린샷 매니페스트

다음을 사용하여 브라우저 자동화로 이러한 상태 캡처
[screenshots.md](screenshots.md). 문서와 관련된 체크포인트를 선택합니다.
이 매니페스트는 리소스 만들기, 생성, 승인 또는
배포.

원시 캡처가 `docs-captures/<run-id>/`에 남아 있습니다. 시각적으로 검토된 항목만 게시합니다.
`help/assets/`에서 정리된 PNG입니다. 다음과 같은 경우 파일 이름을 안정적으로 유지합니다.
이미지를 새로 고치는 중입니다.

## 인프라 설정

문서: `/help/guides/create-app.md`

출력 디렉터리: `help/assets/guide-infrastructure/`

| 파일 이름 | 상태 및 유용한 컨텍스트 | 제안된 대체 텍스트 |
|---|---|---|
| `setup-choice.png` | **처리 시작** 및 **내 Cloud Manager 설정 사용**, 자체 리포지토리 옵션에서 콜아웃 시작 | LLM 앱 만들기 - 시작하기 LLM 앱을 처리하고 Cloud Manager 설정을 사용할 수 있습니다. |
| `action-repository.png` | Cloud Manager 프로그램 및 저장소를 선택했습니다. **저장소의 유효성을 검사했습니다** | 작업 인프라 설정 - Cloud Manager 프로그램 및 저장소 선택됨 |
| `widget-source.png` | **위젯 리포지토리를 수동으로 선택**&#x200B;하는 위젯 소스(Cloud Manager EDS가 곧 제공될 예정입니다.) | 위젯 인프라 설정 - 위젯 소스 |
| `widget-repository.png` | GitHub가 아닌 위젯 리포지토리의 유효성을 검사했습니다. **Edge Delivery Services 사이트** 메모 | 위젯 인프라 설정 - 검증된 위젯 저장소 및 연결된 EDS 사이트 |
| `app-details.png` | 저장소, Application Insights 데이터 영역에서 설정된 앱 이름, AEM 프로그램 | 앱 세부 사항 - 이름, AEM 프로그램 및 Application Insights 데이터 영역 |
| `settings-repositories.png` | 설정 > Cloud Manager 저장소 카드 및 연결된 사이트가 모두 있는 저장소 | 앱 설정 - 저장소 섹션 |
| `provisioning.png` | 승인된 LLM 앱 관리 설정 진행 중 | 앱 설정 - Adobe이 인프라 준비 중 |

마지막 캡처(모두 `provisioning.png` 및 `da-write-permissions.png` 제외): 2026-11-13, 단계
(`experience-stage.adobe.com/llm-apps`), 가져오기 기능이 있는 GitLab 작업 및 위젯 저장소
Git 소유. 프로그램, 저장소, 사이트 및 원본 값은 마스킹되고 빨간색 아웃라인은 콜아웃입니다.
캡처되지 않음: GitHub 위젯 경로(AEM 코드 동기화 설치, AEM 설정 유효성 검사). 아직 없음
프로덕션에서 확인되었습니다.

계정 세부 정보, 조직 ID, 실제 프로그램 이름, 저장소 제외
식별자 및 앱 ID입니다. 빈 필드 또는 중립적인 고정장치 이름을 사용합니다.
가능합니다. 리포지토리 유효성 검사는 코드를 초기화할 수 있으므로 다음 작업을 수행하기 전에 승인을 얻으십시오.
유효성 검사 결과 캡처

## 작업 만들기 및 생성된 작업 검토

문서: `/help/guides/creating-actions.md`

출력 디렉터리: `help/assets/guide-creating-actions/`

| 파일 이름 | 상태 및 유용한 컨텍스트 | 제안된 대체 텍스트 |
|---|---|---|
| `build-choice.png` | 빌드 작업이 자동으로 선택되었고, 공개 웹 사이트가 입력되었으며, 안정적인 시작 제어가 포함된 경우 다음과 같습니다. | 작업 - 자동 생성 선택 및 공개 웹 사이트 입력 |
| `generating.png` | 생성 실행, 시간 예상 및 작업으로 이동 | 작업 - 생성 실행 중 |
| `actions-ready-for-review.png` | 고정장치 작업 이름, 검토 배지 및 검토 컨트롤 | 작업 - 생성된 작업을 검토할 준비가 되었으며 배포되지 않았습니다. |
| `generated-action-review.png` | 작업 세부 사항, LLM 위젯 세부 사항, 애플리케이션 인사이트, ChatGPT 확장, **분기 푸시** 알림(자체 저장소) 및 검토한 대로 표시. 마지막 캡처: 2026-11-13, 단계, GitLab 앱, 앱 이름 마스크 | 생성된 작업 검토 - 분기가 푸시되고 검토됨으로 표시 |

개인 리포지토리를 마스크하고 요청 링크를 가져옵니다. 생성된 개인 또는 을(를) 자르기
문서에 검토 상태만 필요한 경우 연락처 세부 정보. 선택 안 함
승인 없이 **검토됨으로 표시**.

승인이 범위 내에 있고
국가는 진정으로 존재한다. 스크린샷을 만족시키기 위해 생성된 작업을 승인하지 않음
체크리스트.

## 처음부터 작업 만들기

문서: `/help/guides/create-action.md`

출력 디렉터리: `help/assets/guide-create-action/`

| 파일 이름 | 상태 및 유용한 컨텍스트 | 제안된 대체 텍스트 |
|---|---|---|
| `action-basic-info.png` | 작업 세부 정보: 제품 검색, 코드 식별자, 설명, `category` 및 `query` 매개 변수 | 새 작업 만들기 - 작업 정보 및 입력 매개 변수가 있는 작업 세부 정보 |
| `action-llm-settings.png` | 확장된 LLM 설정: 읽기 전용, idempotent, 오픈 월드 주석, 상태 텍스트, 가시성 | 새 작업 만들기 - LLM 설정 |
| `action-analytics-user-intent.png` | Application Insights: 사용자 의도와 기본 지침 수집 | 새 작업 만들기 - Application Insights |
| `widget-metadata.png` | LLM 위젯 세부 사항: Edge Delivery Services 블록, 예제 위젯 도메인, 위젯 설명 및 템플릿 URL | 새 작업 만들기 - LLM 위젯 세부 정보 |
| `widget-csp.png` | 템플릿 URL 및 CSP 구성 도메인 목록 | 새 작업 만들기 - CSP 구성 |

마지막 캡처: 2026-11-13, 단계, 대화 상자 리팩터링 후(작업 편집기와 동일한 탭:
작업 세부 사항, LLM 위젯 세부 사항, 애플리케이션 통찰력, ChatGPT 확장 프로그램, 권한,
테두리를 선호하며 파일 매개 변수 섹션이 제거되었습니다. 예제 값 - 대화 상자
이(가) 저장하지 않고 취소되었습니다. 빨간색 윤곽선은 콜아웃입니다.

## 인증

문서: `/help/guides/authentication.md`

출력 디렉터리: `help/assets/guide-authentication/`

| 파일 이름 | 상태 및 유용한 컨텍스트 | 제안된 대체 텍스트 |
|---|---|---|
| `auth-core-settings.png` | 단계, 인증 사용, `https://auth.example.com`, `catalog:read`, `profile:read` 및 고급 설정 | 인증 - 인증을 활성화하고 핵심 설정을 완료합니다. |
| `auth-per-action.png` | 없음, 필수, 선택 사항 및 지정된 범위를 표시하는 고정장치 작업 | 인증 - 각 작업에 대한 인증 모드 및 범위 선택 |

저장하지 않은 설정을 사용하고 나중에 버립니다. 저장된 인증 확인
변경되지 않습니다. 리소스 식별자 단계 재사용
`help/assets/guide-onboarding-agent/app-mcp-url.png`.

UI에 배포된 런타임 또는 현재와 충돌하는 경고가 표시되는 경우
플랫폼 비헤이비어, 불일치 문서화. 테이블 전용 자르기가 적절합니다.
교수모드일 때, 그러나 설명하지 않고 관련 경고를 숨기지 마십시오.

## 앱 변수

문서: `/help/guides/app-variables.md`

출력 디렉터리: `help/assets/guide-app-variables/`

`Good day`이(가) 있는 `GREETING_PREFIX`을(를) 예제 값으로 사용하고 `Howdy`을(를) 예제 값으로 사용합니다.
대체 값입니다. 실제 구성 값을 캡처하지 마십시오.

| 파일 이름 | 상태 및 유용한 컨텍스트 | 제안된 대체 텍스트 |
|---|---|---|
| `variables-empty.png` | 빈 스테이지 작업 영역, Workspace 선택기 및 추가 | 변수 및 암호 - 빈 스테이지 작업 영역 |
| `add-variable-dialog.png` | 저장하지 않은 GREETING_PREFIX 변수 및 현재 비밀 지원 알림 | 변수 또는 암호 추가 - GREETING_PREFIX 및 Good Day 입력 |
| `variable-added.png` | 이름, 유형, 값, 타임스탬프 및 행 컨트롤이 있는 승인된 고정장치 변수 | 변수 및 암호 - 단계 작업 영역의 GREETING_PREFIX |
| `update-variable-dialog.png` | 현재 값 Good day 및 저장되지 않은 새 값 Howdy | GREETING_PREFIX 업데이트 - 좋은 날을 Howdy로 변경 |
| `delete-variable-dialog.png` | 삭제를 확인하지 않는 확인 대화 상자 | GREETING_PREFIX 삭제 - 삭제 확인 검사 |

대화 상자를 열어도 저장 또는 삭제가 승인되지 않습니다. 안전한 기존 항목 재사용
고정장치 변수 또는 만들기 전에 묻기. 다음 이후에만 이전 이미지 유지
필드 및 컨트롤이 현재 UI와 일치하는지 확인합니다.

## ChatGPT 확장

문서: `/help/guides/chatgpt-extensions.md`

출력 디렉터리: `help/assets/guide-chatgpt-extensions/`

글로벌 및 스레드 진입점 스크린샷, 디스플레이 설정 및 작성기 언급
구성 스크린샷은 2026-11-10에 제공되었으며 다음과 같이 게시됩니다.
아래에 기록되었습니다. 기존 자산이 아닌 다른 체크포인트는 계획된 상태로 유지됩니다. 금지
실제 캡처가 정리되고 시각적으로 표시될 때까지 문서 이미지 링크 추가
검토함.

| 파일 이름 | 상태 및 유용한 컨텍스트 | 제안된 대체 텍스트 |
|---|---|---|
| `extensions-tab.png` | ChatGPT 확장이 선택된 상태로 저장된 위젯 지원 작업 | ChatGPT 확장 프로그램 - 진입점, 표시 모드 및 작성기 언급 |
| `global-thread-entrypoints.png` | 파일을 선택 취소한 상태로 홈 위젯에 대해 전역 및 스레드가 함께 선택됨 | ChatGPT 확장 프로그램 - 홈 작업에 대해 선택된 전역 및 스레드 |
| `global-entrypoint-menu.png` | 클라이언트의 탐색 메뉴에 있는 홈 진입점 | 홈 글로벌 진입점을 표시하는 ChatGPT 탐색 메뉴 |
| `global-entrypoint-open.png` | 전체 화면 및 탐색 레일이 표시되는 전체 클라이언트 창 | 글로벌 진입점에서 열린 홈 대시보드 |
| `thread-entrypoint-menu.png` | 플러그인이 확장된 대화 메뉴 | 대화 메뉴 - 홈 스레드 진입점 선택 |
| `thread-entrypoint-open.png` | 대화 옆에 있는 홈 대시보드 | ChatGPT 대화 옆에서 홈 대시보드 열기 |
| `file-entrypoint.png` | .csv 로 저장하지 않은 파일 진입점 | 파일 진입점 - CSV 파일에 대한 사용자 지정 뷰어 제공 |
| `entrypoint-icon.png` | 작업의 선택된 진입점에서 공유한 저장되지 않은 선택적 아이콘 URL | Entrypoint 아이콘 - 선택적 공유 작업 아이콘 설정 |
| `display-settings.png` | 모델 실행 환경 설정, 지원되는 리소스 모드 및 호스트 기본값에서 초기 표시 | 표시 모드 - 기본적으로 리소스 및 모델 실행 환경 설정 |
| `composer-mentions.png` | 언급 토글이 켜진 검색 언급 텍스트 전용 작업 | 검색 언급 - 작성기 언급 활성화됨 |
| `composer-mentions-query.png` | 선택적 문자열 쿼리 입력을 사용한 검색 언급 작업 세부 정보 | 검색 언급 - 쿼리라는 선택적 문자열 입력 |
| `composer-mentions-conflict.png` | 기존 공급자의 이름을 지정하는 언급 전환 및 경고 비활성화 | 검색 언급이 이미 공급자이므로 작성기 언급을 사용할 수 없습니다. |

민감하지 않은 승인된 카탈로그 고정장치를 사용합니다. 양식이 자동 저장되지 않음 확인
예제 값을 입력하기 전에, 저장되지 않은 변경 내용을 나중에 삭제하십시오. 저장 안 함,
생성된 작업을 승인하거나 만 배포하여 이 체크리스트를 완료합니다.
타사 결과는 호환되는 클라이언트에 대한 인증된 액세스 권한이 필요합니다. 파일
진입점에 데스크탑이 필요합니다. 작업 브라우저는 클래식 ChatGPT가 아닙니다.

### Entrypoint 설명서 업데이트, 2026-11-05

- Source: 현재 로컬 `llm-apps-ui` 구성 요소 및 입력 유효성 검사이며
프로덕션 UI 워크스루.
- 빠른 작업 컨트롤이 제거되었습니다. 글로벌, 스레드 및 파일 사용
한 위젯에 대해 여러 개의 선택 항목을 지원하는 확인란입니다. 아이콘 URL은
진입점에서 공유하는 선택적 작업 수준 설정입니다.
- 파일을 선택하면 없을 때 이름이 `file`인 선택적 개체 입력이 추가됩니다.
파일을 전역 또는 스레드와 결합하려면 입력이 선택 사항으로 유지되어야 합니다.
- 두 자동화 브라우저 프로필이 모두 삭제되어 브라우저 검사가 차단되었습니다.
이미 사용 중입니다. 새 스크린샷이 캡처되지 않았습니다.

### 텍스트 초안 제공, 2026-11-03

- Source 환경: 로컬 UI/API/런타임/SDK 구현이 완료되지 않음
프로덕션 작업 편집기 워크스루.
- 게시된 패키지 확인에서 런타임 1.3.0 및
SDK 0.2.1. 이는 문서화된 베이스라인 버전이며, 다음을 모두 충족해야 합니다.
이 기능은 이러한 정확한 릴리스에 도입되었습니다.
- OpenAI의 공개 확장 사양이 플랫폼 제한 사항을 확인했습니다.
- 프로덕션 로그인이 별도의 에이전트 제어 브라우저에서 성공했습니다. 앱 없음
설정이 저장되고, 고정 장치가 선택되지 않았으며, 배포가 시작되지 않았습니다.
- 사용자가 스크린샷을 지연했습니다. 새로운 이미지가 캡처되거나 대체되지 않았습니다.
정확한 프로덕션 제어 레이블과 전체 호스트 비헤이비어는 여전히
게시하기 전에 라이브 워크스루

## 배포 및 테스트

문서: `/help/guides/deploy-your-app.md`

| 출력 디렉터리 | 파일 이름 | 상태 및 유용한 컨텍스트 |
|---|---|---|
| `help/assets/guide-onboarding-agent/` | `deploy-stage.png` | 시작하기 전에 Target 환경 스테이징 및 배포 컨트롤 |
| `help/assets/guide-onboarding-agent/` | `app-mcp-url.png` | 현재 앱 카드, 환경 레이블 및 URL 복사 테스트, 모든 런타임 URL 마스크 |
| `help/assets/guide-deploy/` | `deployment-history.png` | 앱 식별자가 없는 환경, 상태 및 배포 날짜 열 |

허용되는 경우 테스트 카드 및 기록에 대해 읽기 전용 기존 배포를 사용합니다.
사용자가 승인하지 않는 한 이러한 상태를 가져오기 위해 배포를 시작하지 마십시오.
실행 중 또는 성공적인 배포 대화 상자를 캡처하는 경우는 둘 다 상태일 때에만 가능합니다.
관련성이 있고 권한이 있습니다.

`/help/guides/test-in-chatgpt.md`의 경우 유지됨
`help/assets/guide-onboarding-agent/chatgpt-generated-app.png`이(가) 제품을 보여 줍니다.
응답. 현재 설정 UI는 확인되지 않습니다. 새로운 ChatGPT 또는 Claude 설정
스크린샷은 현재 인증된 액세스가 필요합니다. 그렇지 않으면 공식 공급업체를 사용하십시오.
확인되지 않은 이미지를 삭제하고 지침

## Analytics 설정

문서: `/help/guides/analytics-setup.md`

출력 디렉터리: `help/assets/guide-analytics/`

| 파일 이름 | 상태 및 유용한 컨텍스트 | 제안된 대체 텍스트 |
|---|---|---|
| `analytics-settings.png` | 앱 설정, Analytics 탭, Analytics 활성화 켜기, 빈 데이터 스트림 ID, 요구 사항 확장, 변경 사항 저장 비활성화 | 앱 설정, Analytics 탭 - Analytics 활성화 켜짐, 빈 데이터 스트림 ID 필드 및 확장된 요구 사항 목록 |
| `datastream-analytics-service.png` | 더미 보고서 세트 ID로 활성화된 데이터 수집, 서비스 추가, Adobe Analytics | 서비스 추가 - 예제 보고서 세트 ID로 활성화된 Adobe Analytics |
| `datastream-platform-service.png` | 이벤트 데이터 세트만 선택한 데이터 수집, Adobe Experience Platform 서비스 | Adobe Experience Platform 서비스 - 이벤트 데이터 세트가 선택되었고 기타 옵션이 지워졌습니다. |
| `datastream-services.png` | 두 서비스가 모두 활성화된 데이터 스트림 세부 정보, 더미 데이터 스트림 ID 및 매핑 스키마 | 데이터 스트림 세부 정보 - 두 서비스 모두 활성화되었으며 데이터 스트림 ID 및 매핑 스키마가 예입니다. |

실제 데이터 스트림 ID는 캡처하지 마십시오. **변경 내용 저장**: 저장 후 선택하지 마십시오.
관리자만 데이터 스트림을 변경할 수 있습니다. Experience Platform, Adobe Analytics 표시,
및 Customer Journey Analytics 화면은 와 관련된 설정이 표시되는 곳에만 적용됩니다.
LLM 앱; 기타 모든 것에 대한 Experience League 링크. 조직 바꾸기
이름, 보고서 세트 ID, 데이터 스트림 ID, 데이터 세트 ID, 이메일 및 개인 이름
다음 더미 값으로 `example-org`, `demo.<suffix>` 보고서 세트,
`00000000-0000-0000-0000-000000000000`, `0000…0001`, `user@example.com` 및
`Example User`.

## 제출 준비

문서: `/help/guides/prepare-submission.md`

출력 디렉터리: `help/assets/guide-submission/`

| 파일 이름 | 상태 및 유용한 컨텍스트 | 제안된 대체 텍스트 |
|---|---|---|
| `submission-card.png` | 프로덕션 배포 후 앱 페이지, 설정으로 제출 카드 준비 | 제출 준비 - 프로덕션 준비 상태 및 설정 |
| `submission-setup.png` | 제출 준비 대화 상자, 설정 및 준비, 예: 게시자 세부 정보 및 두 아이콘 저장됨 | 설정 및 준비 - 게시자 및 법적 세부 정보, 승인 아이콘 및 변경 내용 저장 |
| `submission-prepare.png` | 패키지를 준비하기 위해 스크롤된 동일한 대화 상자, 준비 가 준비 완료 로 표시됨, 확인란이 선택 취소됨 | 패키지 준비 - 준비 완료, 확인 확인란 및 패키지 준비 |
| `submission-progress.png` | 실행이 진행되는 동안 동일한 대화 상자 | 준비 진행 중 - 진행률 표시줄 및 시작 시간 |
| `submission-results.png` | 자리 표시자 법적 링크로 실행을 완료한 후의 결과: 준비에 주의가 필요하고 패키지 다운로드가 비활성화됩니다. | 결과 - 준비 요약, 보고서 다운로드, 패키지 다운로드 및 녹화 다운로드 |
| `submission-test-cases.png` | 결과, 테스트 사례, 첫 번째 사례 요약 | 테스트 사례 - 요청, 예상 동작 및 기록된 상태가 있는 검토 사례 |
| `submission-before-submit.png` | 결과, 목록 및 합격/경고/불합격 검사를 제출하기 전에, 자르기 아래에 ID 실행 | 제출하기 전에 - 나머지 세부 정보, 업데이트 세부 정보 및 패키지 확인 |

프로덕션에 배포된 앱에서 2026-11-13년 스테이지에서 캡처됩니다. 만 사용
예제 게시자 값(`Frescopa Coffee (example)`, `https://www.example.com/...`)
및 자리 표시자 아이콘 각 준비 실행은 배포된 앱을 호출하고 다음을 수행합니다
20분. [결과] 화면을 새로 고쳐야 할 때만 시작합니다. 마스크 MCP URL,
결과 캡처에서 ID 및 앱 이름을 실행합니다.

## 기존 캡처 권한

2026-10-19 캡처 실행은 일치하는 UI/API와 함께 사용자 요청 로컬 UI를 사용했습니다
소스 검사. 운영 패리티를 설정하지 않습니다.

- 원시 캡처: `docs-captures/2026-10-19/` 및 해당 `full-review/` 하위 디렉터리.
- 이전 또는 교체된 공개 이미지는 로컬 `retired/` 하위 디렉터리에 남아 있습니다.
- 문서 구성 요소가 생성된 작업 검토 단계에 도달했습니다. 작업 없음
승인, 가져오기 요청 병합, 배포 또는 저장된 인증/변수
변경 사항은 캡처 실행의 일부입니다.
- 이전 실행의 변수 행, 업데이트 및 삭제 이미지는 이후에 유지되었습니다.
현재 구현에 대한 해당 컨트롤을 확인하고 있습니다.
- 서드파티 설정 화면이 다시 캡처되지 않았습니다. 더 이상 사용되지 않는 설치 스크린샷은
(이)가 현재 상태로 표시되지 않고 제거되었습니다.

새로 고칠 때마다 날짜, 소스 환경, 확인된 상태,
삭제 및 남은 확인 제한. 이전 provenance 복사 안 함
새로운 체포를 증거로 제시하다.

### 작업 편집기 스위치 제거, 2026-10-19

실행 중인 `guide-creating-actions/generated-action-review.png`에서 새로 고침
편집기의 Enabled 스위치를 제거한 후 로컬 UI를 표시합니다. 앱 페이지의 가시성
스위치는 작업 켜기/끄기 제어로 유지되며 모델 및 위젯 노출 설정
편집기에 남아 있습니다. 저장소 링크는 마스킹되고 계정 탐색은
제외됨. 이 새로 고침 중에는 저장, 승인, 전환 또는 배포된 작업이 없습니다.
원시 캡처: `docs-captures/2026-10-19/remove-enabled/`.

### Analytics 설정, 2026-10-21

사용자가 제공한 `guide-analytics/analytics-settings.png`을(를) 새로 고쳤습니다.
에이전트 기반 실행이 아닌 `Screenshot 2026-10-21 at 11.48.36.png`. 소스
환경: 기록되지 않으며 이미지에 표시되지 않음, 프로덕션 패리티는 기록되지 않음
확인됨. 시각적 검토에서 데이터 스트림 ID 필드인 Analytics 활성화가 켜져 있는지 확인했습니다
가 비어 있고, 요구 사항이 확장되며, 변경 사항 저장이 비활성화됩니다. 이미지는
편집 없이 복사됩니다. 식별자나 계정 세부 사항이 표시되지 않습니다.

### 데이터스트림 스크린샷, 2026-10-19

`guide-analytics/datastream-analytics-service.png`을(를) 추가함,
기존 `datastream-platform-service.png` 및 `datastream-services.png`
내부 데이터 수집 캡처(2026-07-29 및 2026-10-19). 보고서 세트 ID
및 데이터 스트림 ID가 더미 텍스트 오버레이(`demo.aem.llm` 및
`00000000-0000-0000-0000-000000000000`); 오버레이 글꼴은 샌프란시스코이므로
제품 글꼴과 약간 다릅니다. 플랫폼 서비스 이미지가 필요하지 않음
변경 사항. Analytics 서비스 이미지는에서 마우스 포인터를
원본 캡처. OCR이 최종 이미지를 통과했지만 원본 식별자가 없습니다.
다음과 같은 경우 UI에 입력된 더미 값을 사용하는 새로운 캡처로 대체합니다.
그 다음 문서가 새로 고쳐집니다.
