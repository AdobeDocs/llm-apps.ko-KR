---
title: 생성된 작업 핸들러 사용자 정의
description: Adobe LLM Apps 핸들러 계약을 이해하고, 생성된 샘플 데이터를 대체하며, 핸들러 출력을 해당 위젯과 일치하도록 유지합니다.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '880'
ht-degree: 0%
---

# 생성된 핸들러 사용자 정의 {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

플랫폼은 생성된 모든 작업에 대해 작업 핸들러를 만듭니다. 처리기는 처음에 전체 경험을 테스트하기 위한 샘플 데이터를 반환합니다.

**이 안내서:** 생성된 처리기를 찾고, 샘플 데이터를 시스템에 대한 호출로 바꾸고, 응답을 위젯과 호환되도록 유지하는 방법을 알아봅니다. 그런 다음 변경 사항을 테스트하고 배포합니다.

## 생성된 핸들러 찾기

[인프라 설정](/help/guides/create-app.md) 중에 연결된 처리기 저장소를 엽니다. Adobe 관리 인프라를 사용하는 경우 계속하기 전에 코드 액세스를 요청하십시오.

```text
actions/
└── <action-slug>/
    ├── index.js
    └── data.json
```

일치하는 테스트는 별도의 디렉터리에 있습니다.

```text
test/
└── actions/
    └── <action-slug>.test.js
```

생성된 가져오기 요청을 사용하여 정확한 파일을 찾습니다. 폴더 슬러그는 하이픈을 사용합니다. 플랫폼은 `actions/search-products/` 아래에 코드 식별자가 `search_products`인 작업을 생성합니다. 런타임에서는 하이픈이 연결된 폼과 식별자와 일치하는 폴더를 정확하게 인식합니다.

생성된 `index.js` 및 연결된 샘플 데이터를 편집합니다. `entry.js`과(와) 같은 런타임 파일은 변경하지 마십시오.

## 핸들러 계약

각 처리기는 하나의 비동기 함수를 내보냅니다.

```javascript
module.exports = async (args, extra) => {
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

입력 스키마가 있는 작업의 경우 함수는 `args` 개체와 선택적 런타임 컨텍스트 `extra`을(를) 받고 결과 개체를 반환합니다. `getVariable(extra, 'PRODUCT_API_ORIGIN')`과(와) 같은 런타임 도우미에 `extra`을(를) 사용하십시오. 앱 변수는 `process.env`을(를) 통해 자동으로 노출되지 않습니다. [앱 변수 및 암호 구성](/help/guides/app-variables.md)을 참조하세요.

### 입력: `args`

`args`에 [!DNL LLM Apps]의 작업에 대해 정의된 매개 변수가 포함되어 있습니다.

`category` 및 `query` 매개 변수가 있는 작업의 경우:

```javascript
module.exports = async ({ category = '', query = '' } = {}, extra) => {
  // Validate business rules before using the arguments.
};
```

작업 메타데이터를 사용하면 런타임에서 선언된 입력 스키마를 필수 필드, 기본 유형, 열거형 및 배열 항목 등 지원되는 규칙에 대한 유효성 검사로 변환합니다. 런타임에서 전체 JSON 스키마 유효성 검사를 수행하지 않습니다. 처리기의 문자열 길이, 숫자 범위 및 중첩된 오브젝트 속성과 같은 제약 조건을 확인합니다.

스키마 메타데이터가 없는 로컬 검색은 배포와 같지 않습니다. 런타임에서 일반 `(args, extra)` 양식의 작업 인수를 전달하지 않습니다. 직접 처리기 테스트 또는 대표적인 로컬 `actions.json` 메타데이터를 사용하여 인수를 테스트하십시오. [로컬 처리기 개발 및 테스트](/help/reference/development.md)를 참조하세요.

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

텍스트 응답을 간결하게 유지합니다. 사용자에게 볼 권한이 없는 자격 증명, 내부 오류 또는 데이터는 포함하지 마십시오.

### 출력: `structuredContent`

작업에 위젯이 있으면 `structuredContent`을(를) 반환합니다. 기본 배열이 아니라 일반 개체여야 합니다.

```javascript
structuredContent: {
  arguments: { query: 'coffee' },
  records: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ]
}
```

`structuredContent`은(는) 호스트에 전달된 MCP 작업 결과의 일부입니다. 모델이나 사용자에게 숨겨져 있다고 가정하지 마십시오. 자격 증명이나 개인 서버 구성이 아닌 경험에 필요한 인증된 데이터만 반환합니다.

텍스트 전용 작업의 경우 `structuredContent` 반환은 선택 사항입니다.

## 핸들러-위젯 계약

처리기와 위젯이 `structuredContent`의 셰이프라는 하나의 계약을 공유합니다.

| 핸들러 출력 | 목적 |
|---|---|
| `content` | 모델이 읽을 수 있는 응답 및 텍스트 전용 대체 |
| `structuredContent` | `bridge.toolResult`을(를) 통해 호스트에 전달되고 위젯에서 읽는 데이터 |

위젯은 LLM 앱 SDK 브리지에서 핸들러 결과를 읽습니다.

```javascript
export default async function decorate(block, bridge) {
  if (!bridge?.isConnected || bridge.hostContext?.preview === true) {
    // Render the block's local preview data instead.
    return;
  }
  const result = await bridge.toolResult;
  const records = result?.structuredContent?.records ?? [];

  // Render records.
}
```

현재 생성된 처리기에서 `arguments` 및 `records`이(가) 포함된 개체를 사용합니다. 기존 앱은 다른 모양을 사용할 수 있으므로 편집하기 전에 생성된 파일을 확인하십시오. 필드 이름이나 유형을 변경하면 위젯이 중단될 수 있습니다. 핸들러, 위젯 및 테스트를 함께 업데이트합니다.

## 샘플 데이터 바꾸기

생성된 처리기는 `data.json`에서 대표 레코드를 로드합니다. 샘플 데이터 조회를 시스템에 대한 서버측 호출로 바꿉니다. 다음 예제에서는 공개 HTTPS 카탈로그 API를 사용하고 앱 변수에서 민감하지 않은 원본을 읽습니다. 응답 유효성 검사를 API에 맞게 조정합니다.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { arguments: {}, records: [] },
      isError: true
    };
  }

  const knownArguments = { query };
  try {
    const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
    if (!apiOrigin) {
      throw new Error('Missing configuration');
    }
    const origin = new URL(apiOrigin);
    if (origin.protocol !== 'https:' || origin.username || origin.password) {
      throw new Error('Invalid API origin');
    }
    const url = new URL('/v1/products', origin);
    url.searchParams.set('query', normalizedQuery);

    const response = await fetch(url, {
      signal: AbortSignal.timeout(8000)
    });
    if (!response.ok) {
      throw new Error('Request failed');
    }
    const payload = await response.json();
    if (!payload || !Array.isArray(payload.products)
        || !payload.products.every((product) =>
          product
          && typeof product.id === 'string'
          && typeof product.name === 'string'
          && typeof product.price === 'string')) {
      throw new Error('Unexpected response');
    }

    const records = payload.products.map(({ id, name, price }) => ({
      id,
      name,
      price
    }));

    return {
      content: [
        { type: 'text', text: `Found ${records.length} matching products.` }
      ],
      structuredContent: { arguments: knownArguments, records }
    };
  } catch {
    return {
      content: [{ type: 'text', text: 'Product search is unavailable. Try again later.' }],
      structuredContent: { arguments: knownArguments, records: [] },
      isError: true
    };
  }
};
```

