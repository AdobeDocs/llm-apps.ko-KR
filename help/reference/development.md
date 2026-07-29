---
title: 로컬 핸들러 개발 및 테스트
description: Adobe LLM 앱에 대한 핸들러 프로젝트 구조, 로컬 서버 명령, MCP 테스트 및 단위 테스트.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '280'
ht-degree: 1%

---


# 로컬 핸들러 개발 및 테스트 {#development}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

처리기를 로컬로 개발하는 동안 이 참조를 사용하십시오. 처리기 결과 계약에 대해서는 [생성된 처리기 사용자 지정](/help/guides/customize-handler.md)을 참조하십시오.

## 요구 사항

- Node.js 24 이상
- npm.
- 연결된 처리기 저장소의 로컬 복제입니다.

## 프로젝트 구조

연결된 저장소는 다음 레이아웃을 따릅니다.

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   └── echo/
│       └── index.js           # Example handler
├── test/
│   ├── actions/
│   │   └── echo.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — optional local metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

주요 사항:

- **`entry.js`**&#x200B;은(는) Webpack 진입점입니다. 빌드 시 모든 `actions/*/index.js`개의 파일을 검색하고 단일 `dist/index.js`(으)로 번들합니다. 수정하지 마십시오.
- **`actions.json`**&#x200B;이(가) 점프되었습니다. 배포 파이프라인이 [!DNL LLM Apps]의 작업 메타데이터에서 자동으로 씁니다.
- **테스트**&#x200B;이(가) `test/actions/` 아래에 있음, `actions/` 내에 **아님**. Webpack은 `actions/`의 모든 항목을 배포된 아티팩트에 번들로 묶습니다. 테스트를 함께 찾으면 해당 아티팩트가 [!DNL Adobe I/O Runtime]에 전달됩니다.

## 로컬 개발

Adobe 자격 증명 없이 로컬에서 핸들러를 개발하고 테스트할 수 있습니다.

```bash
npm install
npm run dev:local
```

이렇게 하면 webpack으로 프로젝트를 빌드하고 `http://localhost:9080`에서 일반 Node.js HTTP 서버를 시작합니다. 서버가 `actions/`에서 처리기 파일을 자동 검색하고 MCP 도구로 등록합니다.

### 로컬 메타데이터 동작

현재 UI에서 `actions.json` 다운로드를 제공하지 않습니다. 이 파일 없이 로컬 서버를 실행할 수 있습니다. `actions/`에서 처리기를 검색하여 최소한의 메타데이터로 등록합니다.

`actions.json`이(가) 없으면 UI 입력 스키마에 대해 로컬 작업 인수의 유효성을 검사하지 않습니다. 단위 및 통합 테스트에서는 대표 메타데이터에 `test/fixtures/actions.json`을(를) 사용합니다.

### curl로 테스트

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"echo","arguments":{"message":"hello"}}}'
```

### MCP 검사기를 사용하여 테스트

```bash
npx @modelcontextprotocol/inspector
```

**전송 형식**&#x200B;을(를) `streamable-http`(으)로 설정하고 **URL**&#x200B;을(를) `http://localhost:9080`(으)로 설정합니다.

## 테스트

처리기 단위 테스트는 `test/actions/` 아래에 있으며 `actions/` 레이아웃을 미러링합니다.

```javascript
// test/actions/echo.test.js
const handler = require('../../actions/echo/index.js')

test('echoes the message', async () => {
  const result = await handler({ message: 'hello' })
  expect(result.content[0].text).toBe('Echo: hello')
})

test('always returns content parts', async () => {
  const result = await handler({})
  expect(Array.isArray(result.content)).toBe(true)
})
```

다음을 사용하여 테스트 실행:

```bash
npm test                                      # all tests
npx jest test/actions/echo                   # one action only
```

로컬 테스트가 통과되면 변경 내용을 푸시하고 [변경 내용 배포](/help/guides/deploy-your-app.md)를 따릅니다.

