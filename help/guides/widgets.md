---
title: 위젯(EDS) 설정
description: Edge Delivery Services 위젯 프로젝트를 설정하고 LLM 플랫폼 내에서 시각적 응답을 렌더링하기 위한 블록 계약을 구현하는 방법에 대해 알아봅니다.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '1214'
ht-degree: 1%

---


# 위젯(EDS) 설정

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다. 여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다.

이 안내서에서는 [!DNL LLM Apps] UI에서 작업 구성부터 EDS 프로젝트 설정, LLM 플랫폼 내에서 데이터를 렌더링하는 블록 코드 작성에 이르기까지 EDS 위젯을 빌드하는 방법에 대해 설명합니다. 자세한 내용은 [핵심 개념](/help/overview/overview.md#widgets-eds)을 참조하세요.

## [!DNL LLM Apps] SDK

모든 항목은 [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk) npm 패키지로 시작합니다. SDK은 위젯과 LLM 호스트 간의 양방향 통신 채널을 구동하는 JavaScript 라이브러리입니다.

SDK은 또한 SDK을 표준 EDS 블록 파이프라인에 연결하는 EDS 관련 진입점인 `aem-embed.js`을(를) 제공합니다. `npm install @adobe/llmapps-sdk`을(를) 실행하면 사후 설치 스크립트는 두 개의 파일을 프로젝트에 자동으로 복사합니다.

```
scripts/
└── llm-apps/
    ├── aem-embed.js     ← EDS widget entry point, ships with the SDK
    └── llmapps-sdk.js   ← core SDK, loaded internally by aem-embed.js
```

EDS 프로젝트에서 **블록 코드에서 직접 SDK을 사용하지 않습니다.** `aem-embed.js`은(는) SDK 연결을 만들고 관리하며 완전히 연결된 `LLMApp` 인스턴스를 `decorate(block, bridge)`의 `bridge` 인수로 블록에 전달합니다. 전체 SDK API를 `bridge`에서 사용할 수 있습니다. 가져올 필요가 없습니다.

EDS **(표준 번들러 또는 TypeScript 프로젝트) 없이**&#x200B;위젯을 빌드하고 있는 경우 SDK을 직접 사용할 수 있습니다.

```javascript
import { LLMApp } from '@adobe/llmapps-sdk';

const app = new LLMApp({ appInfo: { name: 'MyWidget', version: '1.0.0' } });
await app.connect();

const { structuredContent } = await app.toolResult;
```

## 모든 것이 어떻게 조화되는지

AI가 작업을 호출하고 핸들러가 `structuredContent`을(를) 반환하면 LLM 플랫폼은 대화에서 대화형 위젯을 렌더링합니다. 이 작업을 함께 수행할 수 있는 세 가지 방법이 있습니다.

**[!DNL LLM Apps] UI** — 작업을 만들 때 [위젯 메타데이터] 탭에 **[!UICONTROL 스크립트 URL]** 및 **[!UICONTROL 위젯 URL]**&#x200B;을(를) 입력합니다. 스크립트 URL은 `aem-embed.js`(SDK과 함께 제공되는 파일)을 가리키며 `scripts/llm-apps/aem-embed.js`의 EDS 저장소에 있습니다. 작업이 호출될 때 로드할 스크립트를 LLM 플랫폼에 알려줍니다.

**`aem-embed.js`** — LLM 플랫폼은 이 스크립트를 샌드박스를 작동하는 위젯 표면에 로드합니다. `aem-embed.js`은(는) 위젯의 EDS 인식 진입점 역할을 하는 사용자 지정 HTML 요소(`<aem-embed>`)입니다. SDK을 사용하여 LLM 호스트와의 핸드셰이크를 수행하고, 일반 EDS 페이지 파이프라인(머리글/바닥글 없음)을 억제하고, 위젯 URL에서 EDS 페이지 콘텐츠를 가져오고, EDS 블록 파이프라인을 실행하고, 각 블록의 `decorate()` 함수에 라이브 `bridge` 개체를 전달합니다.

**블록 코드** — `decorate(block, bridge)` 함수를 내보내는 표준 EDS 블록을 작성합니다. `bridge`은(는) 연결된 SDK 인스턴스입니다. 작업의 구조화된 결과를 제공하고 대화에 메시지를 다시 보낼 수 있습니다.

## 기존 EDS 프로젝트에 추가

이미 EDS 프로젝트가 있는 경우 블록 쓰기를 시작하려면 두 단계만 거치면 됩니다.

1. `@adobe/llmapps-sdk` 설치. 설치 후 스크립트는 `aem-embed.js` 및 `llmapps-sdk.js`을(를) `scripts/llm-apps/`에 복사합니다.

   ```bash
   npm install @adobe/llmapps-sdk
   ```

2. LLM 플랫폼이 원본 간에 위젯 페이지와 스크립트를 로드할 수 있도록 CORS 헤더를 구성하십시오. 아래 [CORS 헤더 구성](#configure-cors-headers)을 참조하십시오.

그런 다음 [`decorate(block, bridge)` 계약](#the-decorateblock-bridge-contract)에 따라 블록을 만들고, 위젯 페이지를 작성하고, [작업 만들기] 대화 상자에 URL을 입력하십시오.

## 새 EDS 프로젝트 설정

### 저장소 만들기

1. [AEM boilerplate](https://github.com/adobe/aem-boilerplate) 템플릿을 기반으로 새 [!DNL GitHub] 리포지토리를 만듭니다.
2. 저장소에 [AEM 코드 동기화 GitHub 앱](https://github.com/apps/aem-code-sync)을 추가합니다.
3. 로컬 개발을 위해 AEM CLI를 설치합니다. `npm install -g @adobe/aem-cli`.
4. `@adobe/llmapps-sdk` 설치. 설치 후 스크립트는 `aem-embed.js` 및 `llmapps-sdk.js`을(를) `scripts/llm-apps/`에 복사합니다.

   ```bash
   npm install @adobe/llmapps-sdk
   ```

EDS 프로젝트에 대한 전체 안내서는 [AEM 개발자 자습서](https://www.aem.live/developer/tutorial) 및 [프로젝트 구조](https://www.aem.live/developer/anatomy-of-a-project)를 참조하십시오.

설정되면 다음 위치에서 EDS 사이트를 사용할 수 있습니다.

- **미리보기:** `https://main--<repo>--<owner>.aem.page/`
- **라이브:** `https://main--<repo>--<owner>.aem.live/`

### 저장소 구조

```
my-brand-eds/
├── scripts/
│   ├── llm-apps/
│   │   ├── aem-embed.js           # Widget entry point — copied by post-install
│   │   └── llmapps-sdk.js         # Core SDK — copied by post-install
│   ├── aem.js                     # AEM core library
│   └── scripts.js                 # Site-level decoration and loading
├── blocks/
│   └── search-products/           # One folder per widget block
│       ├── search-products.js
│       └── search-products.css
├── styles/
│   └── styles.css
├── head.html
└── package.json
```

### CORS 헤더 구성

EDS 위젯 페이지는 LLM 플랫폼을 통해 샌드박스를 작동하는 위젯 표면 내부에 로드됩니다. 호스트가 위젯 콘텐츠 원본 간 가져오기를 수행할 수 있도록 EDS 사이트는 올바른 `access-control-allow-origin` 헤더를 반환해야 합니다.

헤더는 [구성 서비스](https://aem.live/docs/config-service-setup)를 사용하여 `admin.hlx.page`의 AEM 관리 패널을 통해 구성됩니다. 위젯 페이지 및 SDK 스크립트가 존재하는 경로에 대해 사용자 정의 응답 헤더를 추가합니다.

```json
{
  "/<your-widget-pages-path>/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ],
  "/scripts/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ]
}
```

>[!NOTE]
>
>`.aem.live` 도메인의 공용 위젯 콘텐츠에는 `*`을(를) 원본 값으로 사용할 수 있습니다. 사이트에 보호된 콘텐츠가 포함되어 있는 경우 원본을 특정 도메인으로 제한합니다.

### 위젯 페이지 만들기

EDS 제작 도구에서 페이지를 만들고 블록을 추가합니다. 페이지 URL은 액션과 블록 간의 유일한 연결인 액션에서 사용자가 구성하는 **[!UICONTROL 위젯 URL]**&#x200B;이 됩니다. 블록과 작업 이름 사이에는 이름 지정 요구 사항이 없습니다.

![EDS 작성 — 위젯 페이지에 블록 추가됨](/help/assets/guide-widget/aem-author.png)

### 작업 만들기 대화 상자에 URL을 입력합니다

EDS 저장소를 설정한 후 작업을 만들 때 **위젯 메타데이터 → 템플릿 URL**(으)로 이동합니다.

**[!UICONTROL 스크립트 URL]** — EDS 저장소의 `aem-embed.js`을(를) 가리킵니다. 이 값은 동일한 EDS 프로젝트의 모든 작업에 대해 동일한 값입니다.

```
https://main--<repo>--<owner>.aem.live/scripts/llm-apps/aem-embed.js
```

**[!UICONTROL 위젯 URL]** — 이 위젯에 대해 만든 EDS 페이지의 URL입니다. 작업당 고유:

```
https://main--<repo>--<owner>.aem.live/<path-to-your-widget-page>
```

LLM 플랫폼은 스크립트 URL에서 `aem-embed.js`을(를) 로드합니다. 그런 다음 `aem-embed.js`이(가) 위젯 URL에서 `.plain.html`을(를) 가져와서 차단 콘텐츠를 가져옵니다.

## 데이터 흐름

핸들러에서 렌더링된 위젯으로의 전체 경로:

1. **Action 처리기**&#x200B;이(가) `structuredContent`을(를) 반환합니다.

```javascript
// actions/search-products/index.js
return {
  structuredContent: {
    products: [
      { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
      { id: 'COF-002', name: 'Colombia Huila Natural', price: '$22', rating: 4.5 },
    ],
    total: 2,
    category: 'coffee'
  }
};
```

1. **LLM 플랫폼**&#x200B;이 위젯 표면을 열고 스크립트 URL에서 `aem-embed.js`을(를) 로드합니다.

1. **`aem-embed.js`**&#x200B;이(가) SDK을 통해 호스트에 연결하고, 위젯 URL에서 `.plain.html`을(를) 가져오고, EDS 블록 파이프라인을 실행하고, 블록에서 `decorate(block, bridge)`을(를) 호출합니다.

1. **블록을**&#x200B;에서 `bridge.toolResult`의 데이터를 읽고 UI를 렌더링합니다.

1. **사용자 상호 작용**&#x200B;이(가) `bridge.sendMessage(...)` 또는 `bridge.callTool(...)`을(를) 트리거하여 대화에 대한 후속 작업을 보냅니다.

## `decorate(block, bridge)` 계약

모든 EDS 위젯 블록에서 기본 `decorate` 함수를 내보내야 합니다. 두 번째 인수, 연결된 `bridge`(전체 API가 있는 [`LLMApp`](https://www.npmjs.com/package/@adobe/llmapps-sdk) SDK 인스턴스)로 확장된 표준 EDS 블록 서명입니다.

```javascript
export default async function decorate(block, bridge) {
  // ...
}
```

`bridge`은(는) LLM 플랫폼 위젯 표면 내에서 실행할 때만 존재합니다. 브라우저 또는 로컬 개발 서버에서 직접 미리 볼 때도 블록이 렌더링되도록 항상 브리지 호출을 보호합니다.

### 작업 결과에서 데이터 렌더링

`bridge.toolResult`은(는) `structuredContent`을(를) 포함하여 핸들러가 반환한 전체 결과로 확인되는 약속입니다.

```javascript
const SAMPLE_PRODUCTS = [
  { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
];

export default async function decorate(block, bridge) {
  let products = SAMPLE_PRODUCTS;

  if (bridge) {
    const result = await bridge.toolResult;
    products = result?.structuredContent?.products ?? [];
  }

  block.innerHTML = products.map(p => `
    <div class="product-card">
      <h3>${p.name}</h3>
      <p class="price">${p.price}</p>
      <button data-id="${p.id}">Tell me more</button>
    </div>
  `).join('');
}
```

### 호스트 테마 적용

`decorate` 초기에 `bridge.applyHostStyles()`을(를) 호출하여 호스트의 CSS 변수와 글꼴(밝은/어두운 테마, 타이포그래피)을 위젯에 삽입합니다. 이렇게 하면 위젯이 주변 LLM 플랫폼 UI와 시각적으로 일관되게 유지됩니다.

```javascript
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }
  // ...
}
```

런타임 시 테마 변경 사항에 반응하려면(예: 사용자가 밝은 모드와 어두운 모드 간을 전환하는 경우) 다음을 수행합니다.

```javascript
if (bridge) {
  bridge.onContextChange(ctx => {
    block.dataset.theme = ctx.theme; // 'light' | 'dark'
  });
}
```

### 후속 메시지 보내기

`bridge.sendMessage(text)`이(가) 사용자 메시지를 대화에 삽입합니다. 위젯이 추가 AI 상호 작용을 트리거하는 기본 방법입니다(예: 사용자가 제품 카드를 클릭하여 세부 정보를 요청하는 경우).

```javascript
block.querySelectorAll('button[data-id]').forEach(btn => {
  btn.addEventListener('click', () => {
    bridge.sendMessage(`Show me details for product ${btn.dataset.id}`);
  });
});
```

### 다른 작업을 직접 호출

`bridge.callTool(name, args)`은(는) 사용자 메시지를 거치지 않고 위젯 내에서 다른 작업을 호출합니다. 온디맨드로 관련 데이터를 로드하는 데 유용합니다.

```javascript
btn.addEventListener('click', async () => {
  const result = await bridge.callTool('get-product-details', { id: product.id });
  renderDetails(result.structuredContent);
});
```

### 위젯 자동 크기 조정

LLM 플랫폼은 사용자가 보고한 내용에 따라 위젯의 크기를 조정합니다. 콘텐츠가 변경될 때 위젯 높이를 동기화하려면 `bridge.autoResize(element)`을(를) 사용합니다. 내부적으로 `ResizeObserver`을(를) 사용합니다. 초기 렌더링 후 호출합니다.

```javascript
export default async function decorate(block, bridge) {
  // ... render content ...

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

또는 고정 크기를 수동으로 보고합니다.

```javascript
bridge.reportSize(block.offsetWidth, block.offsetHeight);
```

### 미리보기 모드 및 로컬 개발

브라우저 또는 로컬 개발 서버에서 직접 EDS 페이지를 미리 볼 때 `bridge`은(는) `undefined`입니다. 라이브 핸들러 없이 블록이 즉시 렌더링되도록 위에 표시된 샘플 데이터 폴백 패턴을 사용하십시오.

로컬 개발 서버를 시작하려면 다음을 수행하십시오.

```bash
npm install -g @adobe/aem-cli
aem up
```

위젯 페이지로 이동하여 샘플 데이터로 렌더링되는 블록을 확인할 수 있는 `http://localhost:3000`이 열립니다. JS 및 CSS 차단에 대한 변경 사항은 즉시 반영됩니다.

## 다음 단계

- [안내서: 작업 핸들러 작성](/help/guides/write-action-handler.md)

