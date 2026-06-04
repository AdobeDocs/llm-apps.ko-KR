---
title: ChatGPT에서 테스트
description: 배포된 Adobe LLM 앱을 ChatGPT에 추가하고 실제 대화에서 테스트하는 방법에 대해 알아봅니다.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '798'
ht-degree: 2%

---


# [!DNL ChatGPT]에서 테스트

>[!IMPORTANT]
>
>**면책조항:** [!DNL LLM Apps]의 베타 릴리스입니다. 여기에 표시된 기능, 워크플로우 및 UI가 반드시 애플리케이션 또는 제품의 최종 상태를 나타내지는 않습니다.

>[!NOTE]
>
>이 안내서에서는 [!DNL ChatGPT]을(를) 예로 사용합니다. 일반적인 단계(MCP 서버 URL 등록 및 대화에서 테스트)는 다른 LLM 플랫폼에도 적용되지만 설정 흐름 및 UI는 달라집니다.

배포에 성공하면 앱이 [!DNL Adobe I/O Runtime]에서 실행되고 MCP 서버 URL을 노출합니다. 이 안내서에서는 [!DNL ChatGPT]에 추가하고 실제 대화에서 테스트하는 방법을 보여 줍니다.

## 플랜 요구 사항

[!DNL ChatGPT]에 사용자 지정 개발자 앱을 추가하는 것은 OpenAI의 구독 계층에 의해 제어됩니다. 이는 [!DNL LLM Apps] 제한이 아니라 OpenAI가 현재 사용자 지정 MCP 앱에 대한 액세스를 관리하는 방식입니다.

| [!DNL ChatGPT] 플랜 | 사용자 정의 MCP 앱 |
|--------------|-----------------|
| 무료 | 사용할 수 없음 |
| 이동 | 사용할 수 없음 |
| 더하기 | 사용할 수 없음 |
| 프로 | 사용 가능 |
| 상업 | 사용 가능 |
| 엔터프라이즈 / Edu | 사용 가능 |

>[!NOTE]
>
>무료, 이동 또는 플러스 플랜을 사용하는 경우 **배포된 앱을 [!DNL ChatGPT]에 추가할 수 없습니다**. **Pro**(으)로 업그레이드하거나 조직의 관리자에게 **비즈니스** 또는 **Enterprise** 작업 영역에서 사용하도록 설정하도록 요청하세요.

## 개발자 모드 활성화

사용자 지정 MCP 앱을 추가하려면 [!DNL ChatGPT] 계정에서 **개발자 모드**&#x200B;를 활성화해야 합니다. 팔로우
확인 및 활성화하려면 아래 단계를 따르십시오.

### 설정 열기

왼쪽 아래에서 프로필 아바타를 클릭한 다음 **[!UICONTROL 설정]**&#x200B;을 클릭합니다.

