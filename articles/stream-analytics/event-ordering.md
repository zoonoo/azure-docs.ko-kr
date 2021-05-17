---
title: Azure Stream Analytics에 대한 이벤트 순서 정책 구성
description: 이 문서에서는 Stream Analytics에서 이벤트 순서를 구성하는 방법을 설명합니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: f7ec6f32b48a93a29210311c7ba6747eb2e2d066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014298"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Azure Stream Analytics에 대한 이벤트 순서 정책 구성

이 문서에서는 Azure Stream Analytics에서 지연 도착 및 순서가 잘못된 이벤트 정책을 설정하고 사용하는 방법을 설명합니다. 이러한 정책은 쿼리에 [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) 절을 사용하는 경우에만 적용되며 클라우드 입력 원본에만 적용됩니다.

## <a name="event-time-and-arrival-time"></a>이벤트 시간 및 도착 시간

Stream Analytics 작업은 *이벤트 시간* 또는 *도착 시간* 을 기준으로 이벤트를 처리할 수 있습니다. **이벤트/애플리케이션 시간** 은 이벤트가 생성될 때 이벤트 페이로드에 있는 타임스탬프입니다. **도착 시간** 은 이벤트를 입력 원본(Event Hubs/IoT Hub/Blob Storage)에서 받은 타임스탬프입니다. 

기본적으로 Stream Analytics는 *도착 시간* 을 기준으로 이벤트를 처리하지만 쿼리에서 [TIMESTAMP by](/stream-analytics-query/timestamp-by-azure-stream-analytics) 절을 사용하여 *이벤트 시간* 기준으로 이벤트를 처리하도록 선택할 수 있습니다. 지연 도착 및 순서가 잘못된 정책은 이벤트 시간별로 이벤트를 처리하는 경우에만 적용 됩니다. 이러한 설정을 구성하는 경우 시나리오에 대한 대기 시간 및 정확성 요구 사항을 고려해야 합니다. 

## <a name="what-is-late-arrival-policy"></a>지연 도착 정책이란?

이벤트는 다양한 이유로 인해 늦게 도착하기도 합니다. 예를 들어, 40초 더 늦게 도착하는 이벤트의 이벤트 시간이 00:10:00이고 도착 시간이 00:10:40입니다. 지연 도착 정책을 15초로 설정하면 15초보다 늦게 도착하는 이벤트는 삭제되거나(Stream Analytics에서 처리되지 않음) 이벤트 시간이 조정됩니다. 위의 예제에서 이벤트가 40초 더 늦게 도착하므로 해당 이벤트 시간은 최대 지연 도착 정책 00:10:25(도착 시간 - 지연 도착 정책 값)으로 조정됩니다. 기본 지연 도착 정책은 5초입니다.

## <a name="what-is-out-of-order-policy"></a>순서가 잘못된 정책이란? 

이벤트가 잘못된 순서로 도착할 수 있습니다. 이벤트 시간이 지연 도착 정책에 따라 조정된 후에는 순서가 잘못된 이벤트를 자동으로 삭제하거나 조정하도록 선택할 수도 있습니다. 이 정책을 8초로 설정하면 잘못된 순서로 도착하지만 8초 기간에 포함된 이벤트는 모두 이벤트 시간을 기준으로 다시 정렬됩니다. 더 늦게 도착하는 이벤트는 삭제되거나 순서가 잘못된 최대 정책 값으로 조정됩니다. 순서가 잘못된 정책 기본값은 0초입니다. 

## <a name="adjust-or-drop-events"></a>이벤트 조정 또는 삭제

구성된 정책에 따라 이벤트가 늦게 도착하거나 순서가 잘못된 경우 이러한 이벤트를 삭제(Stream Analytics에서 처리하지 않음)하거나 이벤트 시간을 조정할 수 있습니다.

작업에서 볼 수 있는 이러한 정책에 대한 예제를 살펴보겠습니다.
<br> **지연 도착 정책:** 15초
<br> **순서가 잘못된 정책:** 8초

