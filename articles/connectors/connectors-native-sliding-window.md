---
title: 연속 데이터를 처리하도록 작업 예약
description: Azure Logic Apps에서 슬라이딩 윈도우를 사용하여 연속 데이터를 처리하는 되풀이 작업을 만들고 실행합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 103805fbf395dc120acc96fbcee273abcf14939d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010421"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Azure Logic Apps에서 슬라이딩 윈도우 트리거를 사용하여 연속 데이터에 대한 작업을 예약하고 실행합니다.

작업, 프로세스, 데이터를 연속 청크로 처리해야 하는 작업을 정기적으로 실행하려면 **슬라이딩 윈도우** 트리거를 사용하여 논리 앱 워크플로를 시작할 수 있습니다. 워크플로를 시작하려는 날짜 및 시간(표준 시간대 포함)과 해당 워크플로를 반복하기 위한 되풀이를 설정할 수 있습니다. 예를 들어 중단 또는 사용할 수 없는 워크플로 등 어떤 이유로든 되풀이가 누락된 경우 이 트리거는 누락된 되풀이를 처리합니다. 예를 들어 데이터베이스와 백업 스토리지 간에 데이터를 동기화하는 경우 간격 발생 없이 데이터가 동기화되도록 슬라이딩 윈도우 트리거를 사용합니다. 기본 제공 일정 트리거 및 작업에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 반복되는 자동화, 작업 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참고하세요.

이 트리거가 지원하는 몇 가지 패턴은 다음과 같습니다.

* 즉시 실행하고 *n* 초, 분, 시간, 일, 주 또는 월마다 반복합니다.

* 특정 날짜 및 시간에 시작한 다음 *n* 초, 분, 시간, 일, 주 또는 월마다 반복합니다. 이 트리거를 사용하면 과거의 모든 되풀이를 실행하는 시작 시각을 지정할 수 있습니다.

* 실행하기 전에 각 되풀이를 특정 기간 지연합니다.

이 트리거와 되풀이 트리거 간의 차이점 또는 되풀이 워크플로를 예약하는 방법에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 반복되는 자동화 작업, 프로세스, 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참고하세요.

> [!TIP]
> 논리 앱을 트리거하고 나중에 한 번만 실행하려면 [한 번만 작업 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)을 참고하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/)할 수 있습니다.

* [논리 앱](../logic-apps/logic-apps-overview.md)에 대한 기본 지식 논리 앱을 처음 사용하는 경우 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 알아보세요.

## <a name="add-sliding-window-trigger"></a>슬라이딩 윈도우 트리거 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 빈 논리 앱을 만듭니다.

1. 논리 앱 디자이너가 표시되면 검색 상자에 `sliding window`를 필터로 입력합니다. 트리거 목록에서 **슬라이딩 윈도우** 트리거를 논리 앱 워크플로의 첫 번째 단계로 선택합니다.

   ![“슬라이딩 윈도우” 트리거 선택](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 매주 워크플로를 실행하도록 이러한 속성을 설정합니다.

   ![간격 및 빈도 설정](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 속성 | JSON 이름 | 필수 | Type | 설명 |
   |----------|----------|-----------|------|-------------|
   | **간격** | `interval` | 예 | 정수 | 빈도에 따라 워크플로가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. 다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 <br>- 주: 1~71주 <br>- 일: 1-500일 <br>- 시간: 1-12,000시간 <br>- 분: 1-72,000분 <br>- 초: 1-9,999,999초 <p>예를 들어 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. |
   | **빈도** | `frequency` | 예 | String | 되풀이 시간 단위: **초**, **분**, **시간**, **일**, **주** 또는 **월** |
   ||||||

   ![고급 되풀이 옵션](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   더 많은 되풀이 옵션을 보려면 **새 매개 변수 추가** 목록을 엽니다. 선택하는 옵션은 선택 후 트리거에 표시됩니다.

   | 속성 | 필수 | JSON 이름 | 유형 | 설명 |
   |----------|----------|-----------|------|-------------|
   | **지연** | 예 | delay | String | [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Durations)을 사용하여 각 되풀이를 지연하는 기간 |
   | **표준 시간대** | 예 | timeZone | String | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. |
   | **시작 시간** | 예 | startTime | String | 다음 형식의 시작 날짜 및 시간 제공: <p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss <p>또는 <p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ <p>예를 들어 2017년 9월 18일, 오후 2시를 원할 경우 “2017-09-18T14:00:00”을 지정하고 태평양 표준시와 같은 표준 시간대를 지정합니다. 또는 표준 시간대 없이 “2017-09-18T14:00:00Z”를 지정합니다. <p>**참고:** 이 시작 시간은 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset) 없이 [UTC 날짜 시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)의 [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 따라야 합니다. 표준 시간대를 선택하지 않으면 공백 없이 맨 끝에 문자 “Z”를 추가해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>간단한 일정의 경우 시작 시간은 처음 발생 시점인 한편, 고급 되풀이의 경우 시작 시간보다 더 빨리 트리거가 실행되지 않습니다. [*시작 날짜 및 시간을 사용할 수 있는 방법에는 무엇이 있나요?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 이제 다른 작업을 사용하여 나머지 워크플로를 빌드합니다. 추가할 수 있는 다른 작업은 [Azure Logic Apps 커넥터](../connectors/apis-list.md)를 참고하세요.

## <a name="workflow-definition---sliding-window"></a>워크플로 정의 - 슬라이딩 윈도우

JSON을 사용하는 논리 앱의 기본 워크플로 정의에서 선택한 옵션을 사용하여 슬라이딩 윈도우 트리거 정의를 볼 수 있습니다. 이 정의를 보려면 디자이너 도구 모음에서 **코드 보기** 를 선택합니다. 디자이너로 돌아가려면 디자이너 도구 모음에서 **디자이너** 를 선택합니다.

이 예제에서는 슬라이딩 윈도우 트리거 정의가 각 되풀이별 지연이 시간별 되풀이에 대해 5초인 기본 워크플로 정의에서 어떻게 표시되는지 보여 줍니다.

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

* [워크플로의 다음 작업 연기](../connectors/connectors-native-delay.md)
* [Logic Apps용 커넥터](../connectors/apis-list.md)
