---
title: 생성된 EDS 위젯 사용자 정의
description: Adobe LLM 앱에서 자동으로 생성된 Edge Delivery Services 위젯을 이해하고 사용자 정의합니다.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1065'
ht-degree: 0%
---

# 생성된 위젯 사용자 정의 {#customize-generated-widget}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

>[!NOTE]
>
>이 안내서에서는 Adobe EDS(Edge Delivery Services)에 대해 기본적으로 잘 알고 있다고 가정합니다. EDS를 처음 사용하는 경우 위젯을 사용자 지정하기 전에 [EDS 개발자 자습서](https://www.aem.live/developer/tutorial) 및 [블록 살펴보기](https://www.aem.live/docs/exploring-blocks)를 읽어 보십시오. 이러한 리소스는 블록, `decorate` 함수 및 EDS 프로젝트 구조를 포함합니다.

생성이 완료되면 플랫폼에서는 [!DNL LLM Apps]의 작업에 연결된 EDS 위젯을 제공합니다. 위젯은 미리보기를 위해 대표 데이터를 렌더링하고 지원되는 LLM 플랫폼에 연결된 경우 핸들러 결과를 읽습니다. 위젯을 사용자 정의하기 전에 생성된 디자인과 비헤이비어를 검토하십시오.

생성된 위젯을 테스트하여 시작합니다. 그런 다음 데이터 계약, 상호 작용 및 시각적 디자인을 사용자 지정합니다.

**이 안내서에서:** 생성된 위젯을 찾아 동작 데이터와 브랜드 디자인에 맞게 조정하는 방법을 알아봅니다. 그런 다음 변경 사항을 로컬에서 미리 보고 배포된 앱에서 테스트합니다.

## 생성된 위젯 찾기

[인프라 설정](/help/guides/create-app.md) 중에 연결된 EDS 저장소를 엽니다. Adobe 관리 인프라를 사용하는 경우 계속하기 전에 코드 액세스를 요청하십시오. 생성된 각 위젯은 EDS 블록입니다.

```text
blocks/
└── <action-slug>/
    ├── <action-slug>.js
    └── <action-slug>.css
```

- JavaScript 파일은 작업 결과를 읽고 인터페이스를 빌드합니다.
- CSS 파일은 레이아웃, 반응형 동작 및 시각적 디자인을 제어합니다.
- 생성된 가져오기 요청에는 작업에 대해 생성된 정확한 파일이 표시됩니다.

슬러그는 하이픈을 사용합니다. 코드 식별자가 `search_products`인 작업은 `blocks/search-products/`과(와) 같은 블록을 사용합니다. 생성된 위젯 페이지는 일반적으로 `/eds-widgets/search-products-demo`입니다. 작업 메타데이터에서 실제 **[!UICONTROL 위젯 URL]**&#x200B;을(를) 사용하십시오.

또한 플랫폼은 위젯 URL을 구성하고 SDK 파일을 지원합니다. 생성된 위젯을 사용자 정의하기 위해 두 번째 EDS 프로젝트를 생성하거나 위젯 URL을 다시 입력할 필요가 없습니다.

## LLM 앱 SDK이 위젯을 연결하는 방법

`@adobe/llmapps-sdk` 패키지는 EDS 위젯을 LLM 호스트에 연결합니다. 생성된 EDS 저장소의 해당 진입점은 다음과 같습니다.

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

`aem-embed.js`이(가) 호스트 연결을 설정하고 EDS 페이지를 로드한 다음 블록을 호출합니다.

```javascript
export default async function decorate(block, bridge) {
  // Customize the widget here.
}
```

임베드는 위젯 페이지의 `.plain.html` 콘텐츠를 가져오고 해당 블록을 가져옵니다. 블록에서 SDK을 가져오지 않습니다. 임베드는 `bridge`을(를) 자동으로 제공합니다. 일반 EDS 페이지 로드에서는 제공하지 않습니다. 브리지를 사용하면 위젯이 다음과 같은 작업을 수행할 수 있습니다.

- `bridge.toolResult`(으)로 처리기 결과를 읽습니다.
- `bridge.applyHostStyles()`(으)로 호스트 스타일을 적용합니다.
- `bridge.sendMessage()`님과 대화를 계속합니다.
- `bridge.callTool()`(으)로 다른 작업을 호출합니다.
- 크기를 `bridge.autoResize()`과(와) 동기화된 상태로 유지합니다.

이 안내서에서는 일반적인 브리지 방법을 다룹니다. 전체 API에 대해서는 [`@adobe/llmapps-sdk` 패키지](https://www.npmjs.com/package/@adobe/llmapps-sdk)를 참조하십시오.

## 데이터 계약 이해

작업 처리기가 `structuredContent`을(를) 반환하고 블록이 `bridge.toolResult`에서 읽습니다.

```javascript
// Handler result
return {
  content: [{ type: 'text', text: `Found ${records.length} products.` }],
  structuredContent: { arguments: { query: 'coffee' }, records }
};
```

```javascript
// EDS block
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];
  // Render records or an error state when result?.isError is true.
}
```

이 예제에서 `PREVIEW_RESULT`은(는) 블록의 로컬 샘플 데이터 개체입니다. 현재 생성된 위젯은 `structuredContent.arguments` 및 `structuredContent.records`을(를) 사용합니다. 생성된 파일에서 정확한 모양을 검사합니다. 빈 라이브 결과를 미리보기 레코드로 바꾸지 마십시오.

`bridge.toolResult`은(는) 첫 번째 결과로만 확인됩니다. 위젯이 마운트된 상태로 유지되므로 나중에 결과를 처리해야 하는 경우 `bridge.onToolResult(callback)`을(를) 사용합니다.

`structuredContent`을(를) 변경하면 처리기와 위젯을 함께 업데이트합니다. 구조화된 데이터가 호스트의 작업 결과에 속하므로 모델에서 숨겨져 있다고 가정하지 마십시오. 전체 반환 계약에 대해서는 [생성된 처리기 사용자 지정](/help/guides/customize-handler.md)을 참조하십시오.

## 외부 데이터를 안전하게 렌더링

처리기 출력을 신뢰할 수 없는 데이터로 처리합니다. `innerHTML`에 응답 값을 삽입하지 않고 `textContent`과(와) 같은 DOM API를 선호합니다.

```javascript
function createProductCard(product, bridge) {
  const card = document.createElement('article');
  card.className = 'product-card';

  const title = document.createElement('h3');
  title.textContent = String(product.name ?? 'Product');

  const button = document.createElement('button');
  button.type = 'button';
  button.textContent = 'Tell me more';
  button.disabled = !bridge?.isConnected;
  const status = document.createElement('p');
  status.setAttribute('role', 'status');
  button.addEventListener('click', async () => {
    if (bridge?.isConnected && typeof product.id === 'string') {
      try {
        await bridge.sendMessage(`Show me details for product ${product.id}`);
      } catch {
        status.textContent = 'Unable to send the request. Try again.';
      }
    }
  });

  card.append(title, button, status);
  return card;
}
```

URL을 `href` 또는 `src`에 할당하기 전에 유효성을 검사하고 경험에 필요한 프로토콜만 허용합니다.

## 호스트 브리지 사용

임베드가 브리지를 `decorate(block, bridge)`에 전달합니다. 직접 EDS 미리 보기 도중에도 블록이 렌더링되도록 `bridge?.isConnected`(으)로 대화형 호출을 보호합니다. 연결 없이 독립 실행형 모드로 브리지 개체가 있을 수 있습니다. 독립 실행형 모드로 `toolResult` 대기 중인 개체는 확인되지 않습니다. LLM 앱 미리 보기는 블록에서 샘플 데이터를 선택할 수 있도록 `bridge.hostContext.preview`도 설정합니다.

### 호스트 스타일 적용

```javascript
if (bridge?.isConnected) {
  bridge.applyHostStyles();
}
```

이 메서드는 사용 가능한 호스트 CSS 변수와 글꼴 스타일을 적용합니다. 호스트 스타일 적용은 선택 사항입니다. 생성된 위젯은 대신 자체 디자인 토큰을 사용할 수 있습니다. 포함 항목은 섀도 본문에서 `.light` 및 `.dark` 클래스를 유지합니다. 블록을 테마 변경에 반응시켜야 할 경우 `bridge.hostContext.theme` 및 `bridge.onContextChange(callback)`을(를) 사용합니다.

### 후속 메시지 보내기

```javascript
if (bridge?.isConnected) {
  await bridge.sendMessage('Show me similar products.');
}
```

상호 작용 후 대화를 계속하려면 `sendMessage`을(를) 사용하세요.

### 직접 작업 호출

```javascript
if (bridge?.isConnected) {
  const result = await bridge.callTool('search_products', { query: 'coffee' });
  // Re-render this widget from result.structuredContent, or show result.isError.
}
```

작업 결과가 필요한 명시적 상호 작용에 `callTool`을(를) 사용합니다. 블록 폴더 이름이나 표시 이름이 아닌 실제 대상 작업의 코드 식별자를 전달합니다. `search_products`이(가) 예입니다. 호스트는 작업 호출을 지원해야 하며 대상 작업은 앱 가시성을 허용해야 합니다. 작업을 호출해도 현재 위젯이 자동으로 다시 렌더링되지 않습니다. 검증된 값만 전달하고 내부 세부 정보를 노출하지 않고 거부된 호출을 처리합니다.

대상 작업의 가시성을 변경해야 하는 경우 **[!UICONTROL 작업 세부 정보]**&#x200B;에서 해당 작업만 편집하고 변경 내용을 저장한 다음 테스트 중인 환경에 다시 배포하십시오.

### 위젯 크기를 동기화된 상태로 유지

```javascript
if (bridge?.isConnected) {
  bridge.autoResize(block);
}
```

호스트가 콘텐츠 변경 내용에 응답할 수 있도록 하려면 초기 렌더링 후 `autoResize`을(를) 호출하십시오. 이 메서드는 위젯이 제거될 때 관찰을 중지하는 정리 함수를 반환합니다.

## 변경 사항 미리보기

생성된 블록에는 브리지가 누락되거나 연결이 끊겼거나 미리보기로 표시된 경우 직접 미리보기를 위한 샘플 데이터가 포함됩니다. 샘플 데이터 폴백을 라이브 빈 상태 및 오류 상태와 별도로 유지합니다.

로컬에서 EDS 프로젝트를 미리 보려면 다음 작업을 수행하십시오.

```bash
npm install -g @adobe/aem-cli
aem up
```

사이트 루트뿐만 아니라 로컬 서버(예: `http://localhost:3000/eds-widgets/search-products-demo`)의 작업 위젯 URL에서 경로를 엽니다. 확인:

- 비어 있음, 로드, 성공 및 오류 상태.
- 긴 텍스트와 누락된 옵션 필드.
- 키보드 탐색 및 보이는 포커스.
- 밝은 테마와 어두운 테마.
- 좁고 넓은 레이아웃.

직접 EDS 미리 보기는 호스트 핸드셰이크나 라이브 작업 데이터를 테스트하지 않습니다. 지원되는 LLM 플랫폼에서 EDS 변경 사항을 게시하고 라이브 `structuredContent`을(를) 사용하여 테스트합니다.

## 사용자 지정 게시

1. EDS 변경 사항을 커밋하고 푸시합니다.
2. 구성된 EDS URL에서 위젯 페이지와 변경된 스크립트 및 스타일을 사용할 수 있는지 확인합니다. EDS 게시 작업 과정을 통해 콘텐츠 변경 사항을 게시합니다.
3. 핸들러 또는 작업 메타데이터를 변경한 경우 해당 변경 사항을 푸시하고 앱을 스테이지에 배포합니다.
4. 지원되는 LLM 플랫폼에서 작업 및 위젯을 테스트합니다. [Test in ChatGPT](/help/guides/test-in-chatgpt.md) 또는 [Test in Cloud](/help/guides/test-in-claude.md)를 참조하십시오.
5. 테스트가 성공하면 필요에 따라 핸들러 및 메타데이터 변경 사항을 프로덕션에 배포합니다.

EDS 게시는 앱 배포와 별개입니다. 스테이지 및 프로덕션이 동일한 EDS URL을 사용하는 경우 해당 URL에서 위젯 변경 사항은 두 환경 모두에 영향을 미칩니다. 스테이지에 배포하면 격리된 위젯 사본은 생성되지 않습니다.

## 기타 EDS 설정

직접 작업을 만들었거나 호환되는 기존 EDS 사이트를 통합하려는 경우 [자체 EDS 프로젝트 가져오기](/help/guides/bring-your-own-eds.md)를 참조하십시오.