| 이벤트 번호 | 이벤트 시간 | 도착 시간 | System.Timestamp | 설명 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 이벤트가 허용 수준을 벗어나 지연 도착했습니다. 따라서 이벤트 시간은 최대 지연 도착 허용 시간으로 조정됩니다.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 이벤트가 지연 도착했지만 허용 수준 이내에 도착했습니다. 따라서 이벤트 시간이 조정되지 않습니다.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 이벤트가 제 시간에 도착했습니다. 조정이 필요하지 않습니다.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 이벤트가 잘못된 순서로 도착했지만 허용 오차 범위인 8초 이내에 도착했습니다. 따라서 이벤트 시간이 조정되지 않습니다. 분석을 위해 이 이벤트는 이전 이벤트 번호 4로 간주됩니다.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 이벤트가 잘못된 순서로 도착했으며 허용 오차 범위인 8초를 벗어났습니다. 따라서 이벤트 시간은 최대 잘못된 순서 허용 오차로 조정됩니다. |

## <a name="can-these-settings-delay-output-of-my-job"></a>이러한 설정으로 작업 출력을 지연할 수 있나요? 

예. 기본적으로 잘못된 순서 정책은 0(00분 00초)으로 설정됩니다. 기본값을 변경하면 작업의 첫 번째 출력이 이 값만큼(또는 그 이상) 지연됩니다. 

입력 파티션 중 하나가 이벤트를 수신하지 않는 경우 지연 도착 정책 값만큼 출력이 지연될 것으로 예상됩니다. 이유를 알아보려면 아래의 InputPartition 오류 섹션을 참조하세요. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>활동 로그에 LateInputEvents 메시지가 표시됩니다.

이러한 메시지는 이벤트가 늦게 도착하여 구성에 따라 삭제되거나 조정됨을 알리기 위해 표시됩니다. 지연 도착 정책을 적절하게 구성한 경우 이러한 메시지를 무시해도 됩니다. 

이 메시지의 예는 다음과 같습니다. <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>활동 로그에 InputPartitionNotProgressing이 표시됩니다.

입력 원본(이벤트 허브/IoT Hub)에 파티션이 여러 개 있을 수 있습니다. Azure Stream Analytics는 결합된 모든 파티션이 적어도 t1 시간이 된 후에만 타임스탬프 t1에 대한 출력을 생성합니다. 예를 들어 쿼리가 두 개의 파티션이 있는 이벤트 허브 파티션에서 읽는다고 가정합니다. 파티션 중 하나인 P1에는 t1 시간까지 이벤트가 있습니다. 다른 파티션 P2에는 t1 + x 시간까지 이벤트가 있습니다. t1 시간까지 출력이 생성됩니다. 그러나 명시적 Partition by PartitionId 절이 있으면 두 파티션은 모두 독립적으로 진행합니다. 

같은 입력 스트림의 여러 파티션이 결합될 때의 지연 도착 허용 시간은 모든 파티션이 새 데이터 도착을 대기하는 최대 시간입니다. 이벤트 허브에 하나의 파티션이 있거나 IoT Hub가 입력을 수신하지 않는 경우 지연 도착 허용 임계값에 도달할 때까지 해당 파티션의 타임라인이 진행되지 않습니다. 이로 인해 지연 도착 허용 임계값만큼 출력이 지연됩니다. 이러한 경우 다음과 같은 메시지가 표시될 수 있습니다.
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
이 메시지는 입력에 있는 하나 이상의 파티션이 비어 있으며 지연 도착 임계값만큼 출력이 지연될 것임을 알려줍니다. 이를 해결하려면 다음 중 하나를 수행하는 것이 좋습니다.  
1. 이벤트 허브/IoT Hub의 모든 파티션이 입력을 수신하는지 확인합니다. 
2. 쿼리에 Partition by PartitionID 절을 사용합니다. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>지연 도착 정책이 0으로 설정된 경우에도 5초의 지연 시간이 표시되는 이유는 무엇인가요?
이 문제는 입력이 수신되지 않은 입력 파티션이 있는 경우에 발생합니다. 파티션당 입력 메트릭을 확인하여 이 동작이 유효한지 검사할 수 있습니다. 

파티션이 구성된 지연 도착 임계값을 초과하는 기간 동안 데이터를 포함하지 않는 경우 Stream Analytics는 이벤트 순서 고려 사항 섹션에 설명된 대로 애플리케이션 타임스탬프를 진행합니다. 이 경우 예상 도착 시간이 필요합니다. 파티션에 데이터가 전혀 없는 경우 Stream Analytics는 도착 시간을 *로컬 시간-5초* 로 추정합니다. 데이터가 전혀 없는 이 파티션으로 인해 5초의 워터마크 지연이 표시될 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* [시간 처리 고려 사항](stream-analytics-time-handling.md)
* [Stream Analytics에서 사용 가능한 메트릭](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)