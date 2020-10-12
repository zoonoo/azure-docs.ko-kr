---
title: Azure Stream Analytics에 대 한 이벤트 순서 정책 구성
description: 이 문서에서는 Stream Analytics에서 짝수 순서 설정을 구성 하는 방법을 설명 합니다.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: b4e34befbf28de2b985ff49ce17a87a25842015e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87901694"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Azure Stream Analytics에 대 한 이벤트 순서 정책 구성

이 문서에서는 Azure Stream Analytics에서 지연 도착 및 순서 없는 이벤트 정책을 설정 하 고 사용 하는 방법을 설명 합니다. 이러한 정책은 쿼리에 [타임 스탬프 by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 절을 사용 하는 경우에만 적용 되며 클라우드 입력 원본에만 적용 됩니다.

## <a name="event-time-and-arrival-time"></a>이벤트 시간 및 도착 시간

Stream Analytics 작업은 *이벤트 시간* 또는 *도착 시간*을 기준으로 이벤트를 처리할 수 있습니다. 이벤트 **/응용 프로그램 시간은** 이벤트가 생성 될 때 이벤트 페이로드에 있는 타임 스탬프입니다. **도착 시간은** 이벤트를 입력 원본 (Event Hubs/IoT Hub/Blob 저장소)에서 받은 타임 스탬프입니다. 

기본적으로 Stream Analytics는 *도착 시간*을 기준으로 이벤트를 처리 하지만 쿼리에서 [TIMESTAMP by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 절을 사용 하 여 이벤트 *시간별* 로 이벤트를 처리 하도록 선택할 수 있습니다. 지연 도착 및 순서가 잘못 된 정책은 이벤트 시간별로 이벤트를 처리 하는 경우에만 적용 됩니다. 이러한 설정을 구성하는 경우 시나리오에 대한 대기 시간 및 정확성 요구 사항을 고려해야 합니다. 

## <a name="what-is-late-arrival-policy"></a>지연 도착 정책이란?

이벤트는 다양 한 이유로 인해 늦게 도착 하기도 합니다. 예를 들어, 지연 시간을 40 초 동안 도착 하는 이벤트는 이벤트 시간 = 00:10:00 및 도착 시간 = 00:10:40이 있습니다. 지연 도착 정책을 15 초로 설정 하면 15 초 보다 늦게 도착 하는 이벤트는 삭제 되거나 (Stream Analytics에서 처리 되지 않음) 이벤트 시간이 조정 됩니다. 위의 예제에서 이벤트가 40 초 지연 (정책 집합 초과)에 도달 하면 해당 이벤트 시간은 최대 지연 도착 정책 00:10:25 (도착 시간-지연 도착 정책 값)으로 조정 됩니다. 기본 지연 도착 정책은 5 초입니다.

## <a name="what-is-out-of-order-policy"></a>순서가 잘못 된 정책 이란? 

이벤트가 잘못 된 순서로 도착할 수 있습니다. 이벤트 시간이 지연 도착 정책에 따라 조정 된 후에는 순서가 잘못 된 이벤트를 자동으로 삭제 하거나 조정 하도록 선택할 수도 있습니다. 이 정책을 8 초로 설정 하면 잘못 된 순서로 도착 하는 이벤트는 모두 이벤트 시간을 기준으로 다시 정렬 됩니다. 나중에 도착 하는 이벤트는 삭제 되거나 순서가 잘못 된 최대 정책 값으로 조정 됩니다. 기본 순서가 잘못 된 정책은 0 초입니다. 

## <a name="adjust-or-drop-events"></a>이벤트 조정 또는 삭제

구성 된 정책에 따라 이벤트가 늦게 나 순서 대로 도착 하는 경우 이러한 이벤트를 삭제 하거나 (Stream Analytics에서 처리 되지 않음) 이벤트 시간을 조정할 수 있습니다.

이러한 정책에 대 한 예를 실행 하는 방법을 살펴보겠습니다.
<br> **지연 도착 정책:** 15 초
<br> **순서가 잘못 된 정책:** 8 초

| 이벤트 번호 | 이벤트 시간 | 도착 시간 | System.Timestamp | 설명 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 이벤트가 지연 및 외부 허용 수준에 도달 했습니다. 따라서 이벤트 시간은 최대 지연 도착 허용 시간으로 조정 됩니다.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 이벤트가 허용 수준 내에서 늦게 도착 했습니다. 따라서 이벤트 시간이 조정 되지 않습니다.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 이벤트가 시간에 도달 했습니다. 조정이 필요 하지 않습니다.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 이벤트가 잘못 된 순서로 도착 했지만 허용 오차 범위는 8 초입니다. 따라서 이벤트 시간을 조정 하지 않습니다. 분석을 위해이 이벤트는 이전 이벤트 번호 4로 간주 됩니다.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 이벤트가 잘못 된 순서로 도착 하 고 허용 되는 범위는 8 초입니다. 따라서 이벤트 시간은 잘못 된 순서 허용 오차의 최대값으로 조정 됩니다. |

## <a name="can-these-settings-delay-output-of-my-job"></a>이러한 설정은 내 작업의 출력을 지연 시킬 수 있나요? 

예. 기본적으로 순서가 잘못 된 정책은 0 (00 분 및 00 초)으로 설정 됩니다. 기본값을 변경 하면 작업의 첫 번째 출력이이 값 (이상)에 의해 지연 됩니다. 

입력 파티션 중 하나가 이벤트를 수신 하지 않는 경우 지연 도착 정책 값에 의해 출력이 지연 될 것으로 간주 됩니다. 이유를 알아보려면 아래의 InputPartition 오류 섹션을 참조 하세요. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>활동 로그에 LateInputEvents 메시지가 표시 됩니다.

이러한 메시지는 이벤트가 늦게 도착 하 여 구성에 따라 삭제 되거나 조정 됨을 알리기 위해 표시 됩니다. 지연 도착 정책을 적절 하 게 구성한 경우 이러한 메시지를 무시할 수 있습니다. 

이 메시지의 예는 다음과 같습니다. <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>활동 로그에서 Input파티션 진행이 표시 됩니다.

입력 원본 (Event Hub/IoT Hub)에 파티션이 여러 개 있을 수 있습니다. Azure Stream Analytics는 결합 된 모든 파티션이 최소 t1 시간 이상인 경우에만 타임 스탬프 t1에 대 한 출력을 생성 합니다. 예를 들어 쿼리가 두 개의 파티션이 있는 이벤트 허브 파티션에서 읽는다고 가정합니다. 파티션 중 하나인 P1에는 t1 시간까지 이벤트가 있습니다. 다른 파티션 P2에는 t1 + x 시간까지 이벤트가 있습니다. t1 시간까지 출력이 생성됩니다. 그러나 명시적 Partition by PartitionId 절이 있으면 두 파티션은 모두 독립적으로 진행합니다. 

동일한 입력 스트림의 여러 파티션이 결합 된 경우 지연 도착 허용 시간은 모든 파티션이 새 데이터를 대기 하는 최대 시간입니다. 이벤트 허브에 하나의 파티션이 있거나 IoT Hub 입력을 수신 하지 않는 경우 지연 도착 허용 임계값에 도달할 때까지 해당 파티션의 타임 라인이 진행 되지 않습니다. 이렇게 하면 지연 도착 허용 임계값에 따라 출력이 지연 됩니다. 이러한 경우 다음과 같은 메시지가 표시 될 수 있습니다.
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
이 메시지는 입력에 있는 하나 이상의 파티션이 비어 있음을 알려 주고 지연 도착 임계값에 따라 출력을 지연 시킵니다. 이를 해결 하려면 다음 중 하나를 수행 하는 것이 좋습니다.  
1. 이벤트 허브/IoT Hub의 모든 파티션이 입력을 수신 하는지 확인 합니다. 
2. 쿼리에 Partition by PartitionID 절을 사용 합니다. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>지연 도착 정책이 0으로 설정 된 경우에도 5 초 지연 시간이 표시 되는 이유는 무엇 인가요?
이는 입력이 수신 되지 않은 입력 파티션이 있는 경우에 발생 합니다. 파티션당 입력 메트릭을 확인 하 여이 동작의 유효성을 검사할 수 있습니다. 

파티션이 구성 된 지연 도착 임계값을 초과 하는 데이터를 포함 하지 않는 경우 stream analytics는 이벤트 순서 고려 사항 섹션에 설명 된 대로 응용 프로그램 타임 스탬프를 이동 합니다. 이 경우 예상 도착 시간이 필요 합니다. 파티션에 데이터가 전혀 없는 경우 stream analytics는 도착 시간을 *현지 시간-5 초로*추정 합니다. 이 파티션에는 데이터가 전혀 없는 경우 워터 마크 지연 시간이 5 초인 것으로 표시 될 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* [시간 처리 고려 사항](stream-analytics-time-handling.md)
* [Stream Analytics에서 사용 가능한 메트릭](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
