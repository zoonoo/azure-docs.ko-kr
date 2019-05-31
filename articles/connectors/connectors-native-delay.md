---
title: 워크플로-Azure Logic Apps에서 다음 작업을 지연
description: Azure Logic Apps에서 지연 또는 지연 될 때까지 작업을 사용 하 여 논리 앱 워크플로에서 다음 작업을 실행할 때까지 대기합니다
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297665"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Azure Logic Apps에서 다음 작업을 실행 하는 지연

다음 작업을 실행 하기 전에 시간을 대기 하 여 논리 앱에 추가할 수 있습니다 기본 제공 **지연-예약** 논리 앱 워크플로에서 작업 하기 전에 동작 합니다. 또는 기본 제공을 추가할 수 있습니다 **될 때까지 지연-예약** 다음 작업을 실행 하기 전에 시간과 특정 날짜까지 대기 하는 작업입니다. 기본 제공 일정 작업 및 트리거에 대 한 자세한 내용은 참조 하세요. [일정 및 되풀이 자동화 된 실행, 작업 및 Azure Logic Apps를 사용 하 여 워크플로](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)합니다.

* **지연**: 지정 된 초, 분, 시간, 일, 주 또는 월, 다음 작업이 실행 되기 전에 등의 시간 단위 수까지 기다립니다.

* **다음 기간까지 지연**: 지정 된 날짜 및 다음 작업 실행 되기 전에 시간까지 기다립니다.

이러한 동작을 사용 하는 몇 가지 예제 방법을 다음과 같습니다.

* 평일까지 기다렸다가 전자 메일로 상태 업데이트 전송

* 다시 시작 하 고 데이터를 검색 하기 전에 HTTP 호출이 완료 될 때까지 워크플로 지연 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독자가 없는 경우 [무료 Azure 계정에 대 한 등록](https://azure.microsoft.com/free/)합니다.

* 에 대 한 기본 지식이 [논리 앱](../logic-apps/logic-apps-overview.md)합니다. 작업을 사용 하려면 먼저 논리 앱 트리거를 사용 하 여 먼저 시작 해야 합니다. 모든 트리거를 원하며 지연 동작을 추가 하기 전에 다른 작업 추가 사용할 수 있습니다. 이 항목에서는 Office 365 Outlook 트리거를 사용 합니다. 논리 앱을 처음 접하는 경우에 대해 알아봅니다 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)합니다.

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>지연 작업 추가

1. 지연 작업을 추가 하려는 단계 아래에서 논리 앱 디자이너에서 선택 **새 단계**합니다.

   지연 작업 단계 사이 추가 하려면 단계를 연결 하는 화살표 위로 포인터를 이동 합니다. 그런 다음 선택한 표시 되는 더하기 기호 (+)를 선택 **작업 추가**합니다.

1. 검색 상자에서 필터로 "지연"를 입력 합니다. 작업 목록에서 다음 작업을 선택합니다. **지연**

   !["지연" 작업 추가](./media/connectors-native-delay/add-delay-action.png)

1. 다음 작업 실행 되기 전에 대기할 시간을 지정 합니다.

   ![시간 지연에 대 한 설정](./media/connectors-native-delay/delay-time-intervals.png)

   | 자산 | JSON 이름 | 필수 | 형식 | 설명 |
   |----------|-----------|----------|------|-------------|
   | 카운트 | count | 예 | 정수  | 지연할 시간 단위 수 |
   | 단위 | 단위 | 예 | String | 예를 들어 시간 단위: `Second`, `Minute`, `Hour`합니다 `Day`, `Week`, 또는 `Month` |
   ||||||

1. 워크플로에서 실행 하려는 다른 작업을 추가 합니다.

1. 완료되면 논리 앱을 저장합니다.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>지연 추가-until 작업

1. 지연 작업을 추가 하려는 단계 아래에서 논리 앱 디자이너에서 선택 **새 단계**합니다.

   지연 작업 단계 사이 추가 하려면 단계를 연결 하는 화살표 위로 포인터를 이동 합니다. 그런 다음 선택한 표시 되는 더하기 기호 (+)를 선택 **작업 추가**합니다.

1. 검색 상자에서 필터로 "지연"를 입력 합니다. 작업 목록에서 다음 작업을 선택합니다. **다음 기간까지 지연**

   !["다음 기간까지 지연" 작업 추가](./media/connectors-native-delay/add-delay-until-action.png)

1. 워크플로 다시 시작 하려는 경우에 대 한 종료 날짜 및 시간을 제공 합니다.

   ![지연 시간을 종료 하는 경우에 대 한 타임 스탬프를 지정 합니다.](./media/connectors-native-delay/delay-until-timestamp.png)

   | 자산 | JSON 이름 | 필수 | 형식 | 설명 |
   |----------|-----------|----------|------|-------------|
   | 타임 스탬프 | timestamp | 예 | String | 종료 날짜 및이 형식을 사용 하 여 워크플로 다시 시작 시간: <p>YYYY-MM-DDThh:mm:ssZ <p>2017 년 9 월 18 일 오후 2 시에 원하는 경우 지정 하는 예를 들어 "2017-09-18T14:00:00Z"입니다. <p>**참고:** 이 시간 형식을 따라야 합니다 [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) 에 [UTC 날짜 / 시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), 없이 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)합니다. 표준 시간대 없이 공백 없이 끝 문자 "Z"를 추가 해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. |
   ||||||

1. 워크플로에서 실행 하려는 다른 작업을 추가 합니다.

1. 완료되면 논리 앱을 저장합니다.

## <a name="next-steps"></a>다음 단계

* [만들기, 예약 하며, 되풀이 트리거를 사용 하 여 되풀이 작업 및 워크플로 실행 합니다.](../connectors/connectors-native-recurrence.md)
* [Logic Apps 용 커넥터](../connectors/apis-list.md)