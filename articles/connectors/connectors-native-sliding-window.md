---
title: 연속된 데이터를 처리하도록 작업 예약
description: Azure Logic Apps에서 슬라이딩 창을 사용하여 연속 데이터를 처리하는 되풀이 태스크 만들기 및 실행
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786914"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Azure 논리 앱에서 슬라이딩 윈도우 트리거를 사용하여 연속 데이터에 대한 작업을 예약하고 실행합니다.

연속된 청크에서 데이터를 처리해야 하는 작업, 프로세스 또는 작업을 정기적으로 실행하려면 **슬라이딩 윈도우** 트리거를 사용하여 논리 앱 워크플로를 시작할 수 있습니다. 날짜와 시간, 워크플로시작을 위한 표준 시간대, 해당 워크플로를 반복하기 위한 되풀이를 설정할 수 있습니다. 어떤 이유로든 재발을 놓친 경우 이 트리거는 누락된 재발을 처리합니다. 예를 들어 데이터베이스와 백업 저장소 간에 데이터를 동기화할 때 간격을 두지 않고 데이터가 동기화되도록 슬라이딩 윈도우 트리거를 사용합니다. 기본 제공 Schedule 트리거 및 작업에 대한 자세한 내용은 [Azure Logic Apps를 통해 반복되는 자동화된 작업 및 워크플로를 실행합니다.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

이 트리거가 지원하는 몇 가지 패턴은 다음과 같습니다.

* 즉시 실행하고 초, 분 또는 시간의 *모든 n* 수를 반복합니다.

* 특정 날짜와 시간에서 시작한 다음 초, 분 또는 시간의 *모든 n개* 수를 실행하고 반복합니다. 이 트리거를 사용하면 과거의 모든 되풀이를 실행하는 시작 시간을 지정할 수 있습니다.

* 실행하기 전에 특정 기간 동안 각 되풀이를 지연합니다.

이 트리거와 되풀이 트리거 간의 차이점 또는 되풀이 워크플로 예약에 대한 자세한 내용은 [Azure Logic Apps를 통해 되풀이 되는 자동화된 작업, 프로세스 및 워크플로를 예약 및 실행합니다.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

> [!TIP]
> 논리 앱을 트리거하고 나중에 한 번만 실행하려면 작업 [실행을 한 번만](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 무료 Azure [계정에 등록할](https://azure.microsoft.com/free/)수 있습니다.

* [논리 앱에](../logic-apps/logic-apps-overview.md)대한 기본 지식 . 논리 앱을 처음 접하는 경우 [첫 번째 논리 앱을 만드는 방법을](../logic-apps/quickstart-create-first-logic-app-workflow.md)알아봅니다.

## <a name="add-sliding-window-trigger"></a>슬라이딩 윈도우 트리거 추가

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 빈 논리 앱을 만듭니다.

1. 논리 앱 디자이너가 나타나면 검색 상자에 필터로 "슬라이딩 창"을 입력합니다. 트리거 목록에서 논리 앱 워크플로의 첫 번째 단계로 이 **Sliding Window** 트리거를 선택합니다.

   !["슬라이딩 윈도우" 트리거 선택](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 매주 워크플로를 실행하도록 이러한 속성을 설정합니다.

   ![간격 및 빈도 설정](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 속성 | 필수 | JSON 이름 | Type | Description |
   |----------|----------|-----------|------|-------------|
   | **간격** | yes | interval | 정수 | 빈도에 따라 워크플로가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. 다음은 최소 및 최대 간격입니다. <p>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이고 빈도가 "시간"인 경우 되풀이는 6시간마다 입니다. |
   | **주파수** | yes | frequency | String | 되풀이 시간 단위: **두 번째,** **분**또는 **시간** |
   ||||||

   ![고급 되풀이 옵션](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   자세한 되풀이 옵션을 보려면 **새 매개 변수 추가** 목록을 엽니다. 
   선택한 모든 옵션은 선택 후 트리거에 나타납니다.

   | 속성 | 필수 | JSON 이름 | Type | Description |
   |----------|----------|-----------|------|-------------|
   | **지연** | 예 | delay | String | [ISO 8601 날짜 시간 사양을](https://en.wikipedia.org/wiki/ISO_8601#Durations) 사용하여 각 재발을 지연하는 기간 |
   | **시간대** | 예 | timeZone | String | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. |
   | **시작 시간** | 예 | startTime | String | 이 형식으로 시작 날짜 및 시간을 제공합니다. <p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss <p>또는 <p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ <p>예를 들어 2017년 9월 18일 오후 2:00에 원하는 경우 "2017-09-18T14:00:00"을 지정하고 태평양 표준시와 같은 표준 시간대를 선택합니다. 또는 표준 시간대 없이 “2017-09-18T14:00:00Z”를 지정합니다. <p>**참고:** 이 시작 시간은 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset) 없이 [UTC 날짜 시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)의 [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 따라야 합니다. 표준 시간대를 선택하지 않으면 공백 없이 맨 끝에 문자 “Z”를 추가해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>간단한 일정의 경우 시작 시간이 첫 번째 발생인 반면 고급 되풀이의 경우 트리거가 시작 시간보다 더 빨리 트리거되지 않습니다. [*시작 날짜 및 시간을 사용할 수 있는 방법에는 무엇이 있나요?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 이제 다른 작업으로 나머지 워크플로를 빌드합니다. 추가할 수 있는 추가 작업에 대 한 [참조 Azure 논리 앱에 대 한 커넥터입니다.](../connectors/apis-list.md)

## <a name="workflow-definition---sliding-window"></a>워크플로 정의 - 슬라이딩 윈도우

JSON을 사용하는 논리 앱의 기본 워크플로 정의에서 선택한 옵션으로 슬라이딩 윈도우 트리거 정의를 볼 수 있습니다. 이 정의를 보려면 디자이너 도구 모음에서 **코드 보기를 선택합니다.** 디자이너로 돌아가려면 디자이너 도구 모음 **디자이너**디자이너 를 선택합니다.

이 예제에서는 각 되풀이에 대한 지연이 시간별 되풀이에 대해 5초인 기본 워크플로 정의에서 슬라이딩 윈도우 트리거 정의가 어떻게 표시되는지 보여 주며, 이 경우 다음과 같은 방식으로 표시됩니다.

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>다음 단계

* [워크플로에서 다음 작업 지연](../connectors/connectors-native-delay.md)
* [Logic Apps용 커넥터](../connectors/apis-list.md)