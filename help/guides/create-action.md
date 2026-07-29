---
title: 처음부터 작업 만들기
description: 작업 메타데이터를 정의하고, 핸들러를 구현하고, EDS 위젯을 연결하고, 테스트하고, Adobe LLM 앱과 함께 배포합니다.
source-git-commit: 4c259a4587c0a84bb634a9a56c043dfe1cfc31fb
workflow-type: tm+mt
source-wordcount: '1141'
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

이 안내서를 사용하여 온보딩 에이전트가 만들지 않은 기능을 추가합니다. [!DNL LLM Apps]에서 작업을 정의하고 연결된 리포지토리에 해당 처리기를 작성한 다음 필요한 경우 위젯을 추가하고 테스트한 다음 배포합니다.

**여정:** 작업을 계획하여 해당 메타데이터→ 만들고 →→ 를 작성하여 로컬로 테스트하고 위젯→ 테스트하고 플러그인→ 배포하고 테스트합니다.

첫 번째 앱의 경우 [온보딩 에이전트로 첫 번째 앱을 만들기](/help/guides/create-app.md)로 시작합니다.

## 시작하기에 앞서

다음이 필요합니다.

- 기존 LLM 앱.
- 연결된 처리기 저장소입니다.
- 종속성이 설치된 상태로 로컬로 복제된 저장소입니다.
- 작업에 위젯이 표시되는 경우 EDS 프로젝트입니다.
- 프로덕션 결과에 대한 명확한 API 또는 데이터 소스.

## 작업 계획

작업은 하나의 사용자 지우기 작업을 수행해야 합니다. UI를 열기 전에 다음을 정의합니다.

- **의도** — 사용자가 수행하려는 작업입니다.
- **설명** - LLM 플랫폼에서 이 작업을 선택해야 하는 경우.
- **입력** — 사용자에게 필요한 최소 정보입니다.
- **결과** — 처리기에서 반환된 텍스트 및 구조적 데이터입니다.
- **동작** — 작업에서 데이터를 읽는지, 데이터를 변경하는지, 외부 시스템을 호출하는지 여부를 지정합니다.
- **위젯** — 결과에 시각적 인터페이스가 필요한지 여부.

예를 들어 **제품 검색** 작업은 다음을 사용할 수 있습니다.

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

관련된 작업이지만 다른 작업은 별도로 유지합니다. 제품 검색 및 제품 구매는 입력, 위험 및 확인 요구 사항이 다르므로 하나의 작업이 아니어야 합니다.

## 작업 메타데이터 만들기

앱을 열고 **[!UICONTROL 작업]**&#x200B;을 선택한 다음 **[!UICONTROL 작업 만들기]**&#x200B;를 선택합니다.

편집기에 **[!UICONTROL 작업]** 및 **[!UICONTROL 위젯 메타데이터]** 탭이 있습니다.

### 기본 정보 입력

![작업 만들기 — 기본 정보](/help/assets/guide-create-action/action-basic-info.png)

다음을 입력합니다.

- **[!UICONTROL 작업 이름]** — *제품 검색*&#x200B;과 같은 짧은 작업 이름.
- **[!UICONTROL 설명]** - 작업을 사용할 시점과 반환된 내용을 설명합니다.

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

위젯이 있는 작업의 경우 **[!UICONTROL 위젯 설명]**&#x200B;을 추가하십시오. 작업 설명과 다릅니다.

- **작업 설명**&#x200B;은(는) 모델이 작업을 호출할 시기를 결정하는 데 도움이 됩니다.
- **위젯 설명**&#x200B;은(는) `_meta["openai/widgetDescription"]`에 매핑되고 렌더링된 구성 요소에 표시되는 내용을 요약하므로 반복되는 설명이 줄어듭니다.

[!DNL LLM Apps]이(가) 구성 요소 메타데이터로 적용합니다. 핸들러에서 반환하지 마십시오.

### 가시성 구성

- **[!UICONTROL AI 모델에 표시]**&#x200B;하여 모델이 작업을 선택할 수 있습니다.
- **[!UICONTROL 앱 표면에 위젯으로 표시]**&#x200B;에 구성된 위젯이 표시됩니다.

