---
title: 되풀이 트리거-Azure Logic Apps를 사용 하 여 되풀이 작업 예약
description: 예약 하 고 Azure Logic Apps에서 되풀이 트리거를 사용 하 여 되풀이 자동화 된 작업 및 워크플로 실행 합니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297518"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>만들기, 예약 하며, Azure Logic Apps에서 되풀이 트리거를 사용 하 여 되풀이 작업 및 워크플로 실행 합니다.

특정 일정에 따라 작업, 프로세스 또는 작업을 정기적으로 실행 하려면 기본 제공 논리 앱 워크플로 시작할 수 있습니다 **일정-되풀이** 트리거. 워크플로 시작 하는 해당 워크플로 반복 하는 것에 대 한 되풀이 대 한 표준 시간대 뿐만 아니라 날짜 및 시간을 설정할 수 있습니다. 되풀이 어떤 이유로 든 누락 된 경우이 트리거는 다음 예약 된 간격으로 되풀이 계속 합니다. 기본 제공 일정 트리거 및 작업에 대 한 자세한 내용은 참조 하세요. [일정 및 되풀이 자동화 된 실행, 작업 및 Azure Logic Apps를 사용 하 여 워크플로](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)합니다.

이 트리거는 복잡 한 일정 및 고급 되풀이 함께 지 원하는 몇 가지 패턴은 다음과 같습니다.

* 즉시 실행 하 고 반복 마다 *n* 초, 분, 시간, 일, 주 또는 월 수입니다.

* 그런 다음 특정 날짜와 시간을 시작 하 고, 실행 하 고, 반복 마다 *n* 초, 분, 시간, 일, 주 또는 월 수입니다.

* 예를 들어 8:00 AM 및 5:00 PM과 같이 매일 하나 이상의 시간에 실행하고 반복합니다.

* 매주 실행하고 반복하되, 토요일 및 일요일과 같이 특정 요일에만 적용합니다.

* 매주 실행하고 반복하되, 월요일부터 금요일까지 8:00 AM 및 5:00 PM과 같이 특정 날짜 및 시간에만 적용합니다.

이 트리거 및 슬라이딩 윈도우 트리거 간의 차이점에 대 한 되풀이 워크플로 예약에 대 한 자세한 내용은 참조 하십시오 [일정 및 되풀이 실행 작업, 프로세스 및 Azure Logic Apps를 사용 하 여 워크플로 자동화](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)합니다.

