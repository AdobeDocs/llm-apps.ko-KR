---
title: Adobe LLM 앱 개발
description: Adobe LLM Apps 핸들러 코드에 대한 프로젝트 구조, 로컬 개발 워크플로 및 테스트 설정.
source-git-commit: 51ffb31eec82f9639bd7ade9052d61028c262d0e
workflow-type: tm+mt
source-wordcount: '318'
ht-degree: 4%

---


# 개발 {#development}

>[!IMPORTANT]
>
>**면책조항:** [!DNL LLM Apps]의 베타 릴리스입니다. 여기에 표시된 기능, 워크플로우 및 UI가 반드시 애플리케이션 또는 제품의 최종 상태를 나타내지는 않습니다.

이 섹션에서는 핸들러 프로젝트 구조, 로컬 개발 워크플로우 및 테스트 설정을 다룹니다. 처리기 계약과 예제 코드에 대해서는 [작업 처리기 작성](/help/guides/write-action-handler.md)을 참조하십시오.

## 프로젝트 구조

연결된 저장소는 다음 레이아웃을 따릅니다.

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   ├── search-products/
│   │   └── index.js           # Handler (async function)
│   ├── get-product-details/
│   │   └── index.js
│   └── echo/
│       └── index.js
├── test/
│   ├── actions/
│   │   └── search-products.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — local copy of UI metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

주요 사항:

- **`entry.js`**&#x200B;은(는) Webpack 진입점입니다. 빌드 시 모든 `actions/*/index.js`개의 파일을 검색하고 단일 `dist/index.js`(으)로 번들합니다. 수정하지 마십시오.
- **`actions.json`**&#x200B;이(가) 점프되었습니다. 로컬 개발을 위해 UI의 작업 페이지에서 다운로드합니다. 배포의 경우 파이프라인은 API에서 자동으로 기록합니다.
- **테스트**&#x200B;이(가) `test/actions/` 아래에 있음, `actions/` 내에 **아님**. Webpack은 `actions/`의 모든 항목을 배포된 아티팩트에 번들로 묶습니다. 테스트를 함께 찾으면 해당 아티팩트가 [!DNL Adobe I/O Runtime]에 전달됩니다.

## 로컬 개발

Adobe 자격 증명 없이 로컬에서 핸들러를 개발하고 테스트할 수 있습니다.

```bash
npm install
npm run dev:local
```

이렇게 하면 webpack으로 프로젝트를 빌드하고 `http://localhost:9080`에서 일반 Node.js HTTP 서버를 시작합니다. 서버가 `actions/`에서 처리기 파일을 자동 검색하고 MCP 도구로 등록합니다.

### 다운로드 `actions.json`

로컬 서버가 작업 메타데이터(이름, 설명, 입력 스키마)에 대해 알 수 있도록 [!DNL LLM Apps] UI의 작업 페이지에서 `actions.json`을(를) 다운로드하고 저장소 루트에 배치합니다. 이 기능이 없으면 서버에서 처리기를 검색하지만 최소한의 메타데이터로 등록합니다.

`actions.example.json`을(를) `actions.json`에 시작 지점으로 복사할 수도 있습니다.

### curl로 테스트

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the search-products action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search-products","arguments":{"category":"bagged-coffee"}}}'
```

### MCP 검사기를 사용하여 테스트

```bash
npx @modelcontextprotocol/inspector
```

**전송 형식**&#x200B;을(를) `streamable-http`(으)로 설정하고 **URL**&#x200B;을(를) `http://localhost:9080`(으)로 설정합니다.

## 테스트

처리기 단위 테스트는 `test/actions/` 아래에 있으며 `actions/` 레이아웃을 미러링합니다.

```javascript
// test/actions/search-products.test.js
const handler = require('../../actions/search-products/index.js')

test('returns all products when no filter is given', async () => {
  const result = await handler({})
  expect(result.content[0].text).toContain('product')
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('filters by category', async () => {
  const result = await handler({ category: 'bagged-coffee' })
  expect(result.structuredContent.products.every(
    (p) => p.category === 'bagged-coffee'
  )).toBe(true)
})

test('filters by query', async () => {
  const result = await handler({ query: 'dark-roast' })
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('returns empty result for unknown category', async () => {
  const result = await handler({ category: 'nonexistent' })
  expect(result.structuredContent.products).toHaveLength(0)
})
```

다음을 사용하여 테스트 실행:

```bash
npm test                                      # all tests
npx jest test/actions/search-products        # one action only
```

## 배포

수동으로 빌드하거나 배포하지 않습니다. 배포 파이프라인에 대한 전체 설명은 [앱 배포](/help/guides/deploy-your-app.md)를 참조하십시오.

일상적인 워크플로는 다음과 같습니다.

| 단계 | 작업 |
|------|--------|
| &#x200B;1. 처리기 쓰기 또는 편집 | `actions/<name>/index.js` |
| &#x200B;2. 메타데이터 다운로드 | 작업 페이지 → **Download actions.json** |
| &#x200B;3. 로컬에서 테스트 | `npm run dev:local` |
| &#x200B;4. 푸시 코드 | `git push` |
| &#x200B;5. 배포 | 앱 세부 정보 페이지 → **[!UICONTROL 배포]** |