액션이 텍스트만 반환하는 경우 위젯 가시성을 비활성화합니다.

### 입력 매개 변수 추가

핸들러가 허용하는 각 값에 대해 하나의 매개 변수를 추가합니다. 모든 매개 변수에는 다음이 필요합니다.

- **이름** — 핸들러가 받은 키입니다.
- **유형** — 문자열, 숫자, 정수 또는 부울.
- **설명** — 모델이 값을 추출하는 방법입니다.
- **필수** — 작업을 실행하지 않고 실행할 수 있는지 여부입니다.

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

![작업 만들기 — 사용자 의도 분석](/help/assets/guide-create-action/action-analytics-user-intent.png)

전체 필드 정의는 [작업 및 위젯 필드](/help/reference/reference-docs.md)를 참조하세요.

## 위젯 구성

텍스트 전용 작업인 경우 이 섹션을 건너뜁니다.

**[!UICONTROL 위젯 메타데이터]**&#x200B;을(를) 엽니다.

![작업 만들기 — 위젯 메타데이터](/help/assets/guide-create-action/widget-metadata.png)

다음을 구성하십시오.

- **유형** — EDS를 선택합니다.
- **위젯 도메인** — 위젯을 호스팅하는 EDS 원본입니다.
- **테두리 선호** — 호스트에서 테두리 컨테이너를 요청합니다.
- **스크립트 URL** — EDS 위젯 진입점입니다.
- **위젯 URL** — 이 작업에 대해 게시된 EDS 페이지입니다.

일반적인 URL은 다음과 같습니다.

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

필요한 브라우저 권한과 CSP 도메인만 부여합니다.

![작업 만들기 — 권한 및 CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

EDS 프로젝트 또는 위젯 페이지가 아직 없는 경우 [자신의 EDS 프로젝트 가져오기](/help/guides/bring-your-own-eds.md)를 완료한 다음 작업으로 돌아갑니다.

## 작업 저장

**[!UICONTROL 새 작업 만들기]**&#x200B;를 선택합니다. 작업이 작업 페이지에 **배포되지 않음** 배지와 함께 표시됩니다.

이 시점에서 메타데이터가 존재하지만 작업에는 여전히 핸들러가 필요합니다.

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

`content`은(는) LLM 플랫폼에서 읽은 텍스트 대체 요소입니다.

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

작업에 위젯이 있는 경우에도 항상 유용한 `content`을(를) 반환합니다.

### `structuredContent` 반환

`structuredContent`은(는) 위젯에서 사용하는 일반 개체입니다.

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

서버측 처리기에서 보호된 API 액세스를 유지합니다. 런타임 환경에서 구성을 로드하고 고정 HTTPS 원본을 사용합니다.

```javascript
const API_ORIGIN = process.env.PRODUCT_API_ORIGIN;
const API_TOKEN = process.env.PRODUCT_API_TOKEN;

module.exports = async ({ query = '' } = {}) => {
  const normalizedQuery = String(query).trim();
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  if (!API_ORIGIN || !API_TOKEN) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(API_ORIGIN);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  url.searchParams.set('query', normalizedQuery);

  const response = await fetch(url, {
    headers: { Authorization: `Bearer ${API_TOKEN}` },
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

소스 코드, 작업 메타데이터, 위젯 JavaScript, 로그 또는 사용자 대면 오류에 API 자격 증명을 넣지 마십시오.

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
3. [ChatGPT 플러그 인을 테스트합니다](/help/guides/test-in-chatgpt.md).
4. 작업을 호출해야 하는 프롬프트와 호출해서는 안 되는 프롬프트를 확인합니다.
5. 스테이지가 성공하면 프로덕션에 배포합니다.

일치하는 처리기 없이 메타데이터가 있는 경우 배포는 기본 스텁에 작업을 등록합니다. 사용자가 작업을 사용할 수 있게 만들기 전에 핸들러를 추가하십시오.
- [안내서: 위젯(EDS) 설정](/help/guides/widgets.md)
