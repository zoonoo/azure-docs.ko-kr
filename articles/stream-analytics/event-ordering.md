---
title: Azure Stream 분석을 위한 이벤트 주문 정책 구성
description: 이 문서에서는 스트림 애널리틱스에서 주문 설정도 구성하는 방법에 대해 설명합니다.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461200"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Azure Stream 분석을 위한 이벤트 주문 정책 구성

이 문서에서는 Azure Stream Analytics에서 늦은 도착 및 순서가 바도가 늦은 이벤트 정책을 설정하고 사용하는 방법을 설명합니다. 이러한 정책은 쿼리에서 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 절을 사용하는 경우에만 적용됩니다.

## <a name="event-time-and-arrival-time"></a>이벤트 시간 및 도착 시간

Stream Analytics 작업은 *이벤트 시간* 또는 *도착 시간을*기준으로 이벤트를 처리할 수 있습니다. **이벤트/응용 프로그램 시간은** 이벤트 페이로드(이벤트가 생성된 경우)에 있는 타임스탬프입니다. **도착 시간은** 입력 소스(이벤트 허브/IoT Hub/Blob 저장소)에서 이벤트를 수신한 타임스탬프입니다. 

기본적으로 Stream Analytics는 *도착 시간별로*이벤트를 처리하지만 쿼리에서 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 절을 사용하여 이벤트 *시간별로* 이벤트를 처리하도록 선택할 수 있습니다. 이벤트 시간별로 이벤트를 처리하는 경우에만 늦은 도착 및 순서가 바등기 않은 정책이 적용됩니다. 이러한 설정을 구성하는 경우 시나리오에 대한 대기 시간 및 정확성 요구 사항을 고려해야 합니다. 

## <a name="what-is-late-arrival-policy"></a>지연 도착 정책이란?

때로는 여러 가지 이유로 인해 이벤트가 늦게 도착합니다. 예를 들어 40초 늦게 도착하는 이벤트에는 이벤트 시간 = 00:10:00 및 도착 시간 = 00:10:40이 있습니다. 늦은 도착 정책을 15초로 설정하면 15초 이상 늦게 도착하는 모든 이벤트가 삭제되거나(Stream Analytics에서 처리되지 않음) 이벤트 시간이 조정됩니다. 위의 예에서 이벤트가 40초 늦게 도착(정책 설정 이상)에 도착하면 이벤트 시간이 최대 도착 00:10:25(도착 시간 ~ 늦은 도착 정책 값)로 조정됩니다. 기본 늦은 도착 정책은 5초입니다.

## <a name="what-is-out-of-order-policy"></a>순서가 바도가 다른 정책이란 무엇입니까? 

이벤트도 순서에 따라 도착할 수 있습니다. 늦은 도착 정책에 따라 이벤트 시간을 조정한 후 순서가 다른 이벤트를 자동으로 삭제하거나 조정할 수도 있습니다. 이 정책을 8초로 설정하면 순서가 정차하지만 8초 내에 도착하는 모든 이벤트는 이벤트 시간에 따라 순서가 바뀝습니다. 나중에 도착하는 이벤트는 삭제되거나 최대 순서가 벗어난 정책 값으로 조정됩니다. 기본 순서가 기본이 0초입니다. 

## <a name="adjust-or-drop-events"></a>이벤트 조정 또는 삭제

구성한 정책에 따라 이벤트가 늦게 또는 순서가 잘못되면 해당 이벤트를 삭제하거나(Stream Analytics에서 처리하지 않음) 이벤트 시간을 조정할 수 있습니다.

이러한 정책의 실천 사례를 살펴보겠습니다.
<br> **늦은 도착 정책:** 15초
<br> **순서가 바도 가없습니다:** 8초

