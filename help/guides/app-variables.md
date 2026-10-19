---
title: 앱 변수 및 암호 구성
description: Adobe LLM 앱 앱에 환경별 변수를 추가하고, 작업 핸들러에서 읽고, 배포하고, 일반적인 문제를 해결합니다.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1170'
ht-degree: 1%
---

# 앱 변수 및 암호 구성 {#app-variables}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

변수 및 비밀을 사용하여 작업 처리기의 값을 하드코딩하지 않고 앱을 구성합니다. 예를 들어 핸들러 코드를 변경하지 않고 스테이지에서 테스트 서비스를 사용하고 프로덕션에서 라이브 서비스를 사용하도록 제품 카탈로그 API URL을 구성합니다.

변수에는 민감하지 않은 설정이 포함됩니다. 비밀은 API 키 및 액세스 토큰과 같은 중요한 값에 사용됩니다.

>[!IMPORTANT]
>
>UI는 현재 **변수만 지원**&#x200B;합니다. 향후 릴리스를 위해 비밀 지원이 예정되어 있습니다. 그때까지 **암호, API 키, 액세스 토큰 또는 기타 중요한 정보를**&#x200B;변수에 저장하지 않음&#x200B;**: 설정 테이블에 변수 값이 표시되고 사용자가 복사할 수 있도록 합니다.**

**이 안내서:** 앱 변수를 추가하고 작업 처리기에서 읽는 방법을 알아봅니다. 앱을 배포하고 핸들러가 예상 값을 사용하는지 확인합니다.

## 시작하기에 앞서 {#before-you-begin}

다음이 필요합니다.

- 변수를 읽어야 하는 처리기를 업데이트하려면 **앱의 처리기 저장소에 액세스**&#x200B;하십시오.
- 해당 저장소의 **`@adobe/llm-apps-runtime`1.1.0 이상** 아래 도우미를 사용하기 전에 버전을 확인하십시오.

버전을 확인하려면 처리기 저장소에서 다음 명령을 실행합니다.

```bash
npm ls @adobe/llm-apps-runtime
```

버전이 1.1.0 이전인 경우 업그레이드한 다음 `package.json` 및 `package-lock.json`을(를) 커밋하고 푸시합니다.

```bash
npm install @adobe/llm-apps-runtime@latest
```

## 변수 관리 {#manage-variables}

각 환경 **[!UICONTROL Stage]** 또는 **[!UICONTROL Production]**&#x200B;에는 자체 변수가 있습니다. 변수를 추가, 업데이트 또는 삭제하기 전에 **[!UICONTROL Workspace]**&#x200B;을(를) 확인하십시오. 변경 사항은 다음에 해당 환경에 앱을 배포할 때 적용됩니다.

### 변수 추가 {#add-variable-to-app}

이 예제에서는 값이 `Good day`인 민감하지 않은 변수 `GREETING_PREFIX`을(를) 사용하여 처리기의 기본 인사말 `Hello`을(를) 재정의합니다. 변수를 추가하면 작업을 자동으로 **변경할 수 없습니다**. 처리기 **must**&#x200B;에서 변수를 읽습니다.

#### 1단계: UI에 변수 추가 {#add-a-variable}

1. 앱을 열고 왼쪽 탐색에서 **[!UICONTROL 설정]**&#x200B;을 선택합니다.
2. **[!UICONTROL 변수 및 암호]** 탭을 엽니다.
3. **[!UICONTROL Workspace]**&#x200B;에서 **[!UICONTROL 단계]** 또는 **[!UICONTROL 프로덕션]**&#x200B;을 선택합니다.
4. **[!UICONTROL 추가]**&#x200B;를 선택합니다.

   ![변수 및 암호 — [추가] 버튼이 있는 빈 단계 작업 영역](/help/assets/guide-app-variables/variables-empty.png)

5. 대화 상자에서 다음을 입력합니다.
   - **[!UICONTROL 이름]**: `GREETING_PREFIX`.
   - **[!UICONTROL 유형]**: **[!UICONTROL 변수]**&#x200B;을(를) 선택한 상태로 둡니다.
   - **[!UICONTROL 값]**: `Good day`.

   ![변수 또는 암호 추가 — GREETING_PREFIX가 좋은 날로 설정됨](/help/assets/guide-app-variables/add-variable-dialog.png)

6. 변수를 저장하려면 **[!UICONTROL 추가]**&#x200B;를 선택합니다.

