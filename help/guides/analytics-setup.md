---
title: Adobe Analytics 및 Customer Journey Analytics 설정
description: Adobe LLM 앱 이벤트를 Adobe Experience Platform 데이터 스트림을 통해 고유한 Adobe Analytics 보고서 세트 및 Customer Journey Analytics 데이터 보기에 전달합니다.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '2962'
ht-degree: 2%
---

# Adobe Analytics 및 Customer Journey Analytics 설정 {#analytics-setup}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]이(가) 현재 Beta에 있습니다.
>
>여기에 표시된 기능, 워크플로우 및 UI가 반드시 제품의 최종 상태를 나타내지는 않습니다. Beta에 참여하려면 llm-apps-beta@adobe.com으로 이메일을 보내십시오.

앱의 이벤트를 [!DNL Adobe Analytics], [!DNL Customer Journey Analytics] 또는 둘 다에 보냅니다. [!DNL LLM Apps]이(가) 소유한 데이터 스트림을 통해 이벤트를 [!DNL Adobe Experience Platform] Edge Network에 전달합니다. 데이터스트림은 이벤트를 추가한 각 서비스로 라우팅합니다.

- **[!DNL Adobe Analytics]**&#x200B;은(는) LLM 플랫폼 호출의 동작, 사용자가 선택한 링크를 연결하는 위젯 및 각 호출 이면의 의도를 보고합니다.
- **[!DNL Customer Journey Analytics]**&#x200B;은(는) [!DNL Experience Platform] 데이터에 빌드하는 데이터 보기에서 동일한 이벤트를 개인별로 분석합니다.

이 설정은 [!DNL LLM Apps]의 기본 제공 분석과 별개입니다.

**이 안내서:** [!DNL Experience Platform], [!DNL Adobe Analytics] 및 [!DNL Customer Journey Analytics]에서 각 제품 화면의 Experience League 프로시저에 대한 링크를 사용하여 [!DNL LLM Apps]에 필요한 설정을 알아봅니다. 그런 다음 앱에서 Analytics를 활성화하고 스테이지에 배포하고 프로덕션에 배포하기 전에 데이터를 확인합니다.

## 체크리스트 설정 {#setup-checklist}

사용하는 서비스에 대해 **필수**&#x200B;로 표시된 모든 단계를 완료하십시오. 건너뛴 단계에는 오류가 거의 표시되지 않습니다. 대신 데이터가 도착하지 않습니다.

