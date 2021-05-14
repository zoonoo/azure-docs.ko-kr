---
title: Azure Event Hubs에서 이벤트에 사용자 지정 데이터 추가
description: 이 문서는 Azure Event Hubs에서 이벤트에 사용자 지정 데이터를 추가하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104893474"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Azure Event Hubs에서 이벤트에 사용자 지정 데이터 추가
이벤트는 주로 불투명한 바이트 집합으로 구성되기 때문에 해당 이벤트의 소비자가 처리 방법에 대한 의사 결정을 내리는 것이 어려울 수 있습니다. 이벤트 게시자가 소비자에게 더 나은 컨텍스트를 제공할 수 있도록 이벤트에는 키-값 쌍 집합의 형태로 된 사용자 지정 메타데이터가 포함될 수도 있습니다. 메타데이터를 포함하는 일반적인 시나리오 중 하나는 소비자가 형식을 이해하고 적절하게 역직렬화할 수 있도록 이벤트에 포함된 데이터 형식에 대한 힌트를 제공하는 것입니다.

> [!NOTE]
> 이 메타데이터는 Event Hubs 서비스에서 사용되거나 Event Hubs 서비스에 의미 있는 방식으로 사용되지 않으며, 이벤트 게시자와 소비자를 조정하는 경우에만 존재합니다. 

다음 섹션에서는 다양한 프로그래밍 언어로 이벤트에 사용자 지정 데이터를 추가하는 방법을 보여 줍니다. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

전체 코드 샘플은 [사용자 지정 메타데이터를 사용하여 이벤트 게시](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata)를 참조하세요.

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

전체 코드 샘플은 [사용자 지정 메타데이터를 사용하여 이벤트 게시](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java)를 참조하세요.


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

전체 코드 샘플은 [속성을 사용하여 이벤트 데이터 일괄 처리 전송](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)을 참조하세요.

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>다음 단계
다음 빠른 시작 및 샘플을 참조하세요. 

- 빠른 시작: [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- GitHub의 샘플: [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
