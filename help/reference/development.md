---
title: 로컬 핸들러 개발 및 테스트
description: Adobe LLM 앱에 대한 핸들러 프로젝트 구조, 로컬 서버 명령, MCP 테스트 및 단위 테스트.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '664'
ht-degree: 0%
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

Adobe 관리 인프라의 경우 이 참조를 따르기 전에 저장소 액세스를 요청하십시오. 로컬 핸들러 실행에는 Adobe 자격 증명이 필요하지 않지만 보호된 업스트림 서비스에는 여전히 인증된 자체 액세스가 필요합니다.

## 프로젝트 구조

현재 핸들러 보일러판은 다음 레이아웃을 따릅니다.

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

- **`entry.js`**&#x200B;은(는) Webpack 진입점입니다. 빌드 시 모든 `actions/*/index.js`개의 파일을 검색하고 단일 `dist/index.js`(으)로 번들합니다. 진입점을 수정하지 마십시오.
- **`actions.json`**&#x200B;이(가) 점프되었습니다. 배포 파이프라인이 [!DNL LLM Apps]의 작업 메타데이터에서 자동으로 씁니다.
- **테스트**&#x200B;이(가) `test/actions/` 아래에 있음, `actions/` 내에 **아님**. 빌드는 핸들러 진입점을 검색하고 해당 가져오기를 번들로 묶습니다. 테스트를 해당 검색 트리 외부에 보관하십시오.
- **작업 경로** 코드 식별자 또는 하이픈이 연결된 형식을 사용합니다. 예를 들어 `search_products`은(는) `actions/search_products/index.js` 또는 생성된 코드의 경우 `actions/search-products/index.js`(으)로 확인됩니다. 표시 이름이 조회 키가 아닙니다.
- 핸들러가 데이터를 가져올 때 생성된 `data.json`과(와) 같은 **지원 데이터**&#x200B;가 번들로 제공됩니다.

## 로컬 개발

Adobe 자격 증명 없이 로컬에서 처리기를 개발하고 테스트하려면 다음을 실행합니다.

```bash
npm install
npm run dev:local
```

development 명령은 webpack을 사용하여 프로젝트를 빌드하고 `http://localhost:9080`에서 일반 Node.js HTTP 서버를 시작합니다. 처리기 또는 로컬 메타데이터가 변경된 후 `npm run dev:local`을(를) 중지하고 다시 실행하십시오. 이 명령은 파일을 감시하지 않습니다.

다른 포트를 사용하려면

```bash
PORT=9081 npm run dev:local
```

### 로컬 메타데이터 동작

표준 README 및 런타임 시작 배너에서 `actions.json` 다운로드를 언급하고 있지만 현재 UI에서는 이를 제공하지 않습니다. 로컬 서버도 채워진 파일 없이 시작됩니다. 파일이 없으면 빌드가 `{"actions":[]}`을(를) 만들고 런타임이 처리기 검색으로 대체됩니다.

검색은 처리기를 나열하는 데 유용하지만 완전한 인수 테스트 모드는 아닙니다. 입력 스키마가 없으면 MCP SDK은 일반적인 `(args, extra)` 서명을 사용하는 대신 런타임 컨텍스트가 있는 핸들러를 첫 번째 인수로 호출합니다. SDK은 제공된 작업 인수를 전달하지 않습니다.

인수가 있는 MCP 호출의 경우 작업 편집기와 일치하는 대표 메타데이터가 있는 **로컬 전용** `actions.json`을(를) 만드십시오. 상용구 `echo` 처리기의 경우:

```json
{
  "actions": [
    {
      "name": "echo",
      "description": "Echo the supplied message.",
      "inputSchema": {
        "type": "object",
        "properties": {
          "message": { "type": "string" }
        },
        "required": ["message"]
      }
    }
  ]
}
```

`actions` 배열이 비어 있지 않으면 런타임에서는 배열에 이름이 지정된 작업만 등록합니다. 각 작업의 코드 식별자를 `name`(으)로 사용합니다. 매개 변수가 없는 작업도 `"properties": {}`이(가) 있는 개체 스키마가 있어야 일반 처리기 서명을 유지할 수 있습니다.

로컬 메타데이터는 UI 또는 배포 구성을 업데이트하지 않습니다. 런타임에서는 지원되는 유형, 필수 필드, 열거형 및 배열 항목을 확인하지만 모든 JSON 스키마 제약 조건에서는 확인하지 않습니다. 핸들러의 길이, 범위, 중첩 개체 및 비즈니스 규칙을 확인합니다. 표준 템플릿 통합 테스트에서는 `test/fixtures/actions.json`을(를) 대표 메타데이터로 사용합니다.

### 로컬 앱 변수

앱 변수가 `process.env`이(가) 아닌 `extra.variables`을(를) 통해 처리기에 도달합니다. `@adobe/llm-apps-runtime`에서 `getVariable(extra, name)`(으)로 읽습니다. 빌드 후 민감하지 않은 설정을 시뮬레이션하려면 다음을 수행합니다.

```bash
npm run build
node server/local.js \
  --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' \
  --param GREETING_PREFIX=Howdy
```

JSON 매니페스트와 명명된 값을 모두 제공합니다. 일반적인 `(args, extra)` 계약을 테스트할 때 입력 스키마가 있는 로컬 `actions.json`의 처리기를 포함하십시오. 명령줄 플래그는 플랫폼에서 앱 설정을 로드하지 않습니다. 환경별 배포 설정에 대해서는 [앱 변수 및 암호 구성](/help/guides/app-variables.md)을 참조하십시오.

### curl로 테스트

위에 로컬 `echo` 메타데이터를 추가한 후 서버를 빌드하고 시작한 다음 실행하십시오.

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action (returns "Echo: hello")
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

처리기 단위 테스트는 `test/actions/`에서 실행되며 MCP 스키마 유효성 검사 없이 내보낸 함수를 직접 호출합니다.

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
npm test                                 # configured Jest suite
npm test -- --runInBand test/actions/echo.test.js  # one boilerplate action
```

생성된 테스트 중 일부는 Jest 대신 Node의 내장 테스트 실행기를 사용합니다. 테스트 가져오기를 확인하고 일치하는 실행기(예: `node:test`을(를) 가져오는 파일에 대해 `node --test test/actions/search-products.test.js`)를 사용하십시오. `test/actions/` 아래에 파일을 배치한 후 생성된 상대 가져오기를 확인하십시오.

단위 테스트만으로는 메타데이터, MCP 유효성 검사, 위젯 로드 또는 호스트 동작을 확인하지 않습니다. 대표 메타데이터로 로컬 끝점을 테스트한 다음 지원되는 LLM 플랫폼에서 배포된 앱을 테스트합니다.

로컬 테스트가 통과되면 변경 내용을 푸시하고 [변경 내용 배포](/help/guides/deploy-your-app.md)를 따릅니다.
