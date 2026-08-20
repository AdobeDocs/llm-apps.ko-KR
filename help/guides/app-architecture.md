---
title: 앱 연결 방법
description: 소유한 부분(작업 메타데이터, 핸들러 코드 및 위젯)이 빌드 시간과 런타임 시 실행되는 LLM 앱으로 어떻게 통합되는지 자세히 살펴보십시오.
source-git-commit: 2f3480b3667a6ab7c4ed65b999eed4638c383edb
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

**LLM 앱**&#x200B;은(는) 단일 끝점에 게시하는 **작업** 집합(각각 **모델 컨텍스트 프로토콜** 또는 **MCP**&#x200B;을 통해 노출된 도구)입니다. [!DNL ChatGPT]과(와) 같은 채팅 호스트가 이러한 도구를 검색하고 대화 중간에 호출한 다음 그 결과와 함께 **대화형 위젯**&#x200B;을(를) 채팅 내에서 바로 렌더링합니다.

## 전체 배선, 빌드 → 실행

**다이어그램 1 — 작성 시간** 세 개의 별도 표면을 소유합니다. 플랫폼은 이러한 표면을 하나의 배포 가능한 앱에 융합합니다.

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

- **LLM 앱 UI** — 각 작업의 정의를 만들고, 편집하고, 관리하는 위치: **코드 식별자**(UI, 핸들러 및 위젯에서 동일한 작업을 함께 연결하는 여기서 한 번 설정한 고정 슬러그(예: `my_action`), 설명, 입력 스키마, 위젯 선택 및 CSP/가시성 플래그. 코드 없음.
- **작업 처리기 리포지토리** - 비즈니스 논리를 작성하는 서버측 리포지토리(Boilerplate에서 스캐폴딩)입니다. 모든 처리기 함수는 `content`(*LLM*&#x200B;이(가) 읽는 일반 텍스트)와 `structuredContent`(*위젯*&#x200B;이(가) 읽는 데이터 개체), 이렇게 두 가지를 반환합니다.
- **위젯 저장소** — 각 위젯이 블록으로 존재하고 공개 `*.aem.page` URL에 게시되는 EDS 저장소입니다. 각 블록은 위젯과 호스트/서버 간의 브리지인 [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk)을(를) 사용합니다. 간단한 API 이면에 **MCP 앱 사양**(기본 프로토콜)을 구현하고 LLM 호스트 자체를 추출하므로 동일한 위젯이 [!DNL ChatGPT], [!DNL Claude], Gemini 또는 다른 MCP 호스트에서 수정되지 않은 상태로 작동합니다.

**다이어그램 2 — 런타임.** 해당 서버가 활성 상태가 되면 사용자가 보내는 모든 메시지에서 발생하는 결과. [!DNL ChatGPT]을(를) 예제 호스트로 표시합니다. [!DNL Claude]과(와) 같은 모든 MCP 호스트에 대해 동일한 시퀀스가 재생됩니다.

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
