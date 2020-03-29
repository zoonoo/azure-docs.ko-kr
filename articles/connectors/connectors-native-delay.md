---
title: 워크플로에서 다음 작업 지연
description: Azure 논리 앱의 지연 또는 지연 작업을 사용하여 논리 앱 워크플로에서 다음 작업을 실행할 때까지 기다립니다.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787339"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Azure 논리 앱에서 다음 작업 실행 지연

논리 앱이 다음 작업을 실행하기 전에 많은 시간을 기다리도록 하려면 로직 앱의 워크플로에서 작업 전에 기본 제공 **Delay - 예약** 작업을 추가할 수 있습니다. 또는 기본 제공 Delay를 다음 작업을 실행하기 전에 특정 날짜 및 시간까지 대기하도록 **작업을 예약할 때까지** 추가할 수 있습니다. 기본 제공 예약 작업 및 트리거에 대한 자세한 내용은 [Azure Logic Apps를 통해 반복되는 자동화된 작업 및 워크플로를 실행합니다.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

* **지연**: 다음 작업이 실행되기 전에 초, 분, 시간, 일, 주 또는 월과 같은 지정된 시간 단위 수를 기다립니다.

* **지연 시간 :** 다음 작업이 실행되기 전에 지정된 날짜와 시간까지 기다립니다.

다음은 이러한 작업을 사용하는 몇 가지 예입니다.

* 평일까지 기다렸다가 전자 메일로 상태 업데이트 전송

* 데이터를 다시 시작하고 검색하기 전에 HTTP 호출이 완료될 때까지 워크플로를 지연합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 무료 Azure [계정에 등록할](https://azure.microsoft.com/free/)수 있습니다.

* [논리 앱에](../logic-apps/logic-apps-overview.md)대한 기본 지식 . 작업을 사용하려면 먼저 논리 앱이 트리거로 시작해야 합니다. 지연 작업을 추가하기 전에 원하는 트리거를 사용하고 다른 작업을 추가할 수 있습니다. 이 항목에서는 Office 365 Outlook 트리거를 사용합니다. 논리 앱을 처음 접하는 경우 [첫 번째 논리 앱을 만드는 방법을](../logic-apps/quickstart-create-first-logic-app-workflow.md)알아봅니다.

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>지연 작업 추가

1. 논리 앱 디자이너에서 지연 작업을 추가하려는 단계에서 **새 단계를**선택합니다.

   단계 간에 지연 작업을 추가하려면 단계를 연결하는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호(+)를 선택한 다음 **작업 추가를**선택합니다.

1. 검색 상자에 필터로 "지연"을 입력합니다. 작업 목록에서 이 작업을 **선택합니다.**

   !["지연" 작업 추가](./media/connectors-native-delay/add-delay-action.png)

1. 다음 작업이 실행되기 전에 기다릴 시간을 지정합니다.

   ![지연 시간 설정](./media/connectors-native-delay/delay-time-intervals.png)

   | 속성 | JSON 이름 | 필수 | Type | Description |
   |----------|-----------|----------|------|-------------|
   | 개수 | count | yes | 정수 | 지연할 시간 단위 수 |
   | 단위 | 단위 | yes | String | 시간 단위(예: : `Second` `Minute`" `Hour` `Day`. `Week``Month` |
   ||||||

1. 워크플로에서 실행하려는 다른 작업을 추가합니다.

1. 완료되면 논리 앱을 저장합니다.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>지연-까지 작업 추가

1. 논리 앱 디자이너에서 지연 작업을 추가하려는 단계에서 **새 단계를**선택합니다.

   단계 간에 지연 작업을 추가하려면 단계를 연결하는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호(+)를 선택한 다음 **작업 추가를**선택합니다.

1. 검색 상자에 필터로 "지연"을 입력합니다. 작업 목록에서 다음 작업을 **선택합니다.**

   !["다음 기간까지 지연" 작업 추가](./media/connectors-native-delay/add-delay-until-action.png)

1. 워크플로를 다시 시작하려는 경우종료날짜와 시간을 제공합니다.

   ![지연 종료 시기에 대한 타임스탬프 지정](./media/connectors-native-delay/delay-until-timestamp.png)

   | 속성 | JSON 이름 | 필수 | Type | Description |
   |----------|-----------|----------|------|-------------|
   | 타임스탬프 | timestamp | yes | String | 이 형식을 사용하여 워크플로를 다시 시작하기 위한 종료 날짜 및 시간입니다. <p>YYYY-MM-DDThh:mm:ssZ <p>예를 들어 2017년 9월 18일 오후 2:00에 원하는 경우 "2017-09-18T14:00:00Z"를 지정합니다. <p>**참고:** 이 시간 형식은 [UTC 날짜](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)시간 형식의 [ISO 8601 날짜 시간 사양을](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) 따라야 하지만 [UTC 오프셋은](https://en.wikipedia.org/wiki/UTC_offset)없습니다. 표준 시간대가 없으면 공백 없이 끝에 문자 "Z"를 추가해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. |
   ||||||

1. 워크플로에서 실행하려는 다른 작업을 추가합니다.

1. 완료되면 논리 앱을 저장합니다.

## <a name="next-steps"></a>다음 단계

* [되풀이 트리거를 사용하여 되풀이 작업 및 워크플로를 생성, 예약 및 실행](../connectors/connectors-native-recurrence.md)
* [Logic Apps용 커넥터](../connectors/apis-list.md)