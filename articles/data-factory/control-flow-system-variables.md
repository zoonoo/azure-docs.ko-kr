---
title: Azure Data Factory의 시스템 변수
description: 이 문서에서는 Azure Data Factory에서 지원하는 시스템 변수에 대해 설명합니다. 데이터 팩터리 엔터티를 정의할 때 식에서 이러한 변수를 사용할 수 있습니다.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: a5d2043c29db87876cc0d5ddb5b3708abad033c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591982"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory에서 지원하는 시스템 변수

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 지원하는 시스템 변수에 대해 설명합니다. 데이터 팩터리 엔터티를 정의할 때 식에서 이러한 변수를 사용할 수 있습니다.

## <a name="pipeline-scope"></a>파이프라인 범위

이러한 시스템 변수는 파이프라인 JSON의 어디에서나 참조할 수 있습니다.

| 변수 이름 | 설명 |
| --- | --- |
| @pipeline().DataFactory |파이프라인 실행이 실행 되 고 있는 데이터 팩터리의 이름입니다. |
| @pipeline().Pipeline |파이프라인 이름입니다. |
| @pipeline().RunId |특정 파이프라인 실행의 ID |
| @pipeline().TriggerType |파이프라인을 호출한 트리거의 유형입니다 (예: `ScheduleTrigger` , `BlobEventsTrigger` ). 지원 되는 트리거 형식 목록은 [Azure Data Factory 파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조 하세요. 트리거 형식은 `Manual` 파이프라인이 수동으로 트리거 되었음을 나타냅니다. |
| @pipeline().TriggerId|파이프라인을 호출한 트리거의 ID입니다. |
| @pipeline().TriggerName|파이프라인을 호출한 트리거의 이름입니다. |
| @pipeline().TriggerTime|파이프라인을 호출 하는 트리거 실행의 시간입니다. 트리거는 파이프라인 실행을 호출 하기 위해 **실제로** 발생 하는 시간이 며 트리거의 예약 된 시간과 약간 다를 수 있습니다.  |

>[!NOTE]
>파이프라인 및 트리거 범위에서 트리거 관련 날짜/시간 시스템 변수는 ISO 8601 형식으로 UTC 날짜를 반환 합니다 (예:) `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>일정 트리거 범위

이러한 시스템 변수는 [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger)형식의 트리거에 대 한 트리거 JSON의 어디에서 나 참조할 수 있습니다.

| 변수 이름 | 설명 |
| --- | --- |
| @trigger().scheduledTime |트리거가 파이프라인 실행을 호출 하도록 예약 된 시간입니다. |
| @trigger().startTime |파이프라인 실행을 호출 하기 위해 트리거가 **실제로** 발생 하는 시간입니다. 이는 트리거의 예약 된 시간과 약간 다를 수 있습니다. |

## <a name="tumbling-window-trigger-scope"></a>연속 창 트리거 범위

이러한 시스템 변수는 [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger)형식의 트리거에 대 한 트리거 JSON의 어디에서 나 참조할 수 있습니다.

| 변수 이름 | 설명 |
| --- | --- |
| @trigger().outputs.windowStartTime |트리거 실행에 연결 된 창의 시작입니다. |
| @trigger().outputs.windowEndTime |트리거와 함께 실행 된 창의 끝입니다. |
| @trigger().scheduledTime |트리거가 파이프라인 실행을 호출 하도록 예약 된 시간입니다. |
| @trigger().startTime |파이프라인 실행을 호출 하기 위해 트리거가 **실제로** 발생 하는 시간입니다. 이는 트리거의 예약 된 시간과 약간 다를 수 있습니다. |

## <a name="storage-event-trigger-scope"></a>저장소 이벤트 트리거 범위

이러한 시스템 변수는 [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger)형식의 트리거에 대 한 트리거 JSON의 어디에서 나 참조할 수 있습니다.

| 변수 이름 | 설명 |
| --- | --- |
| @triggerBody(). fileName  |생성 또는 삭제로 인해 트리거를 발생 시킨 파일의 이름입니다.   |
| @triggerBody(). folderName  |로 지정 된 파일을 포함 하는 폴더의 경로 `@triggerBody().fileName` 입니다. 폴더 경로의 첫 번째 세그먼트는 Azure Blob Storage 컨테이너의 이름입니다.  |
| @trigger().startTime |파이프라인 실행을 호출 하기 위해 트리거가 발생 한 시간입니다. |

## <a name="custom-event-trigger-scope"></a>사용자 지정 이벤트 트리거 범위

이러한 시스템 변수는 [CustomEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger)형식의 트리거에 대 한 트리거 JSON의 어디에서 나 참조할 수 있습니다.

>[!NOTE]
>Azure Data Factory [Azure Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 사용 하 여 사용자 지정 이벤트의 형식을 지정할 것으로 예상 합니다.

| 변수 이름 | 설명
| --- | --- |
| @triggerBody(). 이벤트. eventType | 사용자 지정 이벤트 트리거 실행을 트리거한 이벤트의 유형입니다. 이벤트 유형은 고객 정의 필드 이며 string 유형의 값을 사용 합니다. |
| @triggerBody(). 이벤트 제목 | 트리거를 발생 시킨 사용자 지정 이벤트의 제목입니다. |
| @triggerBody(). 이벤트 데이터. _keyName_ | 사용자 지정 이벤트의 데이터 필드는 고객이 메시지와 데이터를 전송 하는 데 사용할 수 있는 JSON blob에서 무료로 제공 됩니다. 데이터를 사용 하십시오. 각 필드를 참조 하는 _keyName_ 예를 들어 @triggerBody ().  . i a. i a. |
| @trigger().startTime | 파이프라인 실행을 호출 하기 위해 트리거가 발생 한 시간입니다. |

## <a name="next-steps"></a>다음 단계

* 이러한 변수가 식에서 사용되는 방법은 [식 언어 및 함수](control-flow-expression-language-functions.md)를 참조하세요.
* 파이프라인에서 트리거 범위 시스템 변수를 사용 하려면 [파이프라인의 참조 트리거 메타 데이터](how-to-use-trigger-parameterization.md) 를 참조 하세요.
