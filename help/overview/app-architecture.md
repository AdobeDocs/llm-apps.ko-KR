---
title: 앱 연결 방법
description: 소유한 부분(작업 메타데이터, 핸들러 코드 및 위젯)이 빌드 시간과 런타임 시 실행되는 LLM 앱으로 어떻게 통합되는지 자세히 살펴보십시오.
source-git-commit: e066f66b37914e2f747176e865e26dcc074bff20
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 0%

---


# 앱 연결 방법 {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

## 한 문장

**LLM 앱**&#x200B;은(는) **모델을 통해 노출된 도구인**&#x200B;작업&#x200B;**집합입니다.
단일 끝점에 게시하는 컨텍스트 프로토콜** 또는 **MCP**). 채팅 호스트
[!DNL ChatGPT]과(와) 마찬가지로 이러한 도구를 검색하고 대화 중간에 호출하여 렌더링합니다.
결과가 포함된 **대화형 위젯**(채팅 바로 내부).

## 전체 배선, 빌드 → 실행

**다이어그램 1 — 작성 시간** 세 개의 별도 표면을 가지고 있습니다. 플랫폼이 융합합니다
하나의 배포 가능한 앱에 포함됩니다.

```
┌─────────────────────┐   ┌─────────────────────┐   ┌─────────────────────┐
│ 1  LLM Apps UI      │   │ 2  Action Handler   │   │ 3  Widget repo      │
│                     │   │    repo             │   │                     │
│ Create, edit, and   │   │                     │   │ Each widget is an   │
│ manage your action  │   │ Business logic —    │   │ EDS block,          │
│ definitions here    │   │ built from our      │   │ published to a      │
│ (metadata)          │   │ boilerplate         │   │ public URL on       │
│                     │   │                     │   │ *.aem.page          │
│                     │   │ Returns content     │   │                     │
│                     │   │ (for the LLM) +     │   │                     │
│                     │   │ structuredContent   │   │                     │
│                     │   │ (for the widget)    │   │                     │
└─────────────────────┘   └─────────────────────┘   └─────────────────────┘
           │                         │                         │
           └─────────────────────────┼─────────────────────────┘
                                     ▼
                       ┌─────────────────────────────┐
                       │ LLM Apps deploy pipeline    │
                       │ Combines the 3 surfaces     │
                       │ into one running app        │
                       └─────────────────────────────┘
                                     │
                                     ▼
                 ┌─────────────────────────────────────────┐
                 │ ONE MCP server on Adobe I/O Runtime     │
                 │ https://<ns>.adobeioruntime.net/.../mcp │
                 └─────────────────────────────────────────┘
```

- **LLM 앱 UI** — 각 작업의 정의를 만들고, 편집하고, 관리하는 곳:
해당 **코드 식별자**(여기서 한 번 설정한 고정 슬러그(예: `my_action`)
UI, 핸들러 및 위젯에서 이 동일한 작업을 함께 연결합니다.
설명, 입력 스키마, 위젯 선택 및 CSP/가시성 플래그. 코드 없음.
- **작업 처리기 리포지토리** — 서버측 리포지토리(보일러판에서 스캐폴딩)
비즈니스 논리를 작성하는 곳. 모든 핸들러 함수는 다음 두 가지를 반환합니다.
  `content`(*LLM*&#x200B;이(가) 읽는 일반 텍스트) 및 `structuredContent`(데이터 개체
  *위젯*&#x200B;이 읽음).
- **위젯 리포지토리** — 각 위젯이 블록으로 존재하며 가져오는 EDS 리포지토리
공개 `*.aem.page` URL에 게시되었습니다. 각 블록은
  [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk),
위젯과 호스트/서버 간의 브리지입니다. **MCP 앱을 구현합니다.
사양** — 기본 프로토콜 — 단순 API 및 it
에서는 LLM 호스트 자체를 추출하므로 동일한 위젯이 수정되지 않은 상태로 작동합니다.
  [!DNL ChatGPT], [!DNL Claude], Gemini 또는 기타 MCP 호스트입니다.

**다이어그램 2 — 런타임.** 사용자가 한 번 보내는 모든 메시지에 발생하는 결과
이 서버는 라이브 서버입니다. [!DNL ChatGPT]을(를) 예제 호스트로 표시 —
[!DNL Claude]과(와) 같은 MCP 호스트에 대해 동일한 시퀀스가 재생됩니다.

```
┌── ChatGPT  (the MCP host) ──────────────────────────────────────────────┐
│  1  tools/list  >  sees `my_action` + its description + input schema    │
│  2  user asks   >  "I need help with …"                                 │
│  3  model picks >  the description matches -> calls this tool           │
│  4  tools/call  >  { name: "my_action", arguments: {situation, ...} }   │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                 routes by CODE IDENTIFIER  ->  my_action
                                     ▼
┌── Adobe I/O Runtime ────────────────────────────────────────────────────┐
│  actions/my_action/index.js  --  your handler runs                      │
│  returns  { content -> text for the model , structuredContent -> data } │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌── Rendered inside the conversation ─────────────────────────────────────┐
│  5  render      >  ChatGPT renders the Widget repo's EDS block          │
│  The EDS block reads the structuredContent the Action Handler repo      │
│  returned, and draws the interactive card — live, inside the chat.      │
└─────────────────────────────────────────────────────────────────────────┘
```
