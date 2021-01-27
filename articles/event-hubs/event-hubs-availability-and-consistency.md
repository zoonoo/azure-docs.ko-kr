---
title: 가용성 및 일관성 - Azure Event Hubs | Microsoft Docs
description: 파티션을 사용하여 Azure Event Hubs에서 가용성 및 일관성의 최대치를 제공하는 방법입니다.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882198"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs의 가용성 및 일관성
이 문서에서는 Azure Event Hubs에서 지 원하는 가용성 및 일관성에 대 한 정보를 제공 합니다. 

## <a name="availability"></a>가용성
Azure Event Hubs는 개별 컴퓨터의 치명적인 오류에 대 한 위험을 분산 하거나 데이터 센터 내의 여러 장애 도메인에 걸쳐 있는 클러스터 전체에 걸쳐 전체 랙을 분산 합니다. 이러한 오류는 투명 한 오류 감지 및 장애 조치 (failover) 메커니즘을 구현 하 여 서비스가 서비스 수준에서 계속 작동 하 고, 일반적으로 이러한 오류가 발생 하는 경우 눈에 띄게 중단 되지 않도록 합니다. [가용성 영역](../availability-zones/az-overview.md)에 대해 enabled 옵션을 사용 하 여 Event Hubs 네임 스페이스를 만든 경우 중단 위험은 실제로 분리 된 세 가지 기능에 추가로 분산 되며 서비스에는 전체 기능의 완전 한 치명적인 손실을 신속 하 게 처리할 수 있는 충분 한 용량 예약이 있습니다. 자세한 내용은 [Azure Event Hubs-지역 재해 복구](event-hubs-geo-dr.md)를 참조 하세요.

클라이언트 응용 프로그램에서 이벤트 허브로 이벤트를 보낼 때 이벤트는 이벤트 허브의 파티션 사이에 자동으로 분산 됩니다. 어떤 이유로 파티션을 사용할 수 없는 경우 이벤트는 나머지 파티션에 분산 됩니다. 이 동작을 사용하면 가동 시간을 최대화할 수 있습니다. 최대 가동 시간을 필요로 하는 사용 사례의 경우이 모델을 사용 하 여 특정 파티션에 이벤트를 전송 하는 대신이 모델을 사용 하는 것이 좋습니다. 자세한 내용은 [파티션](event-hubs-scalability.md#partitions)을 참조하세요.

## <a name="consistency"></a>일관성
일부 시나리오에서는 이벤트의 순서가 중요합니다. 예를 들어, 백 엔드 시스템에서 삭제 명령 전에 업데이트 명령을 처리하려고 할 수 있습니다. 이 시나리오에서 클라이언트 응용 프로그램은 순서가 유지 되도록 이벤트를 특정 파티션으로 보냅니다. 소비자 응용 프로그램은 파티션에서 이러한 이벤트를 사용 하 여 순서 대로 읽습니다. 

이 구성에서는 전송하는 특정 파티션을 사용할 수 없는 경우 오류 응답이 수신된다는 점을 염두에 두어야 합니다. 비교 시점으로 단일 파티션에 대 한 선호도가 없는 경우 Event Hubs 서비스는 사용 가능한 다음 파티션으로 이벤트를 보냅니다.

가동 시간을 극대화하는 동시에 순서도 지정할 수 있는 한 가지 솔루션은 이벤트 처리 애플리케이션의 일부로 이벤트를 집계하는 것입니다. 이를 수행 하는 가장 쉬운 방법은 사용자 지정 시퀀스 번호 속성을 사용 하 여 이벤트를 스탬프 하는 것입니다.

이 시나리오에서 생산자 클라이언트는 이벤트 허브에서 사용 가능한 파티션 중 하나에 이벤트를 보내고 응용 프로그램에서 해당 시퀀스 번호를 설정 합니다. 이 솔루션에서는 상태를 처리 애플리케이션에서 유지해야 하지만 사용할 가능성이 높은 엔드포인트를 보낸 사람에게 제공합니다.

## <a name="appendix"></a>부록

### <a name="net-examples"></a>.NET 예제

#### <a name="send-events-to-a-specific-partition"></a>특정 파티션으로 이벤트 전송
이벤트에 파티션 키를 설정 하거나 `PartitionSender` 개체 (이전 Microsoft. Azure. Messaging 라이브러리를 사용 하는 경우)를 사용 하 여 특정 파티션에만 이벤트를 전송 합니다. 이렇게 하면 이러한 이벤트를 파티션에서 읽을 때 순서대로 읽게 됩니다. 

최신 **EventHubs** 라이브러리를 사용 하는 경우 [파티션에 이벤트를 게시 하기 위해 분할 된 발신자에서 EventHubProducerClient로 코드 마이그레이션](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition)을 참조 하세요.

#### <a name="azuremessagingeventhubs-500-or-later"></a>[EventHubs (5.0.0 이상)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[EventHubs (4.1.0 또는 이전 버전)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

### <a name="set-a-sequence-number"></a>시퀀스 번호 설정
다음 예제에서는 사용자 지정 시퀀스 번호 속성을 사용 하 여 이벤트를 스탬프 처리 합니다. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[EventHubs (5.0.0 이상)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[EventHubs (4.1.0 또는 이전 버전)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

이 예에서는 이벤트 허브에서 사용 가능한 파티션 중 하나에 이벤트를 보내고 애플리케이션에서 해당 시퀀스 번호를 설정합니다. 이 솔루션에서는 상태를 처리 애플리케이션에서 유지해야 하지만 사용할 가능성이 높은 엔드포인트를 보낸 사람에게 제공합니다.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 서비스 개요](./event-hubs-about.md)
* [이벤트 허브 만들기](event-hubs-create.md)
