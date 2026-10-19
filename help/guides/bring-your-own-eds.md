---
title: 나만의 Edge Delivery Services 프로젝트 가져오기
description: 기존 Adobe Edge Delivery Services 프로젝트를 Adobe LLM 앱 작업에 연결합니다.
hidefromtoc: true
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '866'
ht-degree: 1%
---

# 자체 EDS 프로젝트 가져오기 {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

이 안내서를 사용하여 호환되는 기존 Edge Delivery Services(EDS) 프로젝트를 연결하거나 직접 만든 작업에 위젯을 추가할 수 있습니다. 기존 프로젝트는 선택 사항입니다. 고유한 인프라를 설정하면 기본 경로는 [인프라 설정](/help/guides/create-app.md) 중에 플랫폼이 초기화하는 빈 저장소 두 개로 시작됩니다.

플랫폼에서 위젯을 자동으로 만든 경우 [생성된 위젯 사용자 지정](/help/guides/widgets.md)을 대신 따르십시오. 생성된 프로젝트에는 여기에 설명된 SDK 파일, 블록, 컨텐츠 및 작업 구성이 이미 포함되어 있습니다.

**이 안내서에서:** EDS 프로젝트를 준비하고, SDK을 설치하고, 위젯을 빌드하고 게시하는 방법에 대해 알아봅니다. 그런 다음 위젯을 작업에 연결하고 배포된 앱에서 테스트합니다.

## 시작하기에 앞서

다음이 필요합니다.