![ChatGPT — 설정 메뉴](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

### 앱으로 이동

설정 대화 상자의 왼쪽 사이드바에서 **[!UICONTROL 앱]**&#x200B;을 선택합니다. 맨 아래에 있는 **[!UICONTROL 고급 설정]**&#x200B;을 클릭합니다.

![ChatGPT — 앱 설정](/help/assets/guide-test-chatgpt/chatgpt-apps-settings.png)

### 개발자 모드 켜기

**[!UICONTROL 개발자 모드]** 토글이 켜져 있는지 확인합니다(파란색). 이를 통해 확인되지 않은 사용자 지정 MCP 서버 URL을 등록할 수 있습니다.

>[!NOTE]
>
>개발자 모드에서는 OpenAI에서 검토하지 않은 앱을 허용하므로 *위험 상승* 레이블이 지정됩니다. [!DNL ChatGPT]은(는) 개발자 모드 앱을 사용하는 대화에 대한 메모리를 자동으로 사용하지 않도록 설정합니다.

![ChatGPT — 개발자 모드 사용](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

## [!DNL ChatGPT]에 앱 추가

### MCP 서버 URL 복사

[!DNL LLM Apps]의 **앱 세부 정보** 페이지로 이동하여 **[!UICONTROL 앱 테스트]** 섹션을 찾습니다. **스테이징** 또는 **프로덕션** URL을 복사합니다. 다음과 같습니다.

```
https://<namespace>.adobeioruntime.net/api/v1/web/llm-apps/mcp
```

### 앱 페이지를 엽니다.

[!DNL ChatGPT]에서 **[!UICONTROL 설정] → [!UICONTROL 앱]**(으)로 이동합니다.

![ChatGPT — 앱 페이지](/help/assets/guide-test-chatgpt/chatgpt-apps-page.png)

### 새 앱 만들기

고급 설정 행에서 **[!UICONTROL 앱 만들기]**&#x200B;를 클릭합니다.

![ChatGPT — 앱 대화 상자 만들기](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

다음을 입력합니다.

| 필드 | 값 |
|-------|-------|
| **아이콘** | 선택 사항 — 128x128 PNG 업로드(최대 10KB) |
| **이름** | 앱의 표시 이름(예: *내 브랜드 앱*) |
| **설명** | 앱이 수행하는 작업에 대한 간단한 설명 |
| **MCP 서버 URL** | [!DNL LLM Apps]의 URL 붙여넣기 |
| **[!UICONTROL 인증]** | *인증 안 함* 선택 |

**이해하고 있고 계속 진행하겠습니다** 확인란 선택 - MCP 서버가
은(는) OpenAI에서 검토하지 않았습니다. **만들기**&#x200B;를 클릭하십시오.

### 앱이 활성화되었는지 확인

앱이 생성되면 **[!UICONTROL 활성화된 앱]** 아래에 **[!UICONTROL 개발]** 배지와 함께 표시되어 활성 상태인지 확인합니다.

>[!NOTE]
>
>앱은 **초안**&#x200B;에도 표시됩니다. 이는 개발자 모드에서 만든 비공개 앱으로 계정에만 표시됩니다.

이제 앱을 [!DNL ChatGPT] 대화에서 사용할 준비가 되었습니다.

![ChatGPT — 앱 사용](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

## 대화에서 테스트

앱이 활성화되면 [!DNL ChatGPT]에서 새 대화를 시작하십시오. 질문을 하기 전에 두 가지 방법 중 하나를 사용하여 앱을 첨부합니다.

### 옵션 1 — 메뉴에서 선택합니다.

채팅 입력에서 **+** 버튼을 클릭한 다음 **자세히**&#x200B;를 클릭하여 사용 가능한 도구의 전체 목록을 확장합니다. 목록에서 앱을 선택하여 현재 대화에 연결합니다.

![ChatGPT — 메뉴에서 앱 선택](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

### 옵션 2 - @mention 사용

채팅 입력에 **@**&#x200B;을(를) 입력하고 드롭다운에서 앱을 선택합니다. 이렇게 하면 앱이 인라인으로 첨부되므로 동일한 메시지에 질문을 계속 입력할 수 있습니다.

>[!NOTE]
>
>동일한 앱에서 **@mention**&#x200B;을(를) 두 번 사용하면 해당 앱이 선택 해제되고 대화에서 제거됩니다.

![ChatGPT — 앱 @mention](/help/assets/guide-test-chatgpt/chatgpt-mention-app.png)

선택하면 앱이 인라인으로 첨부되므로 동일한 메시지에 질문을 입력할 수 있습니다.

![ChatGPT — &#x200B;](/help/assets/guide-test-chatgpt/chatgpt-mention.png)을(@mention) 통해 첨부된 앱

### 결과 보기

앱이 연결되면 구성된 작업 중 하나에 맞는 질문을 입력하십시오(예: *&quot;제품 표시&quot;*). [!DNL ChatGPT]이(가) 관련 작업에 일치시키고 입력 매개 변수를 추출하여 [!DNL Adobe I/O Runtime]에서 처리기를 호출하고 결과를 렌더링합니다.

![ChatGPT — 작업 결과](/help/assets/guide-test-chatgpt/chatgpt-response.png)

응답에는 다음이 포함됩니다.

- **EDS 위젯** — 이미지, 등급 및 작업 버튼이 포함된 풍부한 UI 구성 요소.
- **텍스트 응답** — 위젯 아래의 [!DNL ChatGPT]은(는) 핸들러에서 반환된 `content`을(를) 사용합니다.
결과에 대한 자연어 요약을 작성하다.
- **상태 표시기** — 작업 만들기 대화 상자에서 구성한 *상태 텍스트를 호출했습니다*.

## 다음 단계

- **추가 작업 추가** — UI에서 추가 작업을 정의하고 해당 처리기를 작성한 다음 다시 배포합니다.
- **프로덕션에 배포** — 스테이지에서 테스트한 경우 라이브 환경을 위해 프로덕션에 배포하십시오.
- **팀과 공유** — [앱 세부 정보] 페이지에서 **URL 복사**&#x200B;를 사용하여 MCP 서버 URL을 팀원과 공유합니다.

