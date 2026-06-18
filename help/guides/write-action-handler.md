---
title: 작업 핸들러 작성
description: 처리기 계약, structuredContent 및 작업 예제를 포함하여 Adobe LLM 앱의 작업 처리기를 작성하는 방법을 알아봅니다.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '719'
ht-degree: 0%

---


# 작업 핸들러 작성

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

[!DNL Adobe LLM Apps] UI에서 작업을 만든 후 메타데이터는 [!DNL LLM Apps] API에 저장되지만 아직 숨겨진 코드가 없습니다. 이 안내서에서는 LLM 플랫폼(예: [!DNL ChatGPT] 또는 Claude)이 작업을 호출할 때 실행되는 처리기 함수를 작성하는 과정을 안내합니다.

프로젝트 레이아웃, 로컬 개발 및 테스트 세부 정보는 [개발](/help/reference/development.md)을 참조하십시오.

## 개발자 계약

핸들러만 씁니다. 작업 이름, 설명, 입력 스키마, 주석, 위젯 가시성, 권한, CSP 등의 모든 항목은 [!DNL LLM Apps] UI에 있으며 배포 시 자동으로 런타임에 전달됩니다. 저장소에서 메타데이터를 직접 편집하거나 코드에서 도구를 등록하지 않습니다.

| 관심사 | Where it live |
|---------|----------------|
| 메타데이터(이름, 설명, 스키마, 위젯 설정) | [!DNL LLM Apps] UI — API에 저장됨 |
| 핸들러 코드(실행되는 함수) | [!DNL GitHub] 저장소 — `actions/<name>/index.js` |
| `actions.json`(메타데이터 스냅숏) | 배포 파이프라인에서 작성, 로컬 개발을 위해 UI에서 다운로드 |

## 시작하기

