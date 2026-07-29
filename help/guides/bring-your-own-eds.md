---
title: 나만의 Edge Delivery Services 프로젝트 가져오기
description: 기존 Adobe Edge Delivery Services 프로젝트를 Adobe LLM 앱 작업에 연결합니다.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '472'
ht-degree: 2%

---


# 나만의 EDS 프로젝트 가져오기 {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

이미 EDS(Edge Delivery Services) 프로젝트가 있거나 온보딩 에이전트 없이 앱을 만든 경우 이 안내서를 사용하십시오.

온보딩 에이전트가 위젯을 만든 경우 대신 [생성된 위젯 사용자 지정](/help/guides/widgets.md)을 따르십시오. 생성된 프로젝트에는 여기에 설명된 SDK 파일, 블록, 컨텐츠 및 작업 구성이 이미 포함되어 있습니다.

**여정:** EDS 프로젝트→ 준비하여 SDK → 빌드를 설치하고 블록→ 게시하여 배포 및 테스트 작업→ 구성합니다.

## 시작하기에 앞서

다음이 필요합니다.

- [AEM 코드 동기화](https://github.com/apps/aem-code-sync)가 설치된 EDS 리포지토리입니다.
- 저장소에 종속성을 추가하고 블록을 만들 수 있는 권한입니다.
- EDS 사이트에 대한 응답 헤더를 구성할 수 있는 권한입니다.
- `structuredContent`을(를) 반환하는 핸들러가 있는 [!DNL LLM Apps]의 작업입니다.

## LLM 앱 설치 SDK

EDS 프로젝트 루트에서:

```bash
npm install @adobe/llmapps-sdk
```

패키지는 위젯 진입점 및 브리지 구현을 프로젝트에 복사합니다.

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

액션에 사용된 스크립트 URL이 `scripts/aem-embed.js`을(를) 가리킵니다.

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
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }

  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];

  const list = document.createElement('ul');
  products.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product.name ?? 'Product');
    list.append(item);
  });

  block.replaceChildren(list);

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

텍스트 값을 인코딩하는 DOM API를 사용합니다. 외부 데이터를 HTML에 연결하지 마십시오.

## 위젯 페이지 작성 및 게시

위젯에 대한 EDS 페이지 하나를 만들고 해당 페이지에 블록을 추가합니다. 페이지를 게시합니다.

라이브 페이지 URL이 작업의 위젯 URL이 됩니다.

```text
https://main--<repo>--<owner>.aem.live/<widget-page>
```

페이지 경로가 작업 이름과 일치할 필요는 없지만, 일관된 규칙을 사용하면 프로젝트를 보다 쉽게 유지 관리할 수 있습니다.

## CORS 구성

위젯은 EDS 페이지와 원본 간에 스크립트, 스타일, 블록 및 미디어를 로드합니다. EDS 사이트에 대한 헤더를 구성합니다.

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

지원되는 LLM 플랫폼에 필요한 특정 호스트 원본을 사용하십시오. 위젯이 의도적으로 공개되고 자격 증명된 원본 간 요청을 사용하지 않으며 보안 요구 사항이 허용하는 경우에만 `*`을(를) 사용하십시오.

EDS 구성에 대한 자세한 내용은 [구성 서비스](https://aem.live/docs/config-service-setup)를 참조하세요.

## 작업 구성

[!DNL LLM Apps]에서 작업을 열고 **[!UICONTROL 위젯 메타데이터]**&#x200B;를 선택합니다.

다음을 입력합니다.

- **[!UICONTROL 스크립트 URL]**

  ```text
  https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js
  ```

- **[!UICONTROL 위젯 URL]**

  ```text
  https://main--<repo>--<owner>.aem.live/<widget-page>
  ```

최소 권한을 사용하여 CSP 도메인 및 브라우저 권한을 구성합니다. 위젯에 필요한 원본 및 기능만 추가합니다.

필드 정의는 [작업 및 위젯 필드](/help/reference/reference-docs.md)를 참조하세요.

## 통합 테스트

1. EDS 페이지를 직접 미리 보고 샘플 데이터 폴백을 확인합니다.
2. 핸들러를 로컬에서 테스트하고 해당 `structuredContent`을(를) 블록에서 예상한 모양과 비교합니다.
3. 앱을 스테이징에 배포합니다.
4. [!DNL ChatGPT]에서 작업을 호출합니다.
5. 로드, 성공, 비어 있음 및 오류 상태를 확인합니다.

페이지가 직접 작동하지만 LLM 플랫폼에서는 작동하지 않는 경우 CORS, CSP, HTTPS URL 및 `structuredContent` 셰이프를 확인하십시오. [문제 해결](/help/reference/troubleshooting.md)을 참조하세요.
