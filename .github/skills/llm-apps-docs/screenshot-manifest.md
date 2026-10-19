---
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1052'
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
| `setup-choice.png` | 두 인프라 옵션 모두 표시 시작 | LLM 앱 만들기 - Adobe 관리 인프라 또는 자체 저장소 선택 |
| `app-details.png` | 앱 이름, AEM 프로그램, 분석 영역 및 변경할 수 없는 설정 경고 | 앱 세부 정보 - 이름을 입력하고 AEM 프로그램을 선택한 다음 분석 영역을 선택합니다. |
| `action-repository.png` | GitHub가 선택된 자체 저장소 경로 및 저장소 연결 지침 | 작업 인프라 설정 - 비어 있거나 호환되는 작업 저장소 연결 |
| `provisioning.png` | 승인된 Adobe 관리 설정 진행 중 | 앱 설정 - Adobe이 인프라 준비 중 |

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
| `generated-action-review.png` | LLM 위젯 세부 정보, 작업 세부 정보, Analytics, 생성된 가져오기 요청 알림 및 검토됨으로 표시 | 생성된 작업 - 승인 전에 위젯 및 핸들러 검사 |

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
| `action-basic-info.png` | 제품 검색, 파생 식별자, 유용한 설명 및 읽기 전용 주석 | 새 작업 만들기 - 기본 정보 및 주석 |
| `action-analytics-user-intent.png` | 사용자 의도와 기본 익명화된 요약 지침을 수집합니다. | 새 작업 만들기 - 사용자 의도 컬렉션 검토 |
| `widget-metadata.png` | Edge Delivery Services 블록 유형 및 안전 예시 샌드박스 원본 | 새 작업 만들기 - 위젯 유형 및 샌드박스 도메인 |
| `widget-template-urls.png` | 예제 스크립트 및 위젯 URL | 새 작업 만들기 - 스크립트 및 위젯 URL |
| `widget-permissions.png` | 옵션 브라우저 권한, 사용하지 않는 권한 해제 | 새 작업 만들기 - 브라우저 권한 |
| `widget-csp.png` | 리소스, 연결, 프레임, 리디렉션 및 기본 URI 도메인 컨트롤 | 새 작업 만들기 - 콘텐츠 보안 정책 도메인 |

만들기 대화 상자에 **작업** 및 **위젯 메타데이터** 탭이 있습니다. 저장된 편집기
**LLM 위젯 세부 정보**, **작업 세부 정보** 및 **Analytics** 탭이 있습니다. 캡처
프로시저가 실제로 사용하는 레이아웃을 지정합니다. 저장하지 않고 예제 필드를 채운 다음
편집 내용을 취소합니다.

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
