---
title: 가용성 및 일관성 - Azure Event Hubs | Microsoft Docs
description: 파티션을 사용하여 Azure Event Hubs에서 가용성 및 일관성의 최대치를 제공하는 방법입니다.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6005a51314cff19883fc2a07e4810bd24eb94b24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600958"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs의 가용성 및 일관성
이 문서에서는 Azure Event Hubs에서 지 원하는 가용성 및 일관성에 대 한 정보를 제공 합니다. 

## <a name="availability"></a>가용성
Azure Event Hubs는 개별 컴퓨터의 치명적인 오류에 대 한 위험을 분산 하거나 데이터 센터 내의 여러 장애 도메인에 걸쳐 있는 클러스터 전체에 걸쳐 전체 랙을 분산 합니다. 이러한 오류는 투명 한 오류 감지 및 장애 조치 (failover) 메커니즘을 구현 하 여 서비스가 서비스 수준에서 계속 작동 하 고, 일반적으로 이러한 오류가 발생 하는 경우 눈에 띄게 중단 되지 않도록 합니다. 

[가용성 영역](../availability-zones/az-overview.md) 을 사용 하도록 설정 된 Event Hubs 네임 스페이스를 만든 경우 중단 위험은 물리적으로 분리 된 세 가지 기능에 추가로 분산 되며 서비스에는 전체 기능의 완전 한 치명적인 손실을 즉시 처리할 수 있는 충분 한 용량 예약이 있습니다. 자세한 내용은 [Azure Event Hubs-지역 재해 복구](event-hubs-geo-dr.md)를 참조 하세요.

클라이언트 응용 프로그램에서 파티션을 지정 하지 않고 이벤트 허브로 이벤트를 보낼 때 이벤트는 이벤트 허브의 파티션 사이에 자동으로 분산 됩니다. 어떤 이유로 파티션을 사용할 수 없는 경우 이벤트는 나머지 파티션에 분산 됩니다. 이 동작을 사용하면 가동 시간을 최대화할 수 있습니다. 최대 가동 시간을 필요로 하는 사용 사례의 경우이 모델을 사용 하 여 특정 파티션에 이벤트를 전송 하는 대신이 모델을 사용 하는 것이 좋습니다. 

## <a name="consistency"></a>일관성
일부 시나리오에서는 이벤트의 순서가 중요합니다. 예를 들어, 백 엔드 시스템에서 삭제 명령 전에 업데이트 명령을 처리하려고 할 수 있습니다. 이 시나리오에서 클라이언트 응용 프로그램은 순서가 유지 되도록 이벤트를 특정 파티션으로 보냅니다. 소비자 응용 프로그램은 파티션에서 이러한 이벤트를 사용 하 여 순서 대로 읽습니다. 

이 구성에서는 전송하는 특정 파티션을 사용할 수 없는 경우 오류 응답이 수신된다는 점을 염두에 두어야 합니다. 비교 시점으로 단일 파티션에 대 한 선호도가 없는 경우 Event Hubs 서비스는 사용 가능한 다음 파티션으로 이벤트를 보냅니다.

따라서 고가용성이 가장 중요 한 경우에는 파티션 ID/키를 사용 하 여 특정 파티션을 대상으로 지정 하지 마십시오. Partition ID/key를 사용 하 여 파티션 수준으로 이벤트 허브의 가용성을 다운 그레이드 수 있습니다. 이 시나리오에서는 가용성 (파티션 ID/키 없음) 및 일관성 (이벤트를 특정 파티션에 고정) 사이에서 명시적으로 선택할 수 있습니다. Event Hubs의 파티션에 대 한 자세한 내용은 [파티션](event-hubs-features.md#partitions)을 참조 하세요.

## <a name="appendix"></a>부록

### <a name="send-events-without-specifying-a-partition"></a>파티션을 지정 하지 않고 이벤트 보내기
파티션 정보를 설정 하지 않고 이벤트 허브에 이벤트를 전송 하는 것이 좋습니다. Event Hubs 서비스에서 파티션 간에 부하를 분산할 수 있도록 합니다. 다른 프로그래밍 언어에서이 작업을 수행 하는 방법에 대해 알아보려면 다음 빠른 시작을 참조 하세요. 

- [.NET을 사용 하 여 이벤트 전송](event-hubs-dotnet-standard-getstarted-send.md)
- [Java를 사용 하 여 이벤트 보내기](event-hubs-java-get-started-send.md)
- [JavaScript를 사용 하 여 이벤트 보내기](event-hubs-python-get-started-send.md)
- [Python을 사용하여 이벤트 보내기](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>특정 파티션으로 이벤트 전송
이 섹션에서는 다양 한 프로그래밍 언어를 사용 하 여 특정 파티션에 이벤트를 보내는 방법에 대해 알아봅니다. 

### <a name="net"></a>[.NET](#tab/dotnet)
특정 파티션에 이벤트를 보내려면 [](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) `PartitionId` `PartitionKey` [createbatchasync](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions)에서 또는를 지정 하 여 EventHubProducerClient을 사용 하 여 일괄 처리를 만듭니다. 다음 코드에서는 파티션 키를 지정 하 여 이벤트 일괄 처리를 특정 파티션으로 보냅니다. Event Hubs 파티션 키 값을 공유 하는 모든 이벤트가 함께 저장 되 고 도착 순서 대로 배달 되도록 합니다.

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

[SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions)에서 **PartitionId** 또는 **PartitionKey** 을 지정 하 여 SendAsync 메서드를 사용할 수도 있습니다 [EventHubProducerClient.](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_)

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
특정 파티션에 이벤트를 보내려면 [Createbatchoptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions)에서 **파티션 ID** 또는 **파티션 키** 를 지정 하 여 [createbatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) 메서드를 사용 하 여 일괄 처리를 만듭니다. 다음 코드에서는 파티션 키를 지정 하 여 이벤트 일괄 처리를 특정 파티션으로 보냅니다. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

[SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions)에서 **파티션 ID** 또는 **파티션 키** 를 지정 하 여 [EventHubProducerClient](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) 메서드를 사용할 수도 있습니다.

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
특정 파티션에 이벤트를 보내려면 메서드를 사용 하 여 일괄 처리를 만들 때 [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) 또는을 지정 합니다 `partition_id` `partition_key` . 그런 다음 메서드를 사용 [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) 하 여 이벤트 허브의 파티션으로 일괄 처리를 보냅니다. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

또는 매개 변수의 값을 지정 하 여 [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) 메서드를 사용할 수도 있습니다 `partition_id` `partition_key` .

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
특정 파티션에 이벤트를 전송 하려면 또는를 지정 하 여 [EventHubProducerClient 옵션](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) 개체를 사용 하 여 [일괄 처리](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) 를 만듭니다 `partitionId` `partitionKey` . 그런 다음 [EventHubProducerClient](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) 메서드를 사용 하 여 이벤트 허브로 일괄 처리를 보냅니다. 

다음 예제를 참조하세요.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

또한 [Sendbatchoptions](/javascript/api/@azure/event-hubs/sendbatchoptions)에서 **파티션 ID** 또는 **파티션 키** 를 지정 하 여 [EventHubProducerClient](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) 메서드를 사용할 수 있습니다.

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Event Hubs 서비스 개요](./event-hubs-about.md)
- [Event Hubs 용어](event-hubs-features.md)
