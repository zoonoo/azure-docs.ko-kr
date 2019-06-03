---
title: 슬라이딩 윈도우 트리거-Azure Logic Apps를 사용 하 여 되풀이 작업 예약
description: 예약 하 고 Azure Logic Apps에서 슬라이딩 윈도우 트리거를 사용 하 여 되풀이 자동화 된 작업 및 워크플로 실행 합니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299504"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>만들기, 예약 하며, Azure Logic Apps에서 슬라이딩 윈도우 트리거를 사용 하 여 되풀이 작업 및 워크플로 실행 합니다.

작업, 프로세스 또는 연속 청크로 데이터를 처리 해야 하는 작업을 정기적으로 실행 하려면 사용 하 여 논리 앱 워크플로 시작할 수 있습니다 합니다 **슬라이딩 윈도우-일정** 트리거. 워크플로 시작 하는 해당 워크플로 반복 하는 것에 대 한 되풀이 대 한 표준 시간대 뿐만 아니라 날짜 및 시간을 설정할 수 있습니다. 되풀이 어떤 이유로 든 누락 된 경우이 트리거는 이러한 누락 된 되풀이 처리 합니다. 예를 들어, 데이터베이스 및 백업 저장소 간에 데이터를 동기화 할 때 트리거 사용 하 여 슬라이딩 윈도우 데이터 가져옵니다 간격 없이 동기화 되도록 합니다. 기본 제공 일정 트리거 및 작업에 대 한 자세한 내용은 참조 하세요. [일정 및 되풀이 자동화 된 실행, 작업 및 Azure Logic Apps를 사용 하 여 워크플로](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)합니다.

이 트리거를 지 원하는 몇 가지 패턴은 다음과 같습니다.

* 즉시 실행 하 고 반복 마다 *n* 초, 분 또는 시간 수입니다.

* 그런 다음 특정 날짜와 시간을 시작 하 고, 실행 하 고, 반복 마다 *n* 초, 분 또는 시간 수입니다. 이 트리거를 사용 하 여 모든 되풀이 과거를 실행 하는 이전에 시작 시간을 지정할 수 있습니다.

* 실행 하기 전에 특정 기간 동안 각 되풀이 지연 합니다.

이 트리거 및 되풀이 트리거 간의 차이점에 대 한 되풀이 워크플로 예약에 대 한 자세한 내용은 참조 하십시오 [일정 및 되풀이 실행 작업, 프로세스 및 Azure Logic Apps를 사용 하 여 워크플로 자동화](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)합니다.

> [!TIP]
> 논리 앱 트리거 및 미래 참조에서 한 번만 실행 하려는 경우 [실행 작업을 한 번만](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독자가 없는 경우 [무료 Azure 계정에 대 한 등록](https://azure.microsoft.com/free/)합니다.

* 에 대 한 기본 지식이 [논리 앱](../logic-apps/logic-apps-overview.md)합니다. 논리 앱을 처음 접하는 경우에 대해 알아봅니다 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)합니다.

## <a name="add-sliding-window-trigger"></a>슬라이딩 윈도우 트리거 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 빈 논리 앱을 만듭니다.

1. 검색 상자에서 논리 앱 디자이너에서 표시 되 면 "슬라이딩 윈도우"를 필터로 입력 합니다. 트리거 목록에서 논리 앱 워크플로에서 첫 번째 단계로이 트리거를 선택 합니다. **슬라이딩 윈도우**

   !["슬라이딩 윈도우" 트리거 선택](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 매주 워크플로를 실행하도록 이러한 속성을 설정합니다.

   ![간격 및 빈도 설정](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 자산 | 필수 | JSON 이름 | Type | 설명 |
   |----------|----------|-----------|------|-------------|
   | **간격** | 예 | interval | 정수  | 빈도에 따라 워크플로가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. 다음은 최소 및 최대 간격입니다. <p>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이 고는 frequency가 "Hour", 다음 되풀이 간격은 6 시간 마다. |
   | **Frequency(빈도)** | 예 | frequency | String | 되풀이에 대한 시간 단위: **두 번째**하십시오 **분**, 또는 **시간** |
   ||||||

   ![고급 되풀이 옵션](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   되풀이 옵션을 보려면 엽니다는 **새 매개 변수 추가** 목록입니다. 
   원하는 옵션을 선택 하면 선택한 후 트리거에 표시 됩니다.

   | 자산 | 필수 | JSON 이름 | Type | 설명 |
   |----------|----------|-----------|------|-------------|
   | **지연** | 아닙니다. | delay | 문자열 | 기간을 사용 하 여 각 되풀이 지연 된 [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **표준 시간대** | 아닙니다. | timeZone | String | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. |
   | **시작 시간** | 아닙니다. | startTime | String | 시작 날짜 및 시간 형식으로 제공 합니다. <p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss <p>또는 <p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ <p>예를 들어 2017 년 9 월 18 일 오후 2 시에 원하는 경우 지정 "2017-09-18T14:00:00" 태평양 표준시와 같은 표준 시간대를 선택 합니다. 또는 표준 시간대 없이 “2017-09-18T14:00:00Z”를 지정합니다. <p>**참고:** 이 시작 시간은 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset) 없이 [UTC 날짜 시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)의 [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 따라야 합니다. 표준 시간대를 선택하지 않으면 공백 없이 맨 끝에 문자 “Z”를 추가해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>간단한 일정의 경우 시작 시간은 첫 번째 발생 하는 동안에 대 한 고급 되풀이 트리거 시작 시간 보다 더 일찍 발생 하지 않습니다. [*시작 날짜 및 시간을 사용할 수 있는 방법에는 무엇이 있나요?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 이제 다른 작업을 사용 하 여 나머지 워크플로 작성 합니다. 추가할 수 있는 기타 작업을 참조 하세요 [Azure Logic Apps 용 커넥터](../connectors/apis-list.md)합니다.

## <a name="workflow-definition---sliding-window"></a>워크플로 정의-슬라이딩 윈도우

JSON을 사용 하는 논리 앱의 기본 워크플로 정의 선택한 옵션을 사용 하 여 슬라이딩 윈도우 트리거 정의 볼 수 있습니다. 디자이너 도구 모음에서이 정의 보려면 **코드 보기**합니다. 디자이너 도구 모음에서 선택 하 여 디자이너로 돌아갑니다 **디자이너**합니다.

이 예제에서는 어떻게 슬라이딩 윈도우 트리거 정의에서 찾을 수 있습니다 기본 워크플로 정의 각 되풀이 대 한 지연 되는 매시간 되풀이 대 한 5 초를 보여 줍니다.

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

* [워크플로에서 다음 작업을 지연](../connectors/connectors-native-delay.md)
* [Logic Apps 용 커넥터](../connectors/apis-list.md)