| 이벤트 번호 | 이벤트 시간 | 도착 시간 | System.Timestamp | 설명 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 이벤트는 늦게 및 외부 허용 오차 수준에 도착했다. 따라서 이벤트 시간은 최대 늦은 도착 허용 오차로 조정됩니다.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 이벤트는 늦게 도착했지만 허용 오차 수준 이내로 발생했습니다. 따라서 이벤트 시간은 조정되지 않습니다.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 이벤트가 정시에 도착했습니다. 조정이 필요하지 않습니다.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 이벤트는 순서가 정연하지만 8 초 의 허용 오차 내에서 도착했다. 따라서 이벤트 시간은 조정되지 않습니다. 분석을 위해 이 이벤트는 이전 이벤트 번호 4로 간주됩니다.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 이벤트는 8 초의 순서가 정연하고 외부 허용 오차가 도착했습니다. 따라서 이벤트 시간은 순서가 바도가 다른 허용 오차의 최대값으로 조정됩니다. |

## <a name="can-these-settings-delay-output-of-my-job"></a>이러한 설정으로 인해 작업의 출력이 지연될 수 있습니까? 

예. 기본적으로 순서가 바도가 낮은 정책은 0(00분 및 00초)으로 설정됩니다. 기본값을 변경하면 작업의 첫 번째 출력이 이 값(또는 그 이상)으로 지연됩니다. 

입력의 파티션 중 하나가 이벤트를 수신하지 않는 경우 늦게 도착 정책 값으로 출력이 지연될 것으로 예상해야 합니다. 그 이유를 알아보려면 아래의 InputPartition 오류 섹션을 읽어보십시오. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>내 활동 로그에 LateInputEvents 메시지가 표시됩니다.

이러한 메시지는 이벤트가 늦게 도착했으며 구성에 따라 삭제되거나 조정됨을 알리는 것으로 표시됩니다. 늦은 도착 정책을 적절하게 구성한 경우 이러한 메시지를 무시할 수 있습니다. 

이 메시지의 예는 다음과 같은 것입니다. <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>내 활동 로그에 입력 파티션이 표시되지 않음 진행

입력 소스(이벤트 허브/IoT Hub)에는 여러 파티션이 있습니다. Azure Stream Analytics는 결합된 모든 파티션이 적어도 시간 t1인 후에만 타임스탬프 t1에 대한 출력을 생성합니다. 예를 들어 쿼리가 두 개의 파티션이 있는 이벤트 허브 파티션에서 읽는다고 가정합니다. 파티션 중 하나인 P1에는 t1 시간까지 이벤트가 있습니다. 다른 파티션 P2에는 t1 + x 시간까지 이벤트가 있습니다. t1 시간까지 출력이 생성됩니다. 그러나 명시적 Partition by PartitionId 절이 있으면 두 파티션은 모두 독립적으로 진행합니다. 

동일한 입력 스트림의 여러 파티션이 결합된 경우 늦은 도착 허용 오차는 모든 파티션이 새 데이터를 기다리는 최대 시간입니다. 이벤트 허브에 파티션이 하나 있거나 IoT Hub가 입력을 받지 못하는 경우 해당 파티션의 타임라인이 늦은 도착 허용 오차 임계값에 도달할 때까지 진행되지 않습니다. 이렇게 하면 늦은 도착 허용 오차 임계값까지 출력이 지연됩니다. 이러한 경우 다음 메시지가 표시될 수 있습니다.
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
이 메시지는 입력에 있는 하나 이상의 파티션이 비어 있으며 늦게 도착 임계값까지 출력을 지연시겠습니까 를 알려줍니다. 이 문제를 극복하려면 다음 중 하나를 권장합니다.  
1. 이벤트 허브/IoT Hub의 모든 파티션이 입력을 받도록 합니다. 
2. 쿼리에서 PartitionID에 의한 파티션을 사용합니다. 

## <a name="next-steps"></a>다음 단계
* [시간 처리 고려 사항](stream-analytics-time-handling.md)
* [Stream Analytics에서 사용 가능한 메트릭](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
