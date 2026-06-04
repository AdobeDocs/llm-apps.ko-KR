---
title: 문제 해결
description: Adobe LLM 앱을 빌드하고, 배포하고, 테스트할 때의 일반적인 문제에 대한 솔루션입니다.
source-git-commit: c0f4affd586e77379f5c79731c7aed2c7a5d5d20
workflow-type: tm+mt
source-wordcount: '435'
ht-degree: 0%

---


# 문제 해결

>[!IMPORTANT]
>
>**면책조항:** [!DNL LLM Apps]의 베타 릴리스입니다. 여기에 표시된 기능, 워크플로우 및 UI가 반드시 애플리케이션 또는 제품의 최종 상태를 나타내지는 않습니다.

## 일반적인 문제

| 증상 | 가능한 원인 | 해결 방법 |
|---------|----------------|-------------|
| LLM 플랫폼에 앱이 표시되지 않음 | LLM 플랫폼 구독이 사용자 지정 MCP 앱을 지원하지 않거나 개발자 모드가 활성화되지 않았습니다. | 플랜이 사용자 정의 MCP 앱을 지원하는지 확인합니다. **앱의 설정 → 고급 설정→1&rbrace;에서 개발자 모드 사용** |
| LLM 플랫폼에서 &quot;연결하지 못함&quot; 오류 | MCP 서버 URL이 올바르지 않거나 배포에 실패했습니다. | 앱 세부 사항 페이지에서 URL을 다시 확인합니다. 배포 기록에서 실패 확인 |
| 작업이 호출되지 않음 | LLM 플랫폼에서 사용자의 질문을 작업에 일치시킬 수 없습니다. | 명시적으로 호출하려면 `@YourApp`을(를) 사용합니다. 작업 설명을 개선하여 모델이 인텐트를 일치시킬 수 있도록 지원 |
| 위젯이 렌더링되지 않음 | EDS 위젯 URL 또는 CSP 도메인이 잘못 구성됨 | [작업 만들기] 대화 상자에서 스크립트 URL 및 위젯 포함 URL을 확인합니다. CSP 리소스 및 연결 도메인에 EDS 원본이 포함되어 있는지 확인합니다. |
| 비어 있거나 오류 응답 | 핸들러에 버그가 있거나 없습니다. | 먼저 `npm start`을(를) 사용하여 로컬에서 테스트합니다. [로컬 개발](/help/reference/development.md#local-development)을 참조하세요. |
| 위젯이 로드되지만 데이터가 표시되지 않음 | `structuredContent` 셰이프가 블록에 필요한 셰이프와 일치하지 않습니다. | 블록의 `decorate` 함수에 `bridge.toolResult`을(를) 기록하고 처리기 출력과 비교합니다. |
| &quot;복제 및 구축&quot;에서 구축 실패 | 저장소에 `npm install` 또는 Webpack 빌드 오류가 있습니다. | 오류를 재현하려면 로컬에서 `npm install && npm run build`을(를) 실행하십시오. |
| &quot;자격 증명 수집&quot;에서 배포가 실패합니다. | 저장소가 연결되지 않았거나 Developer Console 프로젝트가 잘못 구성되었습니다. | 저장소가 앱 세부 정보 설정 페이지에 연결되어 있는지 확인합니다. |
| 위젯 로드 시 CORS 오류 발생 | EDS 사이트에 `access-control-allow-origin` 헤더가 없습니다. | `admin.hlx.page`을(를) 통해 CORS 헤더 구성 |
| HTTP 헤더 편집기에서 CORS 헤더를 저장할 때 `404 Error updating config: config not found`을(를) 반환합니다. | 사이트 구성에 `headers` 섹션이 없습니다. | 아래의 [EDS 사이트 구성 헤더 섹션 초기화](#initialize-the-eds-site-config-headers-section)를 참조하십시오. |
| 위젯은 미리보기에서 렌더링되지만 LLM 플랫폼에서는 렌더링되지 않음 | 블록은 미리보기 모드에서 샘플 데이터로 축소되지만 라이브 데이터에서 실패합니다 | MCP 검사기 또는 curl을 사용하여 실제 `structuredContent`(으)로 테스트 |

## EDS 사이트 구성 헤더 섹션 초기화

HTTP 헤더 편집기에서 `404 Error updating config: config not found`을(를) 반환하는 경우 사이트 구성에 `headers` 섹션이 없습니다. 수동으로 수정:

1. [tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html)&#x200B;(으)로 이동하여 조직과 사이트를 입력하고 **[!UICONTROL 가져오기]**&#x200B;를 클릭합니다.
2. 브라우저 DevTools(네트워크 탭)를 열고 Fetch 요청에서 `x-auth-token` 헤더의 값을 복사합니다.
3. 현재 사이트 구성 검색:

   ```bash
   curl -H "x-auth-token: $TOKEN" \
     https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json > config.json
   ```

4. `config.json`을(를) 열고 `"headers": {}`을(를) JSON 개체에 추가합니다.
5. 업데이트된 구성을 다시 게시합니다.

   ```bash
   curl -X POST \
     -H "x-auth-token: $TOKEN" \
     -H "Content-Type: application/json" \
     -d @config.json \
     "https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json"
   ```

6. 헤더 편집기를 다시 로드하고 `Access-Control-Allow-Origin` 헤더를 정상적으로 저장합니다.

