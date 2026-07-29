---
title: 생성된 EDS 위젯 사용자 정의
description: Adobe LLM 앱 온보딩 에이전트에서 생성한 Edge Delivery Services 위젯을 이해하고 사용자 정의합니다.
source-git-commit: 4c259a4587c0a84bb634a9a56c043dfe1cfc31fb
workflow-type: tm+mt
source-wordcount: '650'
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
>이 안내서에서는 Adobe EDS(Edge Delivery Services)에 대해 기본적으로 잘 알고 있다고 가정합니다. EDS를 처음 사용하는 경우 위젯을 사용자 지정하기 전에 먼저 [EDS 개발자 자습서](https://www.aem.live/developer/tutorial) 및 [블록 살펴보기](https://www.aem.live/docs/exploring-blocks)를 읽고 필수 사항(블록, `decorate` 함수 및 EDS 프로젝트 구조)을 알아보십시오.

온보딩 에이전트는 생성된 모든 작업에 대한 EDS 위젯을 생성합니다. 위젯이 이미 작업 결과를 수신하고, 샘플 데이터를 렌더링하고, 호스트 스타일을 적용하고, [!DNL LLM Apps]의 작업에 연결되어 있습니다.

생성된 위젯을 테스트하여 시작합니다. 그런 다음 데이터 계약, 상호 작용 및 시각적 디자인을 사용자 지정합니다.

**여정:** 생성된 블록→ 찾아 데이터 계약을 조정하고 로컬로 미리 보기→ 안전하게 → 배포 → 테스트합니다.

## 생성된 위젯 찾기

앱을 만들 때 선택한 EDS 저장소를 엽니다. 생성된 각 위젯은 EDS 블록입니다.

```text
blocks/
└── <action-name>/
    ├── <action-name>.js
    └── <action-name>.css
```

- JavaScript 파일은 작업 결과를 읽고 인터페이스를 빌드합니다.
- CSS 파일은 레이아웃, 반응형 동작 및 시각적 디자인을 제어합니다.
- 생성된 가져오기 요청에는 작업에 대해 생성된 정확한 파일이 표시됩니다.

또한 온보딩 에이전트는 위젯 URL을 구성하고 SDK 파일을 지원합니다. 생성된 위젯을 사용자 정의하기 위해 두 번째 EDS 프로젝트를 생성하거나 해당 값을 다시 입력할 필요가 없습니다.

## LLM 앱 SDK이 위젯을 연결하는 방법

`@adobe/llmapps-sdk` 패키지는 EDS 위젯을 LLM 호스트에 연결합니다. 생성된 EDS 저장소에는 다음이 포함됩니다.

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

블록에서 SDK을 가져오지 않습니다. 연결된 `bridge`이(가) 자동으로 제공됩니다. 이를 통해 위젯은 다음과 같은 작업을 수행할 수 있습니다.

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
  content: [{ type: 'text', text: `Found ${products.length} products.` }],
  structuredContent: { products, total: products.length }
};
```

```javascript
// EDS block
export default async function decorate(block, bridge) {
  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];
  // Render products.
}
```

`structuredContent`을(를) 변경하면 처리기와 위젯을 함께 업데이트합니다. 전체 반환 계약에 대해서는 [생성된 처리기 사용자 지정](/help/guides/customize-handler.md)을 참조하십시오.

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
  button.addEventListener('click', () => {
    if (bridge && product.id) {
      bridge.sendMessage(`Show me details for product ${String(product.id)}`);
    }
  });

  card.append(title, button);
  return card;
}
```

URL을 `href` 또는 `src`에 할당하기 전에 유효성을 검사하고 경험에 필요한 프로토콜만 허용합니다.

## 호스트 브리지 사용

EDS는 연결된 브리지를 `decorate(block, bridge)`에 전달합니다. 직접 EDS 미리 보기 중에도 블록이 렌더링되도록 보호 브리지가 호출됩니다.

### 호스트 스타일 적용

```javascript
if (bridge) {
  bridge.applyHostStyles();
}
```

호스트 타이포그래피 및 테마 변수를 적용합니다. 위젯 CSS는 밝은 호스트 테마와 어두운 호스트 테마를 모두 지원해야 합니다.

### 후속 메시지 보내기

```javascript
await bridge.sendMessage('Show me similar products.');
```

상호 작용에서 대화를 계속해야 하는 경우 `sendMessage`을(를) 사용합니다.

### 다른 작업 호출

```javascript
const result = await bridge.callTool('get-product-details', {
  id: product.id
});
```

다른 작업 결과가 필요한 명시적 상호 작용에 `callTool`을(를) 사용합니다. 내부 세부 정보를 노출하지 않고 검증된 값만 전달하고 실패를 처리합니다.

### 위젯 크기를 동기화된 상태로 유지

```javascript
if (bridge) {
  bridge.autoResize(block);
}
```

호스트가 콘텐츠 변경에 응답할 수 있도록 초기 렌더링 후 `autoResize`을(를) 호출합니다.

## 변경 사항 미리보기

`bridge`을(를) 사용할 수 없는 경우 생성된 블록에는 직접 미리 보기를 위한 샘플 데이터가 포함되어야 합니다.

로컬에서 EDS 프로젝트를 미리 보려면 다음 작업을 수행하십시오.

```bash
npm install -g @adobe/aem-cli
aem up
```

`http://localhost:3000`에서 생성된 위젯 페이지를 엽니다. 확인:

- 비어 있음, 로드, 성공 및 오류 상태.
- 긴 텍스트와 누락된 옵션 필드.
- 키보드 탐색 및 보이는 포커스.
- 밝은 테마와 어두운 테마.
- 좁고 넓은 레이아웃.

그런 다음 LLM 플랫폼에서 앱을 스테이징에 배포하고 라이브 `structuredContent`을(를) 사용하여 테스트합니다.

## 사용자 지정 게시

1. EDS 변경 사항을 커밋하고 푸시합니다.
2. 데이터 모양을 변경한 경우 일치하는 핸들러 변경 사항을 커밋하고 푸시합니다.
3. 앱을 스테이징에 배포합니다.
4. [!DNL ChatGPT]에서 작업 및 위젯을 테스트합니다.
5. 검증된 버전을 프로덕션으로 승격합니다.

## 기타 EDS 설정

온보딩 에이전트를 사용하지 않았거나 기존 EDS 사이트를 통합하려는 경우 [자체 EDS 프로젝트 가져오기](/help/guides/bring-your-own-eds.md)를 참조하십시오.