처리기를 작성하려면 연결된 저장소에 프로젝트 구조가 필요합니다. **[Adobe LLM 앱 상용구](https://github.com/Adobe-AIFoundations/llm-apps-boilerplate)**&#x200B;를 복제하여 빈 시작점으로 시작하십시오.

앱 생성 중에 연결한 리포지토리에 콘텐츠를 푸시합니다(예: `your-org/your-repo`).

코드가 설치되면 다음을 실행합니다.

```bash
npm install
```

MCP 프로토콜 통신, 작업 검색 및 요청 라우팅을 처리하는 런타임인 [`@adobe/llm-apps-runtime`](https://www.npmjs.com/package/@adobe/llm-apps-runtime)을(를) 포함한 모든 종속성을 설치합니다. 런타임과 직접 상호 작용하지 않습니다. 빌드 시 `entry.js`에서 사용합니다.

>[!TIP]
>
>[클라우드 코드](https://claude.ai/code) 또는 [커서](https://cursor.com)를 사용하는 경우 상용구에는 `.claude/skills/llm-apps-action-author/`에서 바로 사용할 수 있는 클라우드 기술이 포함됩니다. 편집기에서 새로운 작업을 스캐폴드하고, 테스트 파일을 생성하고, 핸들러 모양을 확인하고, 핸들러 계약을 안내할 수 있습니다. 사용하려면 Claude에게 &quot;*&quot;search-products&quot;*(이)라는 작업을 추가하도록 요청하십시오. 그러면 올바른 프로젝트 규칙을 자동으로 따릅니다.

## 핸들러 계약

처리기는 하나의 비동기 함수를 내보내는 `actions/<name>/index.js`의 단일 파일입니다.

```javascript
module.exports = async (args) => {
  return {
    content: [{ type: 'text', text: 'response for the LLM' }],
    structuredContent: { /* data for the widget */ }
  }
}
```

이 함수는 작업의 입력 인수를 일반 개체로 수신합니다. 이 인수는 작업 만들기 대화 상자에서 정의한 매개 변수입니다. 처리기가 호출되기 전에 서버에서 입력 스키마에 대해 유효성을 검사합니다.

### `content`(필수)

LLM 및 텍스트 전용 호스트로 전송되는 콘텐츠 부분 배열. 이는 LLM 플랫폼이 응답을 작성하기 위해 읽는 내용입니다.

```javascript
content: [
  { type: 'text', text: 'Found 5 products matching category "bagged-coffee".' }
]
```

항상 `content`을(를) 반환합니다. 모든 호스트의 범용 대체 항목입니다.

### `structuredContent`

위젯으로 전송된 일반 JavaScript 오브젝트입니다. 이 데이터에는 **0의 토큰 비용**&#x200B;이 있습니다. 제품 캐러셀 또는 맵과 같은 풍부한 UI를 렌더링하기 위해 EDS 위젯 블록에서 사용됩니다.

```javascript
structuredContent: {
  products: [
    { name: 'Product A', category: 'bagged-coffee', imageUrl: '...' },
    { name: 'Product B', category: 'bagged-coffee', imageUrl: '...' }
  ],
  total: 2,
  category: 'bagged-coffee'
}
```

구조는 사용자가 결정합니다. `bridge.toolResult`을(를) 통해 EDS 위젯 블록에 필요한 구조와 일치해야 합니다.

>[!IMPORTANT]
>
>`structuredContent`은(는) 기본 배열이 아닌 일반 개체여야 합니다.

### `_meta` (옵션)

결과와 함께 전송된 추가 메타데이터. `openai/widgetDescription` 키는 LLM 플랫폼에 위젯을 표시하는 방법을 알려줍니다.

```javascript
_meta: {
  'openai/widgetDescription': 'The widget displays a scrollable product carousel. '
    + 'Do NOT repeat the product list. Instead, highlight one or two recommendations.'
}
```

## 예: 제품 핸들러 검색

다음은 `search-products` 처리기 예입니다. 선택적 `category` 필터와 자유 텍스트 `query`을(를) 수락하고 제품 카탈로그를 검색한 다음 LLM에 대한 텍스트 요약과 위젯 캐러셀에 대한 구조화된 데이터를 모두 반환합니다.

>[!NOTE]
>
>이 예제에서는 단순성을 위해 하드코딩된 제품 어레이를 사용합니다. 실제 응용 프로그램에서는 일반적으로 자체 제품 API 또는 데이터베이스를 호출하여 결과를 동적으로 가져옵니다.

```javascript
// actions/search-products/index.js

const PRODUCTS = [
  {
    name: 'Product A',
    description: 'A short description of Product A.',
    category: 'bagged-coffee',
    sub_category: 'dark-roast',
    image_url: 'https://www.example.com/products/product-a/hero.jpg',
    url: 'https://www.example.com/products/product-a',
    productId: 'PROD-001',
    rating: 4.7,
    reviewCount: 58
  },
  // ... more products
];

const WIDGET_DESCRIPTION = 'The widget displays a scrollable product carousel '
  + 'with images, star ratings, and review counts. Do NOT repeat the product list.';

module.exports = async ({ category = '', query = '' } = {}) => {
  let results = PRODUCTS;

  if (category) {
    const categoryLower = category.toLowerCase();
    results = results.filter((p) =>
      p.category.toLowerCase().includes(categoryLower)
      || p.sub_category.toLowerCase().includes(categoryLower)
    );
  }

  if (query) {
    const queryLower = query.toLowerCase();
    results = results.filter((p) =>
      p.name.toLowerCase().includes(queryLower)
      || p.description.toLowerCase().includes(queryLower)
    );
  }

  const products = results.map((p) => ({
    productId: p.productId,
    name: p.name,
    shortDescription: p.description,
    category: p.category,
    rating: p.rating,
    reviewCount: p.reviewCount,
    imageUrl: p.image_url,
    productUrl: p.url,
  }));

  if (products.length === 0) {
    return {
      content: [{ type: 'text', text: `No products found for "${category}".` }],
      structuredContent: { products: [], total: 0, category: null },
      _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
    };
  }

  return {
    content: [
      { type: 'text', text: `Found ${products.length} product(s) in "${category}".` }
    ],
    structuredContent: { products, total: products.length, category },
    _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
  };
};
```

**런타임에 수행되는 작업:**

1. 사용자가 LLM 플랫폼 *에 &quot;커피 제품을 보여 주세요.&quot;*
2. LLM 플랫폼은 *제품 검색*&#x200B;의 의도와 일치하며 `category`을(를) 추출합니다.
3. MCP 서버가 `{ category: 'bagged-coffee' }`(으)로 처리기를 호출합니다.
4. 처리기가 카탈로그를 필터링하고 `content`(LLM의 텍스트 요약) + `structuredContent`(위젯의 제품 배열)을 반환합니다.
5. LLM 플랫폼은 텍스트 응답을 표시하고 구조화된 데이터를 EDS 위젯에 전달하여 제품 캐러셀을 렌더링합니다.

## 핸들러가 누락된 경우 어떻게 합니까?

UI에서 작업을 정의했지만 핸들러 파일을 아직 만들지 않은 경우 이 작업은 배포 시 여전히 등록됩니다. 호출에서는 실제 코드를 추가할 때까지 빈 콘텐츠를 반환하는 기본 스텁 처리기를 사용합니다. 즉, UI에서 먼저 모든 작업을 정의하고 증분 구현할 수 있습니다.