변수가 테이블에 **[!UICONTROL 이름]**, **[!UICONTROL 유형]**, **[!UICONTROL 값]**, **[!UICONTROL 마지막으로 업데이트됨]** 날짜로 나타납니다. 값을 복사하려면 인접한 copy 컨트롤을 사용합니다.

![변수 및 암호 — GREETING_PREFIX가 단계 작업 영역에 저장됨](/help/assets/guide-app-variables/variable-added.png)

>[!IMPORTANT]
>
>저장하면 선택한 환경의 구성에 변수가 추가됩니다. 다시 배포할 때까지 배포된 앱을 **업데이트하지**&#x200B;않습니다.

#### 2단계: 핸들러에서 변수 읽기 {#use-a-variable}

처리기 리포지토리에서 `actions/<action-name>/index.js`을(를) 엽니다. 처리기가 두 번째 인수 `extra`을(를) 수락하는지 확인하고 `getVariable`을(를) 사용하여 변수를 읽습니다.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ name = 'there' } = {}, extra) => {
  const prefix = getVariable(extra, 'GREETING_PREFIX') || 'Hello';

  return {
    content: [{ type: 'text', text: `${prefix}, ${name}!` }]
  };
};
```

값이 `Good day`인 경우 `name`이(가) `Ada`(으)로 설정된 호출은 기본값 `Hello, Ada!` 대신 `Good day, Ada!`을(를) 반환합니다. 나중에 값을 `Howdy`(으)로 변경하면 다른 처리기를 편집하지 않고 다시 배포하면 인사말이 변경됩니다.

코드 **must**&#x200B;의 이름이 UI의 이름과 정확히 일치합니다. 변수가 설정되지 않으면 `getVariable`에서 `undefined`을(를) 반환합니다. 예제와 같이 적절한 기본값을 선택하거나 작업에 설정이 필요한 경우 명확한 오류를 반환합니다.

>[!NOTE]
>
>변수는 앱의 작업 처리기에서만 사용할 수 있습니다. 위젯에서 자동으로 사용할 수 있는 **아니요**&#x200B;입니다.

핸들러 변경이 준비되면 커밋하고 앱의 핸들러 저장소에 푸시합니다. 다음 배포에서는 가장 최근에 푸시된 코드를 사용합니다. 처리기 편집에 대한 자세한 내용은 [생성된 처리기 사용자 지정](/help/guides/customize-handler.md)을 참조하세요.

#### 3단계: 배포 및 테스트 {#deploy-and-verify}

1. [앱을 **[!UICONTROL Workspace]**&#x200B;에서 선택한 동일한 환경에 배포](/help/guides/deploy-your-app.md)합니다.
2. `name`이(가) `Ada`(으)로 설정된 지원되는 LLM 플랫폼에서 작업을 호출합니다. [ChatGPT 플러그 인 테스트](/help/guides/test-in-chatgpt.md) 또는 [클라우드 커넥터 테스트](/help/guides/test-in-claude.md)를 참조하십시오.
3. 응답이 `Good day, Ada!`인지 확인하십시오. 응답은 처리기가 구성된 변수를 읽고 기본 인사말을 무시하는지 확인합니다.

다른 환경을 구성하려면 **[!UICONTROL Workspace]**&#x200B;에서 해당 환경을 선택하고 적절한 값으로 설정을 반복한 다음 앱을 배포하고 테스트하십시오.

>[!NOTE]
>
>단계 및 프로덕션에 **독립적인 구성**&#x200B;이 있습니다. 한 환경의 변경 내용이 다른 환경에 영향을 주지 **않습니다**. 필요한 경우 두 환경에서 동일한 이름을 사용하고 각각에 대해 적절한 값을 선택합니다.

>[!TIP]
>
>배포하기 전에 로컬에서 테스트하려면 [로컬 처리기 개발 및 테스트](/help/reference/development.md)를 참조하고 로컬 서버에 변수를 전달하십시오.
>
>`node server/local.js --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' --param GREETING_PREFIX=Howdy`

### 변수 업데이트 {#update-or-delete}

1. 변수 행에서 편집 컨트롤을 선택합니다.
2. 변수의 **[!UICONTROL 현재 값]**&#x200B;을 검토하고 **[!UICONTROL 새 값]**&#x200B;을 입력하십시오.

   ![GREETING_PREFIX 업데이트 — 값을 Good day에서 Howdy로 변경합니다](/help/assets/guide-app-variables/update-variable-dialog.png)

3. **[!UICONTROL 업데이트]**&#x200B;를 선택합니다.
4. 동일한 환경에 앱을 다시 배포하고 변경된 동작을 확인합니다.