> [!TIP]
> 논리 앱 트리거 및 미래 참조에서 한 번만 실행 하려는 경우 [실행 작업을 한 번만](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독자가 없는 경우 [무료 Azure 계정에 대 한 등록](https://azure.microsoft.com/free/)합니다.

* 에 대 한 기본 지식이 [논리 앱](../logic-apps/logic-apps-overview.md)합니다. 논리 앱을 처음 접하는 경우에 대해 알아봅니다 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)합니다.

## <a name="add-recurrence-trigger"></a>되풀이 트리거 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 빈 논리 앱을 만듭니다.

1. 검색 상자에서 논리 앱 디자이너에서 표시 되 면 필터로 "되풀이"를 입력 합니다. 트리거 목록에서 논리 앱 워크플로에서 첫 번째 단계로이 트리거를 선택 합니다. **되풀이**

   !["되풀이" 트리거 선택](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 매주 워크플로를 실행하도록 이러한 속성을 설정합니다.

   ![간격 및 빈도 설정](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | 자산 | 필수 | JSON 이름 | Type | 설명 |
   |----------|----------|-----------|------|-------------|
   | **간격** | 예 | interval | 정수  | 빈도에 따라 워크플로가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. 다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. |
   | **Frequency(빈도)** | 예 | frequency | String | 되풀이에 대한 시간 단위: **초**, **분**, **시간**, **일**, **주** 또는 **월** |
   ||||||

   더 많은 일정 옵션에 대 한 열을 **새 매개 변수 추가** 목록입니다. 
   원하는 옵션을 선택 하면 선택한 후 트리거에 표시 됩니다.

   ![고급 일정 옵션](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | 자산 | 필수 | JSON 이름 | Type | 설명 |
   |----------|----------|-----------|------|-------------|
   | **표준 시간대** | 아닙니다. | timeZone | String | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. |
   | **시작 시간** | 아닙니다. | startTime | 문자열 | 시작 날짜 및 시간 형식으로 제공 합니다. <p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss <p>또는 <p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ <p>예를 들어 2017 년 9 월 18 일 오후 2 시에 원하는 경우 지정 "2017-09-18T14:00:00" 태평양 표준시와 같은 표준 시간대를 선택 합니다. 또는 표준 시간대 없이 “2017-09-18T14:00:00Z”를 지정합니다. <p>**참고:** 이 시작 시간은 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset) 없이 [UTC 날짜 시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)의 [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 따라야 합니다. 표준 시간대를 선택하지 않으면 공백 없이 맨 끝에 문자 “Z”를 추가해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>단순 일정의 경우 시작 시간은 첫 번째 발생이지만 복잡한 일정의 경우 트리거는 시작 시간보다 더 일찍 발생하지 않습니다. [*시작 날짜 및 시간을 사용할 수 있는 방법에는 무엇이 있나요?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **요일 선택** | 아닙니다. | weekDays | 문자열 또는 문자열 배열 | "주"를 선택하는 경우 워크플로 실행하려고 할 때 하나 이상의 요일을 선택할 수 있습니다. **Monday**, **Tuesday**, **Wednesday**, **Thursday**, **Friday**, **Saturday** 및 **Sunday** |
   | **시간 선택** | 아닙니다. | hours | 정수 또는 정수 배열 | "Day" 또는 "Week"를 선택 하는 경우 워크플로 실행 하려는 경우 하루 중 시간으로 라이브러리 0에서 이상의 정수를 23을 선택할 수 있습니다. <p><p>예를 들어 "10", "12" 및 "14"를 지정 하면 10 AM, 12 PM 및 오후 2, 하루 중 시간을 표시 하지만 되풀이 시작 분을 기준으로 계산 됩니다. 날짜의 분을 설정 하려면 값을 지정 합니다 **분** 속성입니다. |
   | **분 선택** | 아닙니다. | minutes | 정수 또는 정수 배열 | “일” 또는 “주”를 선택하는 경우 워크플로를 실행하려는 시간의 분으로 0~59 사이의 정수를 하나 이상 선택할 수 있습니다. <p>예를 들어 분 표시로 “30”을 지정하고, 앞에 나온 하루 시간 예제를 사용하면 10:30 AM, 12:30 PM 및 2:30 PM이 표시됩니다. |
   |||||

   예를 들어 오늘이 2017년 9월 4일 월요일이라고 가정합니다. 다음 되풀이 트리거가 발생 하지 않는 *더 일찍* 시작 날짜 및 시간 보다은 2017 년 9 월 18 일, 월요일 오전 8시 PST. 반면 되풀이 일정이 월요일에 한해서만 10:30 AM, 12:30 PM 및 2:30 PM으로 설정되어 있습니다. 따라서 트리거가 발생하고 논리 앱 워크플로 인스턴스가 만들어지는 처음 시간은 10:30 AM이 됩니다. 시작 시간이 작동하는 방법에 대한 자세한 내용은 [시작 시간 예](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)를 참조하세요.

   이후 실행은 같은 날 12:30 PM 및 2:30 PM에 발생합니다. 되풀이마다 해당 워크플로 인스턴스가 만들어집니다. 그런 다음 전체 일정이 다시 다음 월요일에 반복됩니다. [*다른 예제에는 어떤 것이 있나요?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![고급 일정 예제](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > 트리거는 주기로 “일” 또는 “주”를 선택한 경우에만 지정된 되풀이에 대한 미리 보기를 보여 줍니다.

1. 이제 다른 작업을 사용 하 여 나머지 워크플로 작성 합니다. 추가할 수 있는 기타 작업을 참조 하세요 [Azure Logic Apps 용 커넥터](../connectors/apis-list.md)합니다.

## <a name="workflow-definition---recurrence"></a>워크플로 정의-되풀이

JSON을 사용 하는 논리 앱의 기본 워크플로 정의 볼 수 있습니다 합니다 [되풀이 트리거 정의](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) 선택한 옵션을 사용 하 여 합니다. 디자이너 도구 모음에서이 정의 보려면 **코드 보기**합니다. 디자이너 도구 모음에서 선택 하 여 디자이너로 돌아갑니다 **디자이너**합니다.

이 예제에서는 되풀이 트리거 정의 기본 워크플로 정의에 표시 될 수 있습니다 하는 방법을 보여 줍니다.

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

* [지연 작업 사용 워크플로 일시 중지](../connectors/connectors-native-delay.md)
* [Logic Apps 용 커넥터](../connectors/apis-list.md)
