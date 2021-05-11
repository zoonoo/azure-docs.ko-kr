---
title: Azure Service Bus 메시지 검색
description: Service Bus 메시지 검색 및 피킹을 사용하여 Azure Service Bus 클라이언트에서 큐 또는 구독의 모든 메시지를 열거할 수 있습니다.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f4943685f03eccb1c3b8da079973cf083bdcc416
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090310"
---
# <a name="message-browsing"></a>메시지 찾아보기

메시지 검색 또는 피킹을 사용하여 Service Bus 클라이언트는 큐 또는 구독의 모든 메시지를 진단 및 디버깅 용도로 열거할 수 있습니다.

**큐** 에 대한 피킹 작업은 즉시 획득할 수 있는 메시지뿐 아니라 큐에 있는 모든 메시지를 반환합니다. **구독** 에 대한 피킹 작업은 구독 메시지 로그에 있는 예약된 메시지를 제외한 모든 메시지를 반환합니다. 

사용된 메시지 및 만료된 메시지는 비동기 "가비지 수집"을 실행하여 정리됩니다. 이 단계가 메시지 만료 직후에 반드시 발생하는 것은 아닙니다. 따라서 피킹 작업은 이미 만료된 메시지를 반환할 수 있습니다. 이런 메시지는 다음에 큐 또는 구독에서 수신 작업이 호출될 때 제거되거나 배달되지 못한 편지로 처리됩니다. 지연된 메시지를 큐에서 복구하려고 시도할 때 이런 동작에 유의해야 합니다. 만료된 메시지는 어떠한 방법으로도(Peek에 의해 반환되는 경우에도) 일반 검색을 수행할 수 없습니다. Peek은 로그의 현재 상태를 반영하는 진단 도구이기 때문에 해당 메시지를 반환하는 것은 의도적입니다.

또한, Peek은 잠겨서 현재 다른 수신기가 처리하는 메시지를 반환합니다. 그러나 Peek은 연결이 끊긴 스냅샷을 반환하기 때문에 피킹 메시지에서 메시지의 잠금 상태를 관찰할 수 없습니다.

## <a name="peek-apis"></a>Peek API
## <a name="azuremessagingservicebus"></a>[Azure.Messaging.ServiceBus](#tab/dotnet)
[PeekMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessageasync) 및 [PeekMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessagesasync) 메서드는 수신기 개체인 `ServiceBusReceiver`, `ServiceBusSessionReceiver`에 존재합니다. Peek은 큐와 구독, 각각의 배달 못한 메시지 큐에서 작동합니다.

반복적으로 호출되면 `PeekMessageAsync`는 큐 또는 구독 로그에 있는 모든 메시지를 순서대로(가장 낮은 시퀀스 번호에서 가장 높은 시퀀스 번호로) 열거합니다. 다음은 메시지를 큐에 넣는 순서이며 최종적으로 메시지가 검색되는 순서는 아닙니다.
PeekMessagesAsync는 여러 메시지를 검색하여 열거형으로 반환합니다. 메시지가 없는 경우 열거 개체는 null이 아니고 비어 있습니다.

[fromSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.eventposition.fromsequencenumber) 매개 변수를 시작할 SequenceNumber로 채운 다음, 추가 열거하는 매개 변수를 지정하지 않고 메서드를 다시 호출할 수도 있습니다. `PeekMessagesAsync`는 동등하게 작동하지만 메시지 세트를 모두 한 번에 검색합니다.


## <a name="microsoftazureservicebus"></a>[Microsoft.Azure.ServiceBus](#tab/dotnetold)
[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) 및 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) 메서드는 수신기 개체인 `MessageReceiver`, `MessageSession`에 존재합니다. Peek은 큐와 구독, 각각의 배달 못한 메시지 큐에서 작동합니다.

반복적으로 호출되면 `Peek`는 큐 또는 구독 로그에 있는 모든 메시지를 순서대로(가장 낮은 시퀀스 번호에서 가장 높은 시퀀스 번호로) 열거합니다. 다음은 메시지를 큐에 넣는 순서이며 최종적으로 메시지가 검색되는 순서는 아닙니다.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_)는 여러 메시지를 검색하여 열거형으로 반환합니다. 메시지가 없는 경우 열거 개체는 null이 아니고 비어 있습니다.

또한, 시작할 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber)로 메서드의 오버로드를 시드한 다음 매개 변수가 없는 메서드 오버로드를 호출하여 추가로 열거할 수도 있습니다. **PeekBatch** 는 동등하게 작동하지만 메시지 집합을 모두 한번에 검색합니다.


---

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
