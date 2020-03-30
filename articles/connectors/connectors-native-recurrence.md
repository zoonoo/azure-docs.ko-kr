---
title: 되풀이 작업 및 워크플로 예약
description: Azure Logic Apps에서 되풀이 트리거를 통해 반복되는 자동화된 작업 및 워크플로를 예약하고 실행합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445869"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Azure Logic Apps에서 되풀이 트리거를 사용하여 되풀이 작업 및 워크플로를 생성, 예약 및 실행합니다.

특정 일정에 따라 작업, 프로세스 또는 작업을 정기적으로 실행하려면 기본 제공 **되풀이 - 일정** 트리거를 사용하여 논리 앱 워크플로를 시작할 수 있습니다. 날짜와 시간, 워크플로시작을 위한 표준 시간대, 해당 워크플로를 반복하기 위한 되풀이를 설정할 수 있습니다. 어떤 이유로든 되풀이가 누락되면 이 트리거는 다음 예약된 간격으로 계속 되풀이됩니다. 기본 제공 Schedule 트리거 및 작업에 대한 자세한 내용은 [Azure Logic Apps를 통해 반복되는 자동화된 작업 및 워크플로를 실행합니다.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

다음은 이 트리거가 고급 되풀이 및 복잡한 일정과 함께 지원하는 몇 가지 패턴입니다.

* 즉시 실행하고 초, 분, 시간, 일, 주 또는 월의 *모든 n* 수를 반복합니다.

* 특정 날짜와 시간에서 시작한 다음 초, 분, 시간, 일, 주 또는 월의 *모든 n개* 수를 실행하고 반복합니다.

* 예를 들어 8:00 AM 및 5:00 PM과 같이 매일 하나 이상의 시간에 실행하고 반복합니다.

* 매주 실행하고 반복하되, 토요일 및 일요일과 같이 특정 요일에만 적용합니다.

* 매주 실행하고 반복하되, 월요일부터 금요일까지 8:00 AM 및 5:00 PM과 같이 특정 날짜 및 시간에만 적용합니다.

이 트리거와 슬라이딩 윈도우 트리거 간의 차이점 또는 되풀이 워크플로 예약에 대한 자세한 내용은 [Azure Logic Apps를 통해 되풀이 되는 자동화된 작업, 프로세스 및 워크플로를 예약 및 실행합니다.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

> [!TIP]
> 논리 앱을 트리거하고 나중에 한 번만 실행하려면 작업 [실행을 한 번만](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [논리 앱에](../logic-apps/logic-apps-overview.md)대한 기본 지식 . 논리 앱을 처음 접하는 경우 [첫 번째 논리 앱을 만드는 방법을](../logic-apps/quickstart-create-first-logic-app-workflow.md)알아봅니다.

## <a name="add-recurrence-trigger"></a>되풀이 트리거 추가

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 빈 논리 앱을 만듭니다.

1. 논리 앱 디자이너가 나타나면 검색 `recurrence` 상자에 필터로 입력합니다. 트리거 목록에서 논리 앱 워크플로의 첫 번째 단계로 이 트리거를 **선택합니다.**

   !["되풀이" 트리거 선택](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 매주 워크플로를 실행하도록 이러한 속성을 설정합니다.

   ![간격 및 빈도 설정](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | 속성 | JSON 이름 | 필수 | Type | Description |
   |----------|-----------|----------|------|-------------|
   | **간격** | `interval` | yes | 정수 | 빈도에 따라 워크플로가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. 다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. |
   | **주파수** | `frequency` | yes | String | 되풀이 시간 단위: **초**, **분**, **시간**, **일**, **주** 또는 **월** |
   ||||||

   > [!IMPORTANT]
   > 되풀이가 고급 일정 옵션을 지정하지 않으면 이후의 되풀이는 마지막 실행 시간을 기반으로 합니다.
   > 이러한 되풀이에 대 한 시작 시간은 저장소 호출 중 대기 시간 등의 요인으로 인해 표류 할 수 있습니다. 논리 앱이 되풀이를 놓치지 않도록 하려면, 특히 빈도가 며칠 이상인 경우 다음 옵션 중 하나를 사용하십시오.
   > 
   > * 되풀이에 대한 시작 시간을 제공합니다.
   > 
   > * **이러한** 시간 및 **이러한 분** 속성을 사용하여 되풀이를 실행할 시기에 대한 시간과 분을 지정합니다.
   > 
   > * 되풀이 [트리거대신 슬라이딩 창 트리거를](../connectors/connectors-native-sliding-window.md)사용합니다.

1. 고급 예약 옵션을 설정하려면 **새 매개 변수 추가** 목록을 엽니다. 선택한 모든 옵션은 선택 후 트리거에 나타납니다.

   ![고급 일정 옵션](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | 속성 | JSON 이름 | 필수 | Type | Description |
   |----------|-----------|----------|------|-------------|
   | **시간대** | `timeZone` | 예 | String | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. |
   | **시작 시간** | `startTime` | 예 | String | 이 형식으로 시작 날짜 및 시간을 제공합니다. <p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss <p>또는 <p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ <p>예를 들어 2017년 9월 18일 오후 2:00에 원하는 경우 "2017-09-18T14:00:00"을 지정하고 태평양 표준시와 같은 표준 시간대를 선택합니다. 또는 표준 시간대 없이 “2017-09-18T14:00:00Z”를 지정합니다. <p>**참고:** 이 시작 시간은 향후 최대 49년이 며 [UTC 날짜](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)시간 형식의 [ISO 8601 날짜 시간 사양을](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) 따라야 하지만 [UTC 오프셋은](https://en.wikipedia.org/wiki/UTC_offset)없습니다. 표준 시간대를 선택하지 않으면 공백 없이 맨 끝에 문자 “Z”를 추가해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>단순 일정의 경우 시작 시간은 첫 번째 발생이지만 복잡한 일정의 경우 트리거는 시작 시간보다 더 일찍 발생하지 않습니다. [*시작 날짜 및 시간을 사용할 수 있는 방법에는 무엇이 있나요?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **요일 선택** | `weekDays` | 예 | 문자열 또는 문자열 배열 | “주”를 선택하는 경우 워크플로를 실행하려는 요일을 하나 이상 선택할 수 있습니다.**월요일**, **화요일**, **수요일**, **목요일**, **금요일**, **토요일** 및 **일요일** |
   | **시간 선택** | `hours` | 예 | 정수 또는 정수 배열 | "일" 또는 "주"를 선택하면 워크플로를 실행할 때의 시간으로 0에서 23까지 하나 이상의 정수를 선택할 수 있습니다. <p><p>예를 들어 "10", "12" 및 "14"를 지정하면 하루 중 시간 동안 오전 10시, 오후 12시 및 오후 2시를 얻지만 되풀이가 시작되는 시간을 기준으로 하루의 분이 계산됩니다. 하루의 분(분)을 설정하려면 이러한 **분** 속성에 대한 값을 지정합니다. |
   | **분 선택** | `minutes` | 예 | 정수 또는 정수 배열 | “일” 또는 “주”를 선택하는 경우 워크플로를 실행하려는 시간의 분으로 0~59 사이의 정수를 하나 이상 선택할 수 있습니다. <p>예를 들어 분 표시로 “30”을 지정하고, 앞에 나온 하루 시간 예제를 사용하면 10:30 AM, 12:30 PM 및 2:30 PM이 표시됩니다. |
   |||||

   예를 들어 오늘이 2017년 9월 4일 월요일이라고 가정합니다. 다음 되풀이 트리거는 시작 날짜 및 시간 보다 *더 빨리* 발생 하지 않습니다., 월요일, 9 월 18, 2017 에 8:00 오전 PST. 반면 되풀이 일정이 월요일에 한해서만 10:30 AM, 12:30 PM 및 2:30 PM으로 설정되어 있습니다. 따라서 트리거가 발생하고 논리 앱 워크플로 인스턴스가 만들어지는 처음 시간은 10:30 AM이 됩니다. 시작 시간이 작동하는 방법에 대한 자세한 내용은 [시작 시간 예](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)를 참조하세요.

   이후 실행은 같은 날 12:30 PM 및 2:30 PM에 발생합니다. 되풀이마다 해당 워크플로 인스턴스가 만들어집니다. 그런 다음 전체 일정이 다시 다음 월요일에 반복됩니다. [*다른 예제에는 어떤 것이 있나요?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![고급 일정 예제](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > 트리거는 주기로 “일” 또는 “주”를 선택한 경우에만 지정된 되풀이에 대한 미리 보기를 보여 줍니다.

1. 이제 다른 작업으로 나머지 워크플로를 빌드합니다. 추가할 수 있는 추가 작업에 대 한 [참조 Azure 논리 앱에 대 한 커넥터입니다.](../connectors/apis-list.md)

## <a name="workflow-definition---recurrence"></a>워크플로 정의 - 되풀이

JSON을 사용하는 논리 앱의 기본 워크플로 정의에서 선택한 옵션으로 [되풀이 트리거 정의를](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) 볼 수 있습니다. 이 정의를 보려면 디자이너 도구 모음에서 **코드 보기를 선택합니다.** 디자이너로 돌아가려면 디자이너 도구 모음 **디자이너**디자이너 를 선택합니다.

이 예제에서는 되풀이 트리거 정의가 기본 워크플로 정의에서 어떻게 보이는지 보여 주며 있습니다.

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>다음 단계

* [지연 작업으로 워크플로 일시 중지](../connectors/connectors-native-delay.md)
* [Logic Apps용 커넥터](../connectors/apis-list.md)