- [AEM 코드 동기화](https://github.com/apps/aem-code-sync)가 설치되어 있고 구성된 콘텐츠 원본이 있는 AEM Boilerplate 호환 EDS 리포지토리입니다.
- 저장소에 종속성을 추가하고 블록을 만들 수 있는 권한입니다.
- EDS 사이트에 대한 응답 헤더를 구성할 수 있는 권한입니다.
- `structuredContent`을(를) 반환하는 핸들러가 있는 [!DNL LLM Apps]의 작업입니다.

기존 리포지토리를 연결하면 플랫폼에서 `scripts/aem.js`, `blocks/`, `styles/styles.css` 및 `head.html`을(를) 확인합니다. 포함 또한 `scripts/scripts.js`을(를) 가져오고 내보낸 `decorateMain` 함수를 호출하여 블록을 준비합니다. EDS 블록 로드 계약을 그대로 유지합니다. 필요한 파일만으로는 심하게 맞춤화된 프로젝트가 올바로 렌더링되는지 확인할 수 없습니다.

## LLM 앱 설치 SDK

EDS 프로젝트 루트에서:

```bash
npm install @adobe/llmapps-sdk
```

패키지의 설치 후 후크는 위젯 진입점, 브리지 구현 및 지원 Adobe 라이브러리를 프로젝트에 복사합니다.

```text
scripts/
├── aem-embed.js
├── llmapps-sdk.js
├── alloy-base.min.js
└── alloy-standalone.min.js
```

작업의 스크립트 URL이 `scripts/llmapps-sdk.js`이(가) 아닌 `scripts/aem-embed.js`을(를) 가리킵니다. 설치 시 SDK 파일이 복사되었는지 확인한 다음 커밋하고 프로젝트와 함께 푸시합니다. 후크는 SDK을 설치하거나 업데이트할 때 복사된 파일을 덮어씁니다. 복사된 SDK 파일을 사용자 지정하지 마십시오.

## 위젯 블록 만들기

작업에 대한 블록을 만듭니다.

```text
blocks/
└── search-products/
    ├── search-products.js
    └── search-products.css
```

연결된 브리지를 두 번째 인수로 사용하여 표준 EDS `decorate` 함수를 내보냅니다.

```javascript
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  if (bridge?.isConnected) {
    bridge.applyHostStyles();
  }

  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];

  if (result?.isError) {
    const message = document.createElement('p');
    message.textContent = 'Product search is unavailable. Try again later.';
    block.replaceChildren(message);
    bridge.autoResize(block);
    return;
  }

  const list = document.createElement('ul');
  records.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product?.name ?? 'Product');
    list.append(item);
  });

  if (records.length === 0) {
    const item = document.createElement('li');
    item.textContent = 'No matching products were found.';
    list.append(item);
  }
  block.replaceChildren(list);

  if (bridge?.isConnected) {
    bridge.autoResize(block);
  }
}
```

텍스트 값을 인코딩하는 DOM API를 사용합니다. 외부 데이터를 HTML에 연결하지 마십시오.

이 예제에서는 `structuredContent.records`을(를) 사용해야 합니다. 핸들러가 반환한 속성 이름 및 레코드 필드와 일치시키십시오. 브리지가 존재하는지 뿐만 아니라 `bridge.isConnected`을(를) 확인합니다. `toolResult`은(는) 독립 실행형 모드에서 확인되지 않습니다. LLM 앱 미리 보기에서 `bridge.hostContext.preview`이(가) 미리 보기 모드를 나타낼 때 블록의 샘플 데이터를 사용합니다.

## 위젯 페이지 작성 및 게시

위젯에 대한 EDS 페이지 하나를 만들고 해당 페이지에 블록을 추가합니다. 페이지를 게시합니다.

페이지 URL이 작업의 위젯 URL이 됩니다. 예를 들어 생성된 기본 구성은 EDS 미리보기 원점을 사용합니다.

```text
https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
```

`https://main--<repo>--<owner>.aem.live`과(와) 같은 EDS 라이브 원본은 페이지 및 자산이 게시될 때도 작동합니다. 실제 사이트 원본을 사용하십시오. 구성된 EDS 사이트에는 GitHub 저장소 및 소유자와 다른 사이트 및 조직 이름이 있을 수 있습니다.

위젯 URL에 `.plain.html`을(를) 추가하면 페이지 조각이 반환되고 페이지에 `search-products` 블록이 포함되어 있는지 확인합니다. 페이지 경로는 작업 식별자와 일치하지 않아도 되지만, 일관된 규칙을 사용하면 프로젝트를 보다 쉽게 유지 관리할 수 있습니다.

## CORS 구성

위젯은 원본 간에 EDS 페이지, 스크립트, 스타일, 블록 및 미디어를 로드합니다. EDS 사이트에 대한 헤더를 구성합니다.

```json
{
  "/**": [
    {
      "key": "access-control-allow-origin",
      "value": "<allowed-host-origin>"
    }
  ]
}
```

채팅 웹 사이트의 오리진뿐만 아니라 지원되는 LLM 플랫폼에서 요구하는 실제 위젯 샌드박스 오리진을 사용하십시오. `.plain.html` 응답과 가져온 JavaScript, 스타일, 글꼴 및 미디어에서 CORS를 확인합니다.

플랫폼의 자동 EDS 설정은 공용 위젯 배달을 위해 `access-control-allow-origin: *`을(를) 구성하며, 더 좁은 기존 값을 대체할 수 있습니다. 자격 증명 원본 간 요청이 없고 보안 요구 사항이 허용하는 경우 의도적으로 공개 리소스에 대해서만 이 정책을 사용하십시오. 제한된 기존 사이트를 관리하는 경우 연결하기 전에 정책을 검토하십시오. 위젯이 작동하도록 하려면 개인 사이트 콘텐츠를 노출하지 마십시오.

EDS 구성에 대한 자세한 내용은 [구성 서비스](https://aem.live/docs/config-service-setup)를 참조하세요.

## 작업 구성

[!DNL LLM Apps]에서 작업을 열고 **[!UICONTROL LLM 위젯 세부 정보]**&#x200B;를 선택합니다. **[!UICONTROL 위젯 정보]**&#x200B;에서 **[!UICONTROL 위젯 유형]**&#x200B;을(를) **[!UICONTROL EDS]**(으)로 설정한 다음 **[!UICONTROL 템플릿 URL]**&#x200B;을(를) 확장합니다.

다음을 입력합니다.

- **[!UICONTROL 스크립트 URL]**

  ```text
  https://main--<repo>--<owner>.aem.page/scripts/aem-embed.js
  ```

- **[!UICONTROL 위젯 URL]**

  ```text
  https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
  ```

위에서 확인한 것과 동일한 작업 사이트 원본 및 페이지로 예제를 바꿉니다. 최소 권한을 사용하여 CSP 도메인 및 브라우저 권한을 구성합니다. EDS 원본은 페이지 조각 가져오기와 같은 연결과 블록 스크립트 및 스타일과 같은 리소스에 대해 모두 허용되어야 합니다. 위젯에 필요한 경우에만 다른 원본을 추가하십시오.

**[!UICONTROL 변경 내용 저장]**&#x200B;을 선택한 다음 테스트 중인 환경에 다시 배포합니다. 저장된 위젯 구성은 배포될 때까지 실행 중인 MCP 서버를 업데이트하지 않습니다.

필드 정의는 [작업 및 위젯 필드](/help/reference/reference-docs.md)를 참조하세요.

## 통합 테스트

1. EDS 페이지를 직접 미리 보고 샘플 데이터 폴백을 확인합니다.
2. 핸들러를 로컬에서 테스트하고 해당 `structuredContent`을(를) 블록에서 예상한 모양과 비교합니다.
3. 핸들러 및 작업 메타데이터를 스테이지에 배포합니다. EDS 코드 및 콘텐츠 게시는 별도의 단계입니다.
4. 지원되는 LLM 플랫폼에서 작업을 호출합니다. [Test in ChatGPT](/help/guides/test-in-chatgpt.md) 또는 [Test in Cloud](/help/guides/test-in-claude.md)를 참조하십시오.
5. 로드, 성공, 비어 있음 및 오류 상태를 확인합니다.

페이지가 직접 작동하지만 LLM 플랫폼에서는 작동하지 않는 경우 CORS, CSP, HTTPS URL 및 `structuredContent` 셰이프를 확인하십시오. [문제 해결](/help/reference/troubleshooting.md)을 참조하세요.