| 단계 | [!DNL Adobe Analytics] | [!DNL Customer Journey Analytics] |
|---|---|---|
| [1. ID 네임스페이스 만들기](#identity-namespace) | 필수 | 필수 |
| [2. 스키마 만들기](#schema) | 필수 | 필수 |
| [3. 데이터 집합 만들기](#dataset) | 필요 없음 | 필수 |
| [4. 보고서 세트 준비](#report-suite) | 필수 | 필요 없음 |
| [5. 데이터 스트림 만들기](#datastream) | 필수 | 필수 |
| [6. Analytics를 사용하도록 설정하고 &#x200B;](#enable-analytics) 단계에 배포 | 필수 | 필수 |
| [7. 연결 및 데이터 보기 만들기](#cja) | 필요 없음 | 필수 |
| [8. 데이터를 확인하고 프로덕션에 배포](#verify) | 필수 | 필수 |

동일한 [!DNL Experience Platform] 샌드박스에서 네임스페이스, 스키마, 데이터 세트 및 데이터 스트림을 만듭니다. 프로덕션에서 보고할 샌드박스 및 데이터 스트림을 사용합니다. 앱에 데이터스트림을 저장한 후 LLM 앱 팀에 문의하여 변경합니다.

## 시작하기에 앞서 {#before-you-begin}

다음이 필요합니다.

- **지원되는 런타임과 SDK.** 처리기 리포지토리는 `@adobe/llm-apps-runtime` 1.2.0 이상을 사용해야 합니다. EDS 위젯의 경우 LLM 앱 SDK 0.1.13 이상을 사용합니다. Analytics를 사용하기 전에 이전 앱을 최신 런타임 및 SDK으로 업데이트하려면 [기존 앱을 업그레이드](/help/guides/upgrade-existing-app.md)하십시오. 이러한 요구 사항을 충족하는 앱은 업그레이드할 필요가 없습니다.
- **에이전트 필드 그룹** 조직 정보 및 대화 이벤트 필드 그룹에 대해 조직이 활성화되어야 합니다. 스키마에 필드 그룹을 추가할 때 이러한 필드 그룹이 표시되지 않으면 Adobe 계정 팀에 문의하십시오. [대화 인사이트 구현](https://experienceleague.adobe.com/ko/docs/analytics-platform/using/conversation-insights/implement#schema-and-datasets)을 참조하세요. 작업 호출은 [!DNL Experience Platform]의 Beta에 있는 에이전트 정보의 `agenticExperience.tools` 필드에 기록됩니다.
- 샌드박스에서 id 네임스페이스, 스키마, 데이터 세트 및 데이터 스트림을 만들 수 있는 **[!DNL Experience Platform]권한**.
- **보고서 세트 및 처리 규칙을 관리할 수 있는 [!DNL Adobe Analytics]:** 권한입니다. 제품 관리자는 기본적으로 둘 다 있습니다.
- **[!DNL Customer Journey Analytics]의 경우:** 제품 관리자가 연결 및 데이터 보기를 만들 수 있습니다.

## 앱에서 전송하는 내용 {#what-your-app-sends}

Analytics를 활성화하고 배포하면 앱에서 두 종류의 이벤트를 전송합니다.

| 이벤트 | `eventType` | 전송 시점 | 포함 |
|---|---|---|---|
| 액션 호출 | `mcp.tool_call` | LLM 플랫폼은 실패하거나 로그인 프롬프트를 반환하는 호출을 포함하여 앱 작업 중 하나를 호출합니다. | 작업의 코드 식별자 및 호출 실패 여부입니다. 사용자 의도, 작업에서 수집되고 플랫폼이 하나를 제공합니다. 플랫폼에서 이를 제공하면(플랫폼 이름, 대화 ID, 플랫폼의 사용자 ID, 로케일 및 조직, 사용자의 대략적인 위치). |
| 위젯 상호 작용 | `web.webinteraction.linkClicks` | 누군가 Edge Delivery Services 위젯에서 링크 또는 버튼을 선택합니다. | 링크 또는 단추 레이블(최대 100자). 작업 호출에서 제공하는 경우 해당 대화 ID 및 turn ID입니다. 또한 Adobe Web SDK은 위젯 프레임 URL, 화면 및 브라우저 창 크기, 로컬 시간 및 시간대, 자체 버전을 추가합니다. |

[!DNL Adobe Analytics]에서 각 작업 호출은 `mcp:<code identifier>` 페이지의 페이지 보기로 계산되며 각 위젯 상호 작용은 링크 클릭으로 계산됩니다. [Adobe Analytics의 Edge Network 이벤트 유형](https://experienceleague.adobe.com/ko/docs/analytics/implementation/aep-edge/hit-types)을 참조하세요.

모든 작업 호출과 모든 위젯 상호 작용은 [!DNL Experience Platform] 데이터 집합에서 하나의 [!DNL Adobe Analytics] 히트 및 하나의 이벤트입니다. 트래픽이 많은 앱에서 Analytics를 활성화하기 전에 계약이 이 볼륨을 포함하는지 확인하십시오.

보고서를 읽을 때는 다음 세부 사항을 염두에 두십시오.

- **실패한 호출에는 로그인 프롬프트가 포함되어 있습니다.** 일부 작업에서 로그인이 필요한 경우 더 높은 실패율을 기대하십시오.
- **일부 거부된 호출은 기록되지 않습니다.** [!DNL ChatGPT] 이외의 플랫폼에서 올바른 로그인 또는 필수 범위 없이 로그인이 필요한 작업을 호출할 때 작업이 실행되기 전에 호출이 거부되고 이벤트를 보내지 않습니다. 모든 작업에서 로그인이 필요한 앱에서는 로그인하지 않은 모든 호출이 동일한 방식으로 거부됩니다.
- **사용자 의도가 종종 없습니다.** 플랫폼에서는 제공 여부를 결정하므로 많은 호출에 아무 호출도 없고 **지정되지 않음**(으)로 표시됩니다.
- **플랫폼 세부 정보가 다릅니다.** 현재 [!DNL ChatGPT]에서 모든 항목을 제공합니다. [!DNL Claude]의 경우 플랫폼 이름만 기록됩니다.
- **페이지에서 위젯 레이블을 가져옵니다.** 안정적인 레이블을 설정하려면 `data-analytics` 특성을 링크 또는 단추에 추가하십시오. 그렇지 않으면 표시되는 텍스트가 사용됩니다.

사용자 의도를 수집하는 작업을 선택하려면 [분석 구성](/help/guides/create-action.md#configure-analytics)을 참조하십시오.

>[!NOTE]
>
>이벤트는 플랫폼 사용자 ID와 대략적인 위치를 포함할 수 있습니다. Analytics를 활성화하기 전에 개인정보 처리방침 및 데이터 거버넌스 설정을 검토하십시오.

### 이벤트가 사람을 식별하는 방법 {#identities}

| 이벤트 | 기본 ID | 네임스페이스 |
|---|---|---|
| 액션 호출 | LLM 플랫폼에서 ID를 제공하는 경우 해당 ID | `MCPHOSTUSER` |
| 위젯 상호 작용 | 작업 호출 중에 Edge Network에서 문제가 되는 ECID(Experience Cloud ID)입니다. 사용 가능한 경우 `MCPHOSTUSER` ID가 보조 ID로 포함됩니다. | `ECID` |

[!DNL Claude]과(와) 같은 일부 플랫폼은 사용자 ID를 제공하지 않습니다. 해당 플랫폼의 작업 호출에는 ID가 없으므로 [!DNL Customer Journey Analytics]에서 건너뜁니다.

>[!NOTE]
>
>각 작업 호출은 새 ECID를 가져오고, 위젯 상호 작용은 위젯을 표시한 작업 호출의 ECID를 사용합니다. 그 결과는 다음과 같습니다.
>
>- [!DNL Customer Journey Analytics]은(는) 사용자의 작업 호출을 한 사람으로 계산하지만 각 작업 호출에 대해 다른 사람으로 계산합니다.
>- [!DNL Adobe Analytics]은(는) 각 작업 호출을 새 방문자로 카운트할 수 있습니다.

## 1단계: ID 네임스페이스 만들기 {#identity-namespace}

**필수 항목:** [!DNL Adobe Analytics] 및 [!DNL Customer Journey Analytics].

작업 호출은 `MCPHOSTUSER` 네임스페이스에서 사람을 식별하므로 네임스페이스가 샌드박스에 있어야 합니다. 다음 설정을 사용하여 사용자 지정 네임스페이스를 만듭니다.

| 설정 | 값 |
|---|---|
| 표시 이름 | `MCP host user` 또는 선택한 다른 이름 |
| ID 심볼 | `MCPHOSTUSER`, 표시된 대로 정확히 |
| ID 유형 | **[!UICONTROL 개별 교차 장치 ID]** |

프로시저는 [사용자 지정 네임스페이스 만들기](https://experienceleague.adobe.com/ko/docs/experience-platform/identity/features/namespaces#create-namespaces)를 참조하십시오.

## 2단계: 스키마 만들기 {#schema}

**필수 항목:** [!DNL Adobe Analytics] 및 [!DNL Customer Journey Analytics].

데이터 스트림과 데이터 세트 모두 이 스키마를 사용합니다. 다음 설정으로 스키마를 만듭니다.

| 설정 | 값 |
|---|---|
| 스키마 유형 | **[!UICONTROL 표준]**, **[!UICONTROL 수동]** 옵션으로 생성됨 |
| 기본 클래스 | **[!UICONTROL 경험 이벤트]** |
| 필드 그룹 | 에이전트 정보, 대화 이벤트, 웹 세부 정보, 환경 세부 정보 및 구현 세부 정보 |
| 표시 이름 | 모든 이름(예: `AEM LLM Apps`) |

>[!IMPORTANT]
>
>5개의 필드 그룹을 모두 추가합니다. [!DNL LLM Apps]은(는) 작업 호출에 에이전트 정보 및 대화 이벤트가 필요합니다. 다른 세 개의 필드에는 이벤트에도 포함된 페이지, 링크, 장치, 환경 및 구현 필드가 포함됩니다. 필드 그룹이 없으면 [!DNL Experience Platform]에서 해당 필드를 삭제하거나 이벤트를 거부할 수 있습니다.

Adobe Analytics ExperienceEvent 필드 그룹은 필요하지 않습니다. 대신 처리 규칙을 통해 사용자 의도가 [!DNL Adobe Analytics]에 도달합니다. [4단계](#report-suite)를 참조하세요.

프로시저는 [새 스키마 만들기](https://experienceleague.adobe.com/ko/docs/experience-platform/xdm/ui/resources/schemas#create) 및 [필드 그룹 추가](https://experienceleague.adobe.com/ko/docs/experience-platform/xdm/ui/resources/schemas#add-field-groups)를 참조하십시오.

## 3단계: 데이터 세트 만들기 {#dataset}

**필수 항목:** [!DNL Customer Journey Analytics].

데이터 집합은 [!DNL Experience Platform]에 앱의 이벤트를 저장합니다. 다음 설정으로 데이터 세트를 만듭니다.

| 설정 | 값 |
|---|---|
| 생성 방법 | **[!UICONTROL 스키마에서 데이터 집합 만들기]** |
| 스키마 | [2단계](#schema)의 스키마 |
| 이름 | 모든 이름(예: `AEM LLM Apps`) |

프로시저는 [데이터 집합 만들기](https://experienceleague.adobe.com/ko/docs/experience-platform/catalog/datasets/user-guide#create)를 참조하십시오.

## 4단계: 보고서 세트 준비 {#report-suite}

**필수 항목:** [!DNL Adobe Analytics].

[!DNL Adobe Analytics]은(는) 페이지 이름과 링크 필드를 자동으로 매핑하므로 `mcp:<code identifier>` 페이지와 위젯 링크 클릭을 추가로 설정할 필요가 없습니다. 사용자 의도에 eVar 및 처리 규칙이 필요합니다.

### 보고서 세트 만들기 또는 선택 {#create-report-suite}

보고서 세트를 만들거나 기존 보고서 세트를 선택합니다. [!DNL Adobe Analytics]에서 추가하는 접두사를 포함하여 전체 보고서 세트 ID를 확인합니다. [5단계](#datastream)에서 ID가 필요합니다.

프로시저는 [보고서 세트 만들기](https://experienceleague.adobe.com/ko/docs/analytics/admin/admin-tools/manage-report-suites/c-new-report-suite/t-create-a-report-suite)를 참조하십시오.

### User Intent eVar 활성화 {#user-intent-evar}

보고서 세트의 전환 변수에서 다음 설정으로 eVar을 활성화합니다.

| 설정 | 값 |
|---|---|
| eVar | eVar |
| 이름 | `User Intent` |
| 유형 | **[!UICONTROL 텍스트 문자열]** |
| 할당 | **[!UICONTROL 가장 최근(마지막)]** |
| 다음 시기 이후에 만료 | **[!UICONTROL 방문]** |
| 상태 | **[!UICONTROL 사용]** |

보고서 세트가 이미 eVar3을 사용하는 경우 사용하지 않는 다른 eVar을 선택하고 처리 규칙에서 해당 eVar을 사용합니다.

프로시저는 [전환 변수 추가 또는 편집](https://experienceleague.adobe.com/ko/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/conversion-variables/conversion-var-admin#add-or-edit-conversion-variables)을 참조하십시오.

### 처리 규칙 만들기 {#processing-rule}

[!DNL Adobe Analytics]이(가) 사용자 의도를 eVar이 아닌 컨텍스트 데이터 변수로 수신합니다. 이유를 알아보려면 [암시적 매핑](https://experienceleague.adobe.com/ko/docs/analytics/implementation/aep-edge/xdm-var-mapping#implicit-mapping)을 참조하세요. 사용자 의도를 eVar에 복사하는 처리 규칙을 만듭니다.

- **조건:** 컨텍스트 데이터 변수 `a.x.conversation.prompt.raw.0.purpose`이(가) `intents`과(와) 같습니다.
- **작업:**&#x200B;이(가) **사용자 의도(eVar3)**&#x200B;의 값을 컨텍스트 데이터 변수 `a.x.conversation.prompt.raw.0.text`(으)로 덮어씁니다.

이 규칙이 없으면 **사용자 의도** 차원이 비어 있습니다. 규칙은 저장한 후 수집된 데이터에만 적용됩니다. 위젯 상호 작용은 사용자 의도를 전달하지 않으므로, 규칙이 사용자 의도를 변경하지 않습니다.

호출 실패 여부 또는 플랫폼 이름 등의 다른 필드도 컨텍스트 데이터 변수로 도착합니다. [!DNL Adobe Analytics]에서 이를 보고하려면 같은 방법으로 더 많은 처리 규칙을 만드십시오.

프로시저는 [처리 규칙 개요](https://experienceleague.adobe.com/ko/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-overview) 및 [처리 규칙 사용 사례](https://experienceleague.adobe.com/ko/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-use-cases)를 참조하십시오.

## 5단계: 데이터 스트림 만들기 {#datastream}

**필수 항목:** [!DNL Adobe Analytics] 및 [!DNL Customer Journey Analytics].

데이터스트림은 앱의 이벤트를 수신하여 사용자가 추가하는 각 서비스에 전달합니다.

### 데이터 스트림 만들기 {#create-datastream}

다음 설정으로 데이터 스트림을 만듭니다.

| 설정 | 값 |
|---|---|
| 이름 | 모든 이름(예: `AEM LLM Apps`) |
| 스키마 매핑 | [2단계](#schema)의 스키마 |

스키마와 동일한 샌드박스에서 데이터 스트림을 만듭니다. 데이터 스트림을 생성한 후에는 데이터 스트림의 샌드박스를 변경할 수 없습니다.

프로시저는 [데이터 스트림 만들기](https://experienceleague.adobe.com/ko/docs/experience-platform/datastreams/configure#create)를 참조하십시오.

### 서비스 추가 {#add-services}

각 대상에 대한 서비스 추가:

| 서비스 | 설정 | 다음에 필요합니다. |
|---|---|---|
| **[!UICONTROL Adobe Analytics]** | **[!UICONTROL 사용]**. **[!UICONTROL 보고서 세트 ID]**&#x200B;에서 [4단계](#report-suite)의 ID를 입력하십시오. | [!DNL Adobe Analytics] |
| **[!UICONTROL Adobe Experience Platform]** | **[!UICONTROL 사용]**. **[!UICONTROL 이벤트 데이터 세트]**&#x200B;의 [3단계](#dataset)에서 데이터 세트를 선택하십시오. 다른 옵션은 지운 상태로 두십시오. | [!DNL Customer Journey Analytics] |

![서비스 추가 - 예제 보고서 세트 ID로 Adobe Analytics 사용](/help/assets/guide-analytics/datastream-analytics-service.png)

![Adobe Experience Platform 서비스 - 이벤트 데이터 세트를 선택하고 기타 옵션을 지웠습니다](/help/assets/guide-analytics/datastream-platform-service.png)

프로시저는 [데이터 스트림에 서비스 추가](https://experienceleague.adobe.com/ko/docs/experience-platform/datastreams/configure#add-services), [Adobe Analytics 설정](https://experienceleague.adobe.com/ko/docs/experience-platform/datastreams/configure#analytics) 및 [Adobe Experience Platform 설정](https://experienceleague.adobe.com/ko/docs/experience-platform/datastreams/configure#aep)을 참조하십시오.

그런 다음 데이터 스트림 세부 정보에서 **[!UICONTROL 데이터 스트림 ID]**&#x200B;을(를) 복사합니다. [6단계](#enable-analytics)에서 필요합니다.

![데이터 스트림 세부 정보 - 사이드 패널에서 데이터 스트림 ID 및 매핑 스키마를 사용하여 Adobe Analytics 및 Adobe Experience Platform 서비스를 사용하도록 설정했습니다](/help/assets/guide-analytics/datastream-services.png)

## 6단계: Analytics를 활성화하고 스테이지에 배포 {#enable-analytics}

**필수 항목:** [!DNL Adobe Analytics] 및 [!DNL Customer Journey Analytics].

>[!IMPORTANT]
>
>저장한 후 데이터 스트림은 앱 설정에서 읽기 전용입니다. 변경하거나 Analytics를 끄려면 LLM 앱 팀에 문의하십시오. 저장하기 전에 데이터 스트림 ID를 확인하십시오. 여전히 데이터 스트림 자체에서 보고서 세트와 데이터 세트를 변경할 수 있습니다.

1. 앱을 열고 왼쪽 탐색에서 **[!UICONTROL 설정]**&#x200B;을 선택합니다.
2. **[!UICONTROL 분석]** 탭을 선택합니다.
3. **[!UICONTROL 분석 사용]**&#x200B;을 켭니다.
4. **[!UICONTROL 데이터 스트림 ID]**&#x200B;에서 [5단계](#datastream)의 ID를 붙여 넣으십시오.
5. **[!UICONTROL 요구 사항]**&#x200B;에서 **[!UICONTROL 세부 정보 보기]**&#x200B;를 선택하고 앱이 각 요구 사항을 충족하는지 확인하십시오.

   ![앱 설정, Analytics 탭 - Analytics 사용 설정, 빈 데이터 스트림 ID 필드 및 확장된 요구 사항 목록](/help/assets/guide-analytics/analytics-settings.png)

6. **[!UICONTROL 변경 내용 저장]**&#x200B;을 선택합니다.
7. 스테이징에 [앱 배포](/help/guides/deploy-your-app.md). 이 설정은 각 환경에 대한 다음 배포에 적용됩니다.

>[!NOTE]
>
>데이터 스트림은 전체 앱에 적용됩니다. 스테이지 및 프로덕션은 동일한 데이터 스트림에 이벤트를 전송하고 이벤트는 이벤트를 전송한 환경을 기록하지 않습니다. 스테이지의 테스트 호출이 프로덕션 트래픽과 동일한 보고서에 나타납니다.

## 7단계: 연결 및 데이터 보기 만들기 {#cja}

**필수 항목:** [!DNL Customer Journey Analytics].

### 연결 만들기 {#connection}

연결을 만들고 다음 설정으로 [3단계](#dataset)에서 데이터 집합을 추가하십시오.

| 설정 | 값 |
|---|---|
| 샌드박스 | 데이터 세트가 포함된 샌드박스 |
| 일일 평균 이벤트 수 | 예상 볼륨과 일치하는 범위 |
| 데이터 세트 | [3단계](#dataset)의 데이터 집합 |
| 개인 ID | **[!UICONTROL ID 맵]** |
| 기본 ID 네임스페이스 사용 | 선택됨 |
| 타임스탬프 | **[!UICONTROL 타임스탬프]** |
| 데이터 소스 유형 | **[!UICONTROL 기타]** |
| 데이터 소스 설명 | 모든 설명(예: `LLM Apps events`) |
| 새 데이터 가져오기 | **[!UICONTROL 새 데이터 모두 가져오기]** 켜짐 |
| 데이터 세트 채우기 | 데이터 집합에 분석하려는 이벤트가 이미 있는 경우 **[!UICONTROL 기존 데이터 모두 채우기]**&#x200B;이(가) 켜졌습니다. |

>[!IMPORTANT]
>
>**[!UICONTROL 새 데이터 모두 가져오기]**&#x200B;를 켭니다. 이 설정은 진행 중인 연결을 만들기 때문에 Workspace에서 자동으로 새 이벤트를 사용할 수 있게 됩니다.

이 개인 ID를 사용하여 [!DNL Customer Journey Analytics]은(는) 각 이벤트의 기본 ID를 사용자로 사용하고 기본 ID가 없는 이벤트는 건너뜁니다. [이벤트가 사람을 식별하는 방법](#identities)을 참조하세요.

프로시저는 [연결 만들기](https://experienceleague.adobe.com/ko/docs/analytics-platform/using/cja-connections/create-connection#create-connection), [이벤트 데이터 세트 설정](https://experienceleague.adobe.com/ko/docs/analytics-platform/using/cja-connections/create-connection#event-dataset-settings) 및 [ID 맵을 개인 ID로 사용](https://experienceleague.adobe.com/ko/docs/analytics-platform/using/cja-connections/create-connection#id-map)을 참조하십시오.

### 데이터 보기 만들기 {#data-view}

다음 설정으로 연결에 대한 데이터 보기를 만듭니다.

| 설정 | 값 |
|---|---|
| 연결 | 방금 만든 연결 |
| 이름 | 모든 이름(예: `LLM Apps`) |
| 구성 요소 | 보고할 차원 및 지표입니다. 아래 표를 참조하십시오. |

다음 스키마 필드를 구성 요소로 추가합니다.

| 다음에 대한 보고서 | 스키마 필드 | 다음으로 추가 |
|---|---|---|
| 작업 | `agenticExperience.tools.name` | 차원 |
| 실패한 작업 호출 | `agenticExperience.tools.failed` | 차원 |
| 실패 이유 | `agenticExperience.tools.errorReason` | 차원 |
| Platform | `agenticExperience.agents.name` | 차원 |
| 사용자 의도 | `conversation.prompt.raw.text` | 차원 |
| 대화 | `conversation.conversationID` | 차원 |
| 위젯 레이블 | `web.webInteraction.name` | 차원 |

사용자 의도와 같은 텍스트 필드를 차원으로 추가합니다. 지표로 추가된 텍스트 필드만 계산할 수 있으며 해당 값으로 분류하면 **잘못됨**&#x200B;이 표시됩니다.

프로시저는 [데이터 보기 구성](https://experienceleague.adobe.com/ko/docs/analytics-platform/using/cja-dataviews/create-dataview#configure) 및 [구성 요소](https://experienceleague.adobe.com/ko/docs/analytics-platform/using/cja-dataviews/create-dataview#components)를 참조하십시오.

## 8단계: 데이터 확인 및 프로덕션에 배포 {#verify}

**필수 항목:** [!DNL Adobe Analytics] 및 [!DNL Customer Journey Analytics].

지원되는 LLM 플랫폼에서 Stage 앱의 작업을 호출하고 해당 위젯에서 링크를 선택합니다. [Test in ChatGPT](/help/guides/test-in-chatgpt.md) 또는 [Test in Cloud](/help/guides/test-in-claude.md)를 참조하십시오. 그런 다음 설정한 각 서비스를 확인합니다.

| 서비스 | 확인할 위치 | 기대 사항 |
|---|---|---|
| [!DNL Experience Platform] | [3단계](#dataset)의 데이터 집합에 대한 **[!UICONTROL 데이터 집합 활동]** | 새로 수집된 일괄 처리 및 실패한 일괄 처리 없음 |
| [!DNL Adobe Analytics] | 자유 형식 테이블이 포함된 [단계 4](#report-suite)의 보고서 세트를 사용하는 Workspace 프로젝트 | 이름이 `mcp:<code identifier>`인 **[!UICONTROL 페이지]** 행, 위젯 상호 작용을 위한 **[!UICONTROL 사용자 지정 링크]** 행, **사용자 의도** 차원의 의도 텍스트 |
| [!DNL Customer Journey Analytics] | [7단계](#cja)의 데이터 보기를 사용하는 Workspace 프로젝트 | 액션 호출 및 위젯 인터랙션용 **[!UICONTROL 이벤트]** |

각 서비스는 데이터를 어느 정도 지연시켜 처리하므로 새로운 이벤트가 즉시 나타나지 않습니다.

보고서를 작성하려면 [Adobe Analytics에서 프로젝트 만들기](https://experienceleague.adobe.com/ko/docs/analytics/analyze/analysis-workspace/build-workspace-project/create-projects), [자유 형식 테이블 개요](https://experienceleague.adobe.com/ko/docs/analytics/analyze/analysis-workspace/visualizations/freeform-table/freeform-table) 및 [Customer Journey Analytics에서 프로젝트 만들기](https://experienceleague.adobe.com/ko/docs/analytics-platform/using/cja-workspace/build-workspace-project/create-projects)를 참조하십시오.

데이터가 올바르게 표시되면 [앱을 프로덕션에 배포](/help/guides/deploy-your-app.md)하세요.

## 제한 사항 {#limitations}

- **위젯 상호 작용이 여러 사용자로 분할됩니다.** 각 작업 호출은 새 ECID를 가져오므로 [!DNL Customer Journey Analytics]은(는) 다른 작업 호출의 위젯 상호 작용을 다른 사람으로 계산합니다. [이벤트가 사람을 식별하는 방법](#identities)을 참조하세요.
- **웹 사이트에 대한 링크가 없습니다.** 누군가가 웹 사이트에 대한 위젯 링크를 따라가면 웹 사이트 방문이 대화에 연결되지 않습니다.
- **스테이지 및 프로덕션 공유 보고서입니다.** 두 환경은 동일한 데이터스트림으로 전송하며 이벤트는 전송된 환경을 기록하지 않습니다.
- **일부 플랫폼에서 ID를 전송하지 않습니다.** [!DNL Customer Journey Analytics]에서 사용자 ID를 제공하지 않는 플랫폼(예: [!DNL Claude])의 작업 호출을 건너뜁니다.
- **작업 호출당 한 번 돌리기.** 각 작업 호출은 자체 대화 전환으로 기록되므로, 여러 작업을 트리거하는 프롬프트는 여러 전환으로 계산됩니다.

## 문제 해결 {#troubleshooting}

| 표시되는 항목 | 확인할 사항 |
|---|---|
| 데이터 세트가 일괄 처리를 받지 않음 | Analytics 설정을 테스트 중인 환경에 저장한 후 앱을 배포했습니다. 데이터 스트림 ID가 올바릅니다. 데이터 스트림에 데이터 집합이 포함된 **[!UICONTROL Adobe Experience Platform]** 서비스가 있습니다. |
| 배치 실패 | 스키마에는 [2단계](#schema)의 5개 필드 그룹이 모두 포함됩니다. |
| [!DNL Adobe Analytics]에 데이터가 없습니다. | 데이터 스트림에 올바른 보고서 세트 ID를 사용하여 **[!UICONTROL Adobe Analytics]** 서비스를 사용하도록 설정했습니다. |
| **페이지**&#x200B;에 `mcp:<code identifier>`이(가) 표시되지만 **사용자 의도** 및 작업 필드가 비어 있습니다. | 처리기 리포지토리는 `@adobe/llm-apps-runtime` 1.2.0 이상을 사용하며 업그레이드 후 배포했습니다. [시작하기 전에](#before-you-begin)를 참조하세요. |
| 일부 작업에 대해 **사용자 의도**&#x200B;가 비어 있습니다. | [4단계](#processing-rule)의 처리 규칙은 컨텍스트 데이터 변수를 소문자로 표시된 대로 사용하고 올바른 eVar을 사용합니다. 작업에 **[!UICONTROL 사용자 의도 수집]**&#x200B;이(가) 설정되어 있습니다. 규칙은 저장한 후 수집된 데이터에만 적용됩니다. |
| [!DNL Customer Journey Analytics]은(는) 데이터 집합보다 적은 이벤트를 표시합니다. | **[!UICONTROL 모든 새 데이터 가져오기]**&#x200B;가 켜져 있습니다. 예를 들어 사용자 ID를 제공하지 않는 플랫폼의 작업 호출과 같이 기본 ID가 없는 이벤트는 건너뜁니다. |
| 데이터 스트림 ID를 변경하거나 Analytics를 끌 수 없습니다 | 이러한 설정은 활성화 후 앱 설정에서 읽기 전용입니다. 도움이 필요하면 LLM Apps 팀에 문의하십시오. 데이터를 다른 곳에 라우팅하려면 데이터 스트림 자체에서 보고서 세트 또는 데이터 세트를 변경합니다. |

## 다음 단계 {#whats-next}

- [작업에 대한 분석 구성](/help/guides/create-action.md#configure-analytics)
- [앱 배포](/help/guides/deploy-your-app.md)
