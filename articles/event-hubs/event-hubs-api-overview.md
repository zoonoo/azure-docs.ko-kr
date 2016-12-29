---
title: "Azure Event Hubs API 개요 | Microsoft 문서"
description: "주요 이벤트 허브 .NET 클라이언트 API의 일부 요약입니다."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7f3b6cc0-9600-417f-9e80-2345411bd036
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: c68b5cfadc3e98e15c119b3717c8098887ca67d0
ms.openlocfilehash: e8c97689f84d3bedb312ec4443427181a542e95b


---
# <a name="event-hubs-api-overview"></a>이벤트 허브 API 개요
이 문서는 키 이벤트 허브 .NET 클라이언트 API의 일부를 요약합니다. 관리와 런타임 API 등 두 가지 범주가 있습니다. 런타임 API는 메시지를 주고받는 데 필요한 모든 작업으로 구성됩니다. 관리 작업을 사용하면 엔터티를 만들고 업데이트 및 삭제하여 이벤트 허브 엔터티 상태를 관리할 수 있습니다.

모니터링 시나리오는 관리 및 런타임 모두에 사용됩니다. .NET API에 대한 자세한 참조 설명서는 [Service Bus .NET](/dotnet/api) 및 [EventProcessorHost API](/dotnet/api) 참조를 참조하세요.

## <a name="management-apis"></a>관리 API
다음 관리 작업을 수행하려면 이벤트 허브 네임스페이스에 대한 **관리** 권한이 있어야 합니다.

### <a name="create"></a>생성
```csharp
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### <a name="update"></a>업데이트
```csharp
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### <a name="delete"></a>삭제
```csharp
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## <a name="run-time-apis"></a>런타임 API
### <a name="create-publisher"></a>게시자 만들기
```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>메시지 게시
```csharp
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>소비자 만들기
```csharp
// Create the Event Hubs client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
EventHubConsumerGroup defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>메시지 사용
```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>이벤트 프로세서 호스트 API
이러한 API는 사용 가능한 작업자에 걸쳐 분할을 배포하여 사용할 수 없게 되는 작업자 프로세스에 탄력성을 제공합니다.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

[IEventProcessor](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) 인터페이스는 다음과 같이 정의됩니다.

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
이벤트 허브 시나리어에 대한 자세한 내용은 다음 링크를 방문하십시오.

* [Azure 이벤트 허브 정의](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 개요](event-hubs-overview.md)
* [이벤트 허브 프로그래밍 가이드](event-hubs-programming-guide.md)
* [Event Hubs 코드 샘플](http://code.msdn.microsoft.com/site/search?query=event hub&f\[0\].Value=event hubs&f\[0\].Type=SearchText&ac=5)

.NET API 참조는 다음과 같습니다.

* [Service Bus 및 Event Hubs .NET API 참조](/dotnet/api)
* [이벤트 프로세서 호스트 API 참조](/dotnet/api)




<!--HONumber=Nov16_HO4-->


