---
title: 처음부터 작업 만들기
description: 작업 메타데이터를 정의하고, 핸들러를 구현하고, EDS 위젯을 연결하고, 테스트하고, Adobe LLM 앱과 함께 배포합니다.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1453'
ht-degree: 0%
---

# 처음부터 작업 만들기 {#create-action-from-scratch}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

>[!NOTE]
>
>이 안내서에서는 Adobe EDS(Edge Delivery Services)에 대해 기본적으로 잘 알고 있다고 가정합니다. EDS를 처음 사용하는 경우 위젯에 연결하기 전에 먼저 [EDS 개발자 자습서](https://www.aem.live/developer/tutorial) 및 [블록 살펴보기](https://www.aem.live/docs/exploring-blocks)를 읽고 필수 사항(블록, `decorate` 함수 및 EDS 프로젝트 구조)을 알아보십시오.

플랫폼에서 생성하지 않은 기능을 추가합니다. [!DNL LLM Apps]에서 작업을 정의하고 연결된 저장소에 해당 처리기를 작성한 다음 필요한 경우 위젯을 추가합니다.

**이 안내서:** 동작을 정의하고 해당 처리기를 구현하며 선택적 위젯을 연결하는 방법을 알아봅니다. 작업을 로컬에서 테스트하고 앱을 배포한 다음 지원되는 LLM 플랫폼에서 작업을 시도하십시오.

첫 번째 앱의 경우 [인프라 설정](/help/guides/create-app.md)으로 시작한 다음 [작업 만들기](/help/guides/creating-actions.md)로 시작하십시오. 작업 만들기 안내서에서는 웹 사이트에서 자동 생성 및 수동 만들기에 대해 설명합니다.

## 시작하기에 앞서

다음이 필요합니다.

- 기존 LLM 앱.
- 연결된 처리기 저장소입니다.
- 종속성이 설치된 저장소의 로컬 복제
- 작업에 위젯이 표시되는 경우 EDS 프로젝트입니다.
- 프로덕션 결과에 대한 명확한 API 또는 데이터 소스.

## 작업 계획

하나의 명확한 사용자 작업을 중심으로 각 작업을 디자인합니다. UI를 열기 전에 다음을 정의합니다.

- **의도** — 사용자가 수행하려는 작업입니다.
- **설명** — 작업을 선택할 때입니다.
- **입력** — 사용자에게 필요한 최소 정보입니다.
- **결과** — 처리기에서 반환된 텍스트 및 구조적 데이터입니다.
- **동작** — 작업에서 데이터를 읽는지, 데이터를 변경하는지, 외부 시스템을 호출하는지 여부를 지정합니다.
- **위젯** — 결과에 시각적 인터페이스가 필요한지 여부.

예를 들어 **제품 검색** 작업에 이 계획을 사용합니다.

```text
Intent: Find products matching a category or search phrase
Inputs:
  category: optional string
  query: optional string
Result:
  content: text summary
  structuredContent: products and total count
Behavior: read-only, idempotent, open-world
Widget: product cards
```

관련된 작업이지만 다른 작업은 별도로 유지합니다. 작업에 입력, 위험 및 확인 요구 사항이 다르므로 제품 검색 및 제품 구매에 대해 별도의 작업을 사용하십시오.

## 작업 메타데이터 만들기

앱을 열고 **[!UICONTROL 작업]**&#x200B;을 선택한 다음 **[!UICONTROL 작업 만들기]**&#x200B;를 선택합니다.

만들기 대화 상자에 **[!UICONTROL 작업]** 및 **[!UICONTROL 위젯 메타데이터]** 탭이 있습니다. 각 섹션을 확장하여 해당 필드를 구성합니다.

### 기본 정보 입력

![작업 만들기 — 기본 정보](/help/assets/guide-create-action/action-basic-info.png)

다음을 입력합니다.

- **[!UICONTROL 작업 이름]** — *제품 검색*&#x200B;과 같은 짧은 작업 이름.
- **[!UICONTROL 설명]** - 작업을 사용할 시점과 반환된 내용을 설명합니다.

대화 상자는 `search-products`과(와) 같은 이름에서 **[!UICONTROL 코드 식별자]**&#x200B;을(를) 파생합니다. 식별자는 만든 후에 수정되며, 앱 내에서 고유해야 합니다. 핸들러 폴더에 정확한 식별자를 사용하십시오.

유용한 설명은 다음과 같습니다.

```text
Search the product catalog by category or keyword. Returns matching
products with their names, prices, categories, and image URLs.
```

*제품 정보를 가져옵니다*&#x200B;와 같이 모호한 설명을 사용하지 마십시오. LLM 플랫폼은 설명을 사용하여 작업 중에서 선택합니다.

### 주석 선택

주석은 작업의 동작을 설명합니다.

- **파괴적 힌트** - 작업을 통해 데이터를 삭제하거나 영구적으로 변경할 수 있습니다.
- **Idempotent(동일한 인수 = 추가 효과 없음)** — 동일한 요청을 반복하면 동일한 효과가 있습니다.
- **Open world 힌트** - 작업이 외부 시스템과 통신합니다.
- **읽기 전용 힌트** — 작업이 데이터를 변경하지 않습니다.

참인 주석만 선택합니다. 예를 들어 제품 검색은 일반적으로 읽기 전용, idempotent 및 open-world입니다.

### OpenAI 메타데이터 추가

작업이 실행되는 동안과 완료된 후에 표시되는 짧은 메시지 입력:

```text
Invoking: Searching products...
Invoked: Products found
```

위젯이 있는 작업의 경우 **[!UICONTROL 위젯 설명]**&#x200B;을 추가하십시오. 위젯 설명은 작업 설명과 다릅니다.

- **작업 설명**&#x200B;은(는) 모델이 작업을 호출할 시기를 결정하는 데 도움이 됩니다.
- **위젯 설명**&#x200B;은(는) `_meta["openai/widgetDescription"]`에 매핑되고 렌더링된 구성 요소에 표시되는 내용을 요약하므로 반복되는 설명이 줄어듭니다.

[!DNL LLM Apps]은(는) 위젯 설명을 구성 요소 메타데이터로 적용합니다. 핸들러에서 설명을 반환하지 마십시오.

### 가시성 구성

- **[!UICONTROL AI 모델에 표시]**&#x200B;하여 모델이 작업을 선택할 수 있습니다.
- **[!UICONTROL 앱 표면에 위젯으로 표시]**&#x200B;에 구성된 위젯이 표시됩니다.

텍스트 전용 작업의 경우 **[!UICONTROL 위젯 메타데이터]** > **[!UICONTROL 유형]**&#x200B;에서 **[!UICONTROL 위젯 없음]**&#x200B;을(를) 선택하십시오. 가시성 컨트롤은 작업의 위젯 유형을 선택하지 않습니다.

### 입력 매개 변수 추가

핸들러가 허용하는 각 값에 대해 하나의 매개 변수를 추가합니다. 모든 매개 변수에는 다음이 필요합니다.

- **이름** — 핸들러가 받는 키입니다.
- **유형** — 문자열, 숫자, 정수 또는 부울.
- **설명** — 값을 추출하는 방법입니다.
- **필수** — 작업에 값이 필요한지 여부입니다.

**제품 검색**&#x200B;의 경우:

```text
category
  Type: String
  Required: No
  Description: Product category used to narrow the catalog.

query
  Type: String
  Required: No
  Description: Product name or search phrase.
```

안정적인 매개 변수 이름을 사용하십시오. 이름을 변경하려면 핸들러 및 해당 테스트도 변경해야 합니다.

### 분석 구성

Analytics에서 작업을 유도한 대화의 요약을 포함하려면 **[!UICONTROL 사용자 의도 수집]**&#x200B;을 사용하도록 설정하십시오.

사용자 의도 컬렉션은 기본적으로 활성화되어 있습니다. 요약을 요청하는 지침이 포함된 **[!UICONTROL 사용자 의도 설명]**&#x200B;을 검토하십시오. 지침을 민감하지 않고 익명화된 의도로 제한하고 개인정보 처리방침에서 데이터 수집을 설명합니다. 설명에는 최대 2,000자를 사용할 수 있습니다.

![작업 만들기 — 사용자 의도 분석](/help/assets/guide-create-action/action-analytics-user-intent.png)

전체 필드 정의는 [작업 및 위젯 필드](/help/reference/reference-docs.md)를 참조하세요.

## 위젯 구성

텍스트 전용 작업의 경우 **[!UICONTROL 위젯 메타데이터]**&#x200B;를 열고 **[!UICONTROL 유형]**&#x200B;을(를) **[!UICONTROL 위젯 없음]**(으)로 설정하십시오. 그런 다음 나머지 위젯 설정을 건너뜁니다.

**[!UICONTROL 위젯 메타데이터]**&#x200B;을(를) 엽니다.

![작업 만들기 — 위젯 메타데이터](/help/assets/guide-create-action/widget-metadata.png)

다음을 구성하십시오.

- **[!UICONTROL 유형]** — 대화형 위젯에 대해 **[!UICONTROL Edge Delivery Services 블록]**&#x200B;을 선택합니다.
- **[!UICONTROL 위젯 도메인]** — 위젯의 샌드박스 원본. UI는 앱 제출에 필요하고 앱별로 고유함을 식별합니다. EDS 페이지 URL과 동일하지 않습니다.
- **테두리 선호** — 호스트에서 테두리 컨테이너를 요청합니다.

**[!UICONTROL 템플릿 URL]**&#x200B;을(를) 확장하고 다음을 입력하십시오.

- **[!UICONTROL 스크립트 URL]** — EDS 위젯 진입점입니다.
- **[!UICONTROL 위젯 URL]** — 이 작업에 대해 게시된 EDS 페이지입니다.

![새 작업 만들기 — EDS 위젯에 대한 스크립트 및 위젯 URL](/help/assets/guide-create-action/widget-template-urls.png)

일반적인 URL은 다음과 같습니다.

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

필요한 브라우저 권한과 CSP 도메인만 부여합니다.

위젯에 지리적 위치와 같은 브라우저 기능이 필요한 경우에만 **[!UICONTROL 권한(MCP별)]**&#x200B;을 확장합니다. 사용하지 않는 사용 권한을 해제하십시오.

![새 작업 만들기 — 선택적 브라우저 권한](/help/assets/guide-create-action/widget-permissions.png)

**[!UICONTROL CSP 구성]**&#x200B;에서 자산 및 네트워크 요청에 필요한 원본을 추가합니다. CSP가 완료되지 않은 경우 외부 리소스를 로드하는 위젯이 렌더링되지 않을 수 있습니다.

![새 작업 만들기 — 콘텐츠 보안 정책 도메인 목록](/help/assets/guide-create-action/widget-csp.png)

EDS 프로젝트 또는 위젯 페이지가 아직 없는 경우 [자신의 EDS 프로젝트 가져오기](/help/guides/bring-your-own-eds.md)를 완료한 다음 작업으로 돌아갑니다.

## 작업 저장

**[!UICONTROL 새 작업 만들기]**&#x200B;를 선택합니다. 작업이 작업 페이지에 **배포되지 않음** 배지와 함께 표시됩니다.

저장하면 메타데이터가 만들어지지만 작업에는 여전히 처리기가 필요합니다.

저장된 작업을 다시 열면 편집기에 **[!UICONTROL LLM 위젯 세부 정보]**, **[!UICONTROL 작업 세부 정보]** 및 **[!UICONTROL Analytics]** 탭이 있습니다. 나중에 편집 내용을 저장하려면 **[!UICONTROL 변경 내용 저장]**&#x200B;을 선택합니다. 만들기 대화 상자와 저장된 작업 편집기의 레이아웃이 다릅니다.

## 핸들러 구현

연결된 핸들러 저장소를 복제하고 해당 종속성을 설치합니다.

```bash
npm install
```

만들기:

```text
actions/
└── search-products/
    └── index.js
```

폴더 이름은 작업 편집기에 표시된 작업의 코드 식별자와 일치해야 합니다.

전체 결과 계약 및 처리기-위젯 관계에 대해서는 [생성된 처리기 사용자 지정](/help/guides/customize-handler.md)을 참조하십시오.

### 핸들러 계약

비동기 함수 하나를 내보냅니다.

```javascript
module.exports = async (args) => {
  return {
    content: [
      { type: 'text', text: 'Response for the LLM platform.' }
    ],
    structuredContent: {
      // Data for the widget.
    }
  };
};
```

핸들러가 UI에 정의된 매개 변수를 수신합니다.

### `content` 반환

LLM 플랫폼은 `content`을(를) 텍스트 대체 항목으로 읽습니다.

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

작업에 위젯이 있는 경우에도 항상 유용한 `content`을(를) 반환합니다.

### `structuredContent` 반환

위젯은 `structuredContent`을(를) 일반 개체로 사용합니다.

```javascript
structuredContent: {
  products: [
    { id: 'P-100', name: 'Product A', price: '$20' }
  ],
  total: 1
}
```

셰이프는 EDS 블록이 `bridge.toolResult`에서 읽는 것과 일치해야 합니다.

### API 연결

서버측 처리기에서 API 호출을 유지합니다. 이 예에서는 공개 제품 API를 호출합니다. 이름이 `PRODUCT_API_ORIGIN`인 [앱 변수](/help/guides/app-variables.md)에서 API의 HTTPS 원본을 구성하십시오. 이 예제에서는 위에 정의된 `query` 및 `category` 매개 변수를 수락하고 하나 이상의 매개 변수를 필요로 하며 자격 증명이 필요하지 않습니다.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '', category = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  const normalizedCategory = typeof category === 'string' ? category.trim() : '';
  if (typeof query !== 'string' || typeof category !== 'string'
      || (!normalizedQuery && !normalizedCategory)
      || normalizedQuery.length > 200 || normalizedCategory.length > 100) {
    return {
      isError: true,
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
  if (!apiOrigin) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(apiOrigin);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  if (normalizedQuery) {
    url.searchParams.set('query', normalizedQuery);
  }
  if (normalizedCategory) {
    url.searchParams.set('category', normalizedCategory);
  }

  const response = await fetch(url, {
    signal: AbortSignal.timeout(8000)
  });

  if (!response.ok) {
    throw new Error('Product service request failed.');
  }

  const payload = await response.json();
  if (!payload || !Array.isArray(payload.products)
      || !payload.products.every((product) =>
        product
        && typeof product.id === 'string'
        && typeof product.name === 'string'
        && typeof product.price === 'string')) {
    throw new Error('Product service returned an unexpected response.');
  }

  const products = payload.products.map((product) => ({
    id: product.id,
    name: product.name,
    price: product.price
  }));

  return {
    content: [
      { type: 'text', text: `Found ${products.length} matching products.` }
    ],
    structuredContent: {
      products,
      total: products.length
    }
  };
};
```

소스 코드, 작업 메타데이터, 위젯 JavaScript, 로그 또는 사용자 대면 오류에 API 자격 증명을 넣지 마십시오. **[!UICONTROL 변수 및 암호]** UI는 현재 비밀이 아닌 민감하지 않은 변수만 지원합니다. API 토큰을 변수에 저장하지 마십시오. 업스트림 API에 자격 증명이 필요한 경우 연결하기 전에 승인된 서버측 자격 증명 메커니즘을 정렬하십시오.

프로덕션 코드의 경우 승인된 필드를 `structuredContent`에 매핑하기 전에 전체 업스트림 응답을 확인하십시오.

## 핸들러 테스트 추가

일치하는 테스트를 만듭니다.

```text
test/
└── actions/
    └── search-products.test.js
```

최소 테스트:

- 올바른 입력입니다.
- 입력이 누락되었거나 잘못되었습니다.
- 결과가 비어 있습니다.
- API 시간 초과 또는 실패.
- 잘못된 API 데이터입니다.
- 위젯에 필요한 `structuredContent` 셰이프입니다.

실행:

```bash
npm test
```

프로젝트 레이아웃 및 로컬 MCP 테스트는 [로컬 처리기 개발 및 테스트](/help/reference/development.md)를 참조하십시오.

## 로컬에서 작업 테스트

실행:

```bash
npm run dev:local
```

로컬 `actions.json`이(가) 없으면 서버에서 최소한의 메타데이터만 있고 입력 스키마 유효성 검사가 없는 처리기를 검색합니다.

MCP 검사기 또는 `curl`을(를) 사용하여 다음을 수행합니다.

1. 등록된 작업을 나열합니다.
2. 대표적인 인수로 새 작업을 호출합니다.
3. `content` 및 `structuredContent`을(를) 확인합니다.
4. 잘못되고 빈 요청을 테스트합니다.

## 위젯 연결 및 테스트

작업에 위젯이 있는 경우:

1. 위젯에서 처리기의 `structuredContent`을(를) 읽도록 합니다.
2. `textContent`과(와) 같은 안전한 DOM API를 사용하여 외부 값을 렌더링합니다.
3. 로드, 빈 및 오류 상태를 추가합니다.
4. 로컬에서 EDS 페이지를 미리 봅니다.
5. CSP, CORS 및 위젯 URL을 확인합니다.

[EDS 프로젝트 가져오기](/help/guides/bring-your-own-eds.md)를 참조하십시오.

## 배포 및 테스트

1. 핸들러 및 위젯 변경 사항을 커밋하고 푸시합니다.
2. 스테이징에 [앱을 배포](/help/guides/deploy-your-app.md)합니다.
3. [ChatGPT](/help/guides/test-in-chatgpt.md) 또는 [Cloud](/help/guides/test-in-claude.md)에서 앱을 테스트합니다.
4. 작업과 일치하는 프롬프트 및 해당 작업을 호출하지 않는 관련되지 않은 프롬프트로 작업 선택을 확인합니다.
5. 스테이지가 성공하면 프로덕션에 배포합니다.

일치하는 처리기 없이 메타데이터가 있는 경우 배포는 기본 스텁에 작업을 등록합니다. 사용자가 작업을 사용할 수 있게 만들기 전에 핸들러를 추가하십시오.
