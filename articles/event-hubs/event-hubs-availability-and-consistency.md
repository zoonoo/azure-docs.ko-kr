---
title: "Azure Event Hubs의 가용성 및 일관성 | Microsoft Docs"
description: "파티션을 사용하여 Azure Event Hubs에서 가용성 및 일관성의 최대치를 제공하는 방법입니다."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: e293dcfc872ba95ca7a0d0faed9b4a824d3fa42c
ms.openlocfilehash: ee9a19c8ae85867a0dfcb896b9c33fd4c51be7fa
ms.lasthandoff: 02/27/2017

---

# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs의 가용성 및 일관성

## <a name="overview"></a>개요
Azure Event Hubs는 [파티션 모델](event-hubs-what-is-event-hubs.md#partitions)을 사용하여 단일 이벤트 허브 내에서 가용성 및 병렬 처리 기능을 개선합니다. 예를 들어, Event Hub에&4;개의 파티션이 있고 해당 파티션 중 하나가 부하 분산 작업 중 한 서버에서 다른 서버로 이동되는 경우 다른&3;개의 파티션을 여전히 보내고 받을 수 있습니다. 또한 더 많은 파티션을 사용하면 더 많은 동시 읽기 권한자가 데이터를 처리할 수 있으므로 집계 처리량이 개선됩니다. 분산된 시스템에서 분할 및 순서 지정이 가진 의미를 이해하는 것은 솔루션 디자인의 중요한 측면입니다.

순서 지정과 가용성 간의 균형을 잘 이해하려면 Brewer의 정리라고도 하는 [CAP 정리](https://en.wikipedia.org/wiki/CAP_theorem)를 참조하세요. 정리에 따르면 일관성, 가용성 및 파티션 허용 오차 중에서 하나를 선택해야 합니다.

Brewer의 정리는 일관성 및 가용성을 다음과 같이 정의합니다.
* 파티션 허용 오차 - 파티션 오류가 발생하는 경우 데이터 처리를 계속하는 데이터 처리 시스템의 기능입니다.
* 가용성 - 실패하지 않은 노드가 오류 또는 시간 제한 없이 적절한 시간 내에 적절한 응답을 반환합니다.
* 일관성 - 읽기는 지정된 클라이언트에 대해 가장 최근의 쓰기를 반환하도록 보장됩니다.

## <a name="partition-tolerance"></a>파티션 허용 오차
Event Hubs는 분할된 모델을 기반으로 빌드됩니다. 설치하는 동안 Event Hub의 파티션 수를 구성할 수 있지만 나중에 이 값을 변경할 수 없습니다. Event Hubs에서 파티션을 사용해야 하므로 응용 프로그램에 대한 가용성 및 일관성에 관한 결정을 내리기만 하면 됩니다.

## <a name="availability"></a>Availability
Event Hubs를 시작하는 가장 간단한 방법은 기본 동작을 사용하는 것입니다. 새 `EventHubClient`를 만들고 보내기 함수를 사용하는 경우 사용자의 이벤트는 Event Hub에서 파티션 간에 자동으로 배포됩니다. 이 동작을 사용하면 가동 시간을 늘일 수 있습니다.

최대 가동 시간을 필요로 하는 사용 사례의 경우 이 모델이 기본 설정입니다.

## <a name="consistency"></a>일관성
특정 시나리오에서는 이벤트의 순서가 중요합니다. 예를 들어, 백 엔드 시스템에서 삭제 명령 전에 업데이트 명령을 처리하려고 할 수 있습니다. 이 경우에는 이벤트에 대한 파티션 키를 설정하거나 `PartitionSender`을 사용하여 이벤트를 특정 파티션에 보낼 수 있습니다. 이렇게 하면 이러한 이벤트를 파티션에서 읽을 때 순서대로 읽게 됩니다.

이 유형의 구성을 사용하여 전송하는 특정 파티션을 사용할 수 없는 경우 오류 응답이 수신된다는 점을 염두에 두어야 합니다. 이에 비해 단일 파티션에 대한 선호도 없는 경우 Event Hubs 서비스는 사용 가능한 다음 파티션에 이벤트를 보냅니다.

순서를 지정할 수 있는 한 가지 솔루션은 가동 시간을 극대화하는 동시에 이벤트를 이벤트 처리 응용 프로그램의 일부로 집계하게 됩니다. 이 작업을 수행하는 가장 쉬운 방법은 사용자 지정 시퀀스 번호 속성을 포함한 이벤트를 각인하는 것입니다. 다음은 이러한 예제입니다.

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

이전 예제에서는 Event Hub에서 사용 가능한 파티션 중 하나에 이벤트를 보내고 응용 프로그램에서 해당 시퀀스 번호를 설정합니다. 이 솔루션에서는 상태를 처리 응용 프로그램에서 유지해야 하지만 사용할 가능성이 높은 끝점을 보낸 사람에게 제공합니다.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [이벤트 허브 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)

