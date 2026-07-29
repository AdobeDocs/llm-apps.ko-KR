---
title: 생성된 작업 핸들러 사용자 정의
description: Adobe LLM Apps 핸들러 계약을 이해하고, 생성된 샘플 데이터를 대체하며, 핸들러 출력을 해당 위젯과 일치하도록 유지합니다.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '541'
ht-degree: 0%

---


# 생성된 핸들러 사용자 정의 {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

플랫폼은 생성된 모든 작업에 대해 작업 핸들러를 만듭니다. 전체 경험을 테스트할 수 있도록 핸들러가 처음에 샘플 데이터를 반환합니다.

이 안내서를 사용하여 핸들러 계약을 이해하고 샘플 데이터를 API 또는 데이터 소스로 대체합니다.

**여정:** 생성된 처리기→ 찾아 해당 입력 및 결과를 이해하고 시스템→ 연결한 → 테스트 및 배포→ 위젯 계약 정렬을 유지합니다.

## 생성된 핸들러 찾기

온보딩 중에 선택한 핸들러 저장소를 엽니다.

```text
actions/
└── <action-name>/
    └── index.js
```

일치하는 테스트는 별도로 저장됩니다.

```text
test/
└── actions/
    └── <action-name>.test.js
```

생성된 `index.js`을(를) 편집합니다. `entry.js`과(와) 같은 런타임 파일은 변경하지 마십시오.

## 핸들러 계약

각 처리기는 하나의 비동기 함수를 내보냅니다.

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

함수는 `args` 개체를 받고 결과 개체를 반환합니다.

### 입력: `args`

`args`에 [!DNL LLM Apps]의 작업에 대해 정의된 매개 변수가 포함되어 있습니다.

`category` 및 `query` 매개 변수가 있는 작업의 경우:

```javascript
module.exports = async ({ category = '', query = '' } = {}) => {
  // Use the validated action arguments.
};
```

런타임에서는 배포 후와 마찬가지로 작업 메타데이터에 `inputSchema`이(가) 포함된 경우 입력 스키마를 확인합니다. `actions.json`이(가) 없는 로컬 처리기 검색은 스키마 유효성 검사를 적용하지 않습니다. 처리기는 항상 지원되는 값, 최대 길이 및 허용된 조합과 같은 비즈니스 규칙을 적용해야 합니다.

### 출력: `content`

항상 `content`을(를) 반환합니다. LLM 플랫폼 및 위젯을 표시하지 않는 호스트에서 읽는 컨텐츠 부분의 배열입니다.

```javascript
content: [
  {
    type: 'text',
    text: 'Found 3 products matching your search.'
  }
]
```

이 응답을 간결하게 유지하십시오. 사용자에게 볼 권한이 없는 자격 증명, 내부 오류 또는 데이터는 포함하지 마십시오.

### 출력: `structuredContent`

작업에 위젯이 있으면 `structuredContent`을(를) 반환합니다. 기본 배열이 아니라 일반 개체여야 합니다.

```javascript
structuredContent: {
  products: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ],
  total: 1
}
```

`structuredContent`이(가) LLM이 아닌 위젯으로 전송됩니다. 인터페이스에 필요한 필드만 반환합니다.

텍스트 전용 작업의 경우 `structuredContent`을(를) 생략할 수 있습니다.

## 핸들러-위젯 계약

처리기와 위젯이 `structuredContent`의 셰이프라는 하나의 계약을 공유합니다.

```text
Action arguments
      ↓
Handler
      ├── content → LLM text response
      └── structuredContent → Widget
                                  ↓
                           bridge.toolResult
```

위젯은 LLM 앱 SDK 브리지에서 핸들러 결과를 읽습니다.

```javascript
export default async function decorate(block, bridge) {
  const result = await bridge.toolResult;
  const products = result?.structuredContent?.products ?? [];

  // Render products.
}
```

처리기가 다음을 반환하는 경우:

```javascript
structuredContent: {
  products: [...],
  total: 3
}
```

위젯은 `structuredContent.products` 및 `structuredContent.total`을(를) 읽어야 합니다.

필드 이름이나 유형을 변경하면 위젯이 중단될 수 있습니다. 핸들러, 위젯 및 테스트를 함께 업데이트합니다.

## 샘플 데이터 바꾸기

생성된 핸들러에는 일반적으로 인메모리 샘플 배열이 포함됩니다. 해당 데이터 조회를 시스템에 대한 서버측 호출로 바꿉니다.

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

  const products = payload.products.map(({ id, name, price }) => ({
    id,
    name,
    price
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

핸들러에서 보호된 네트워크 액세스를 유지합니다. 위젯 JavaScript 또는 소스 제어에 API 자격 증명을 추가하지 마십시오.

## 예상 상태 처리

모든 결과에 대해 예측 가능한 출력 모양을 유지합니다.

### 검색 결과

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { products: [...], total: 3 }
}
```

### 결과 없음

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { products: [], total: 0 }
}
```

이제 `products`이(가) 있는지 여부를 확인하지 않고 위젯에서 빈 상태를 렌더링할 수 있습니다.

서비스 오류의 경우 스택 추적, 토큰, 내부 호스트 또는 업스트림 응답 본문을 노출하지 않고 안전한 오류를 반환하거나 throw하십시오.

## 계약 테스트

처리기가 변경될 때마다 생성된 테스트를 업데이트합니다. 커버:

- 유효하고 잘못된 인수.
- 결과 및 결과 없음 상태.
- API 실패 및 시간 초과.
- 잘못된 API 응답.
- `content`이(가) 항상 있습니다.
- `structuredContent`은(는) 일반 개체입니다.
- 위젯에서 예상하는 모양입니다.

실행:

```bash
npm test
```

로컬 MCP 테스트는 [로컬 처리기 개발 및 테스트](/help/reference/development.md)를 참조하십시오.

## 변경 사항 배포

1. 핸들러 변경 사항을 커밋하고 푸시합니다.
2. 데이터 모양이 변경되면 위젯을 업데이트하고 푸시합니다.
3. 스테이징에 [앱을 배포](/help/guides/deploy-your-app.md)합니다.
4. [ChatGPT 플러그 인을 테스트합니다](/help/guides/test-in-chatgpt.md).
5. 스테이지가 성공하면 프로덕션에 배포합니다.

[생성된 위젯 사용자 지정](/help/guides/widgets.md)을(를) 참조하십시오.