핸들러에서 보호된 네트워크 액세스를 유지합니다. 위젯 JavaScript 또는 소스 제어에 API 자격 증명을 추가하지 마십시오. 로그인한 사용자를 대신하여 요청을 하려면 [최종 사용자 인증](/help/guides/authentication.md)을 참조하십시오.

서비스 URL과 같이 처리기에 필요한 중요하지 않은 설정에 대해서는 [앱 변수 및 암호 구성](/help/guides/app-variables.md)을 참조하십시오. 각 환경에 대해 변수를 별도로 구성합니다. 변수 변경 사항은 다음 배포에 적용됩니다. 현재 UI는 변수 추가만 지원합니다. API 자격 증명을 저장하는 데 변수를 사용하지 마십시오.

## 예상 상태 처리

모든 결과에 대해 예측 가능한 출력 모양을 유지합니다.

### 검색 결과

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [...] }
}
```

### 결과 없음

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [] }
}
```

예측 가능한 결과 셰이프를 사용하면 `records`이(가) 있는지 확인하지 않고 위젯에서 빈 상태를 렌더링할 수 있습니다.

서비스 실패의 경우 예제와 같이 `isError: true`을(를) 사용하여 안전한 오류 결과를 반환합니다. 실패한 요청이 성공적인 빈 검색으로 표시되지 않도록 위젯에서 `result.isError`을(를) 확인하십시오. 스택 추적, 자격 증명, 내부 호스트 또는 업스트림 응답 본문을 노출하지 마십시오.

## 계약 테스트

처리기가 변경될 때마다 생성된 테스트를 업데이트합니다. 커버:

- 유효하고 잘못된 인수.
- 결과 및 결과 없음 상태.
- API 실패 및 시간 초과.
- 잘못된 API 응답.
- 모든 결과에 `content`이(가) 있습니다.
- `structuredContent`에 대한 일반 개체입니다.
- 위젯에서 예상하는 모양입니다.

저장소에 구성된 테스트 세트를 실행합니다.

```bash
npm test
```

생성된 테스트가 Jest가 아닌 `node:test`을(를) 가져오는 경우 `node --test test/actions/<action-slug>.test.js`(으)로 실행하고 가져오기 경로가 `test/actions/`에서 확인되는지 확인하십시오. 파일에 적합한 테스트 실행기를 사용하십시오. 보일러플레이트의 `npm test` 명령은 Jest를 실행합니다.

로컬 MCP 테스트는 [로컬 처리기 개발 및 테스트](/help/reference/development.md)를 참조하십시오.

## 변경 사항 배포

1. 핸들러 변경 사항을 커밋하고 푸시합니다.
2. 데이터 모양을 변경하는 경우 위젯을 업데이트하고 푸시합니다.
3. 스테이징에 [앱을 배포](/help/guides/deploy-your-app.md)합니다.
4. 지원되는 LLM 플랫폼에서 테스트합니다. [Test in ChatGPT](/help/guides/test-in-chatgpt.md) 또는 [Test in Cloud](/help/guides/test-in-claude.md)를 참조하십시오.
5. 테스트가 스테이지에 통과되면 프로덕션에 배포합니다.

[생성된 위젯 사용자 지정](/help/guides/widgets.md)을(를) 참조하십시오.