변수를 업데이트하면 해당 값만 변경됩니다. **변수 이름을**&#x200B;할 수 없습니다. 다른 이름을 사용하려면 기존 변수를 삭제하고 새 변수를 추가한 다음 핸들러를 업데이트하여 새 이름을 읽습니다.

### 변수 삭제 {#delete-a-variable}

1. 작업에 변수가 계속 필요한지 확인합니다. 필요한 경우 **first** 처리기를 업데이트하고 푸시합니다.
2. 변수 행에서 삭제 컨트롤을 선택합니다. 여러 항목을 삭제하려면 해당 확인란을 선택하고 **[!UICONTROL 삭제]**&#x200B;를 선택합니다.
3. 확인 대화 상자에서 이름을 검토한 다음 **[!UICONTROL 삭제]**&#x200B;를 선택합니다.

   ![GREETING_PREFIX 삭제 — 영구 삭제 확인](/help/assets/guide-app-variables/delete-variable-dialog.png)

4. 앱을 동일한 환경에 다시 배포합니다.

>[!IMPORTANT]
>
>삭제를 **실행 취소할 수 없음**. 올바른 변수를 선택했는지 확인합니다. 배포된 앱은 다음 배포까지 기존 구성을 유지합니다. 배포한 후 처리기 **더 이상**&#x200B;이(가) 삭제된 변수를 받지 않으므로 변수를 필요로 하는 작업이 실패할 수 있습니다.

## 규칙 및 제한 {#good-to-know}

| 항목 | 규칙 |
|------|------|
| 이름 | 대문자, 숫자 및 밑줄(숫자로 시작하지 않음)을 최대 64자까지 사용할 수 있습니다. **Must**&#x200B;은(는) 앱 및 환경별로 고유해야 하며, 처리기와 일치해야 합니다. |
| 예약된 이름 | `LLMA_` 및 `MCP_SERVER_URL`(으)로 시작하는 이름. |
| 값 | **필수**, 최대 500자. 플랫폼에서는 선행 및 후행 공백을 제거합니다. |
| 제한 | 앱 및 환경당 50개 변수. |
| 가시성 | 변수 값은 표시 및 복사 가능합니다. 계획된 비밀 지원은 저장된 값을 숨깁니다. |
| 변경 사항 | 선택한 환경에 대한 다음 배포에 적용됩니다. |

## 문제 해결 {#verify-configuration}

| 표시되는 항목 | 할 일 |
|--------------|------------|
| **[!UICONTROL 추가]**&#x200B;이(가) 비활성화되고 페이지에 **[!UICONTROL Workspace 제한에 도달했습니다]**. | 더 이상 필요하지 않은 변수를 삭제합니다. |
| *대문자, 숫자 및 밑줄만 사용* | 이름 바꾸기(예: `API_BASE_URL`) |
| *플랫폼에서 이 이름을 예약했습니다* | `LLMA_`(으)로 시작하지 않고 `MCP_SERVER_URL`이(가) 아닌 이름을 선택하십시오. |
| *이 이름을 가진 변수가 이미 있습니다* | 대신 기존 변수를 업데이트합니다. |
| *이 변수가 다른 곳에서 수정되었습니다* | 페이지를 새로 고치고 다시 시도하십시오. |
| *변수를 로드할 수 없습니다* | 페이지를 다시 로드합니다. 오류가 지속되면 앱에 액세스할 수 있는지 확인하십시오. |
| 작업에서 새 값을 사용하지 않습니다. | 변수를 저장하는 **after**&#x200B;를 배포하고 동일한 환경을 테스트했는지 확인하십시오. 처리기 변경 내용을 푸시했으며 변수 이름이 **정확히**&#x200B;와(과) 일치하는지 확인하십시오. **[!UICONTROL 마지막 업데이트]**&#x200B;은(는) 앱을 배포한 때가 아니라 값을 저장한 때를 표시합니다. |
| `getVariable is not a function` | 앱에서 1.1.0 이전 버전의 런타임을 사용합니다. [시작하기 전에](#before-you-begin)에 설명된 대로 업그레이드한 다음 배포하십시오. |
| 변수를 삭제한 후 작업이 실패합니다 | 변수를 다시 추가하거나 더 이상 필요하지 않도록 핸들러를 업데이트한 다음 배포합니다. |

## 다음 단계 {#whats-next}

- [생성된 핸들러 사용자 정의](/help/guides/customize-handler.md)
- [앱 배포](/help/guides/deploy-your-app.md)
