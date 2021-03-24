---
title: Azure Event Hubs에서 이벤트에 사용자 지정 데이터 추가
description: 이 문서에서는 Azure Event Hubs에서 이벤트에 사용자 지정 데이터를 추가 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104893474"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Azure Event Hubs에서 이벤트에 사용자 지정 데이터 추가
이벤트는 주로 불투명 한 바이트 집합으로 구성 되기 때문에 이러한 이벤트의 소비자가 처리 방법에 대 한 의사 결정을 내리는 것이 어려울 수 있습니다. 이벤트 게시자가 소비자에 게 더 나은 컨텍스트를 제공할 수 있도록 이벤트에는 키-값 쌍 집합의 형태로 사용자 지정 메타 데이터가 포함 될 수도 있습니다. 메타 데이터를 포함 하는 일반적인 시나리오 중 하나는 이벤트에 포함 된 데이터 형식에 대 한 힌트를 제공 하 여 소비자가 형식을 이해 하 고 적절 하 게 deserialize 할 수 있도록 하는 것입니다.

> [!NOTE]
> 이 메타 데이터는에서 사용 되지 않으며 Event Hubs 서비스에서 의미 하는 방식으로 사용 되지 않습니다. 이벤트 게시자와 소비자를 조정 하는 경우에만 존재 합니다. 

다음 섹션에서는 다양 한 프로그래밍 언어로 이벤트에 사용자 지정 데이터를 추가 하는 방법을 보여 줍니다. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

전체 코드 샘플은 [사용자 지정 메타 데이터로 이벤트 게시](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata)를 참조 하세요.

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

전체 코드 샘플은 [사용자 지정 메타 데이터로 이벤트 게시](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java)를 참조 하세요.


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

전체 코드 샘플은 [속성을 사용 하 여 이벤트 데이터 일괄 처리 전송](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)을 참조 하세요.

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>다음 단계
다음 퀵 스타트 및 샘플을 참조 하세요. 

- 빠른 시작: [.net](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- GitHub의 샘플: [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
