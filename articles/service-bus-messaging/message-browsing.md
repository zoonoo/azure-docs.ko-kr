---
title: Azure Service Bus-메시지 찾아보기
description: Service Bus 메시지 찾아보기 및 피킹 Azure Service Bus 클라이언트에서 큐 또는 구독의 모든 메시지를 열거할 수 있습니다.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553644"
---
# <a name="message-browsing"></a>메시지 찾아보기

메시지 찾아보기 또는 보기를 사용 하 여 진단 및 디버깅을 위해 Service Bus 클라이언트가 큐 또는 구독의 모든 메시지를 열거할 수 있습니다.

큐에 대 한 피킹 (peeking) 작업은 또는 루프를 사용 하 여 즉시 가져올 수 있는 메시지 뿐만 아니라 큐에 있는 모든 메시지를 반환 합니다 `Receive()` `OnMessage()` . 각 메시지의 `State` 속성은 메시지가 활성(수신 가능)인지, [지연되었는지](message-deferral.md) 또는[예약되었는지](message-sequencing.md)를 알려줍니다. 구독에 대 한 피킹 (peeking) 작업은 구독 메시지 로그에 있는 예약 된 메시지를 제외한 모든 메시지를 반환 합니다. 

사용 되 고 만료 된 메시지는 비동기 "가비지 수집" 실행에 의해 정리 됩니다. 이 단계는 메시지가 만료 된 직후에 반드시 발생 하는 것은 아닙니다. 그 이유는가 `Peek` 이미 만료 된 메시지를 반환할 수 있기 때문입니다. 이러한 메시지는 다음에 큐 또는 구독에서 수신 작업이 호출 될 때 제거 되거나 배달 되지 않습니다. 큐에서 지연 된 메시지를 복구 하려고 할 때이 동작을 염두에 두어야 합니다. 만료 된 메시지는 Peek에서 반환 되는 경우에도 다른 방법으로 인해 더 이상 일반 검색에 적합 하지 않습니다. 피킹 (Peeking)은 로그의 현재 상태를 반영 하는 진단 도구 이므로 이러한 메시지를 반환 하는 것은 의도 된 것입니다.

또한 Peek는 다른 수신자가 현재 처리 중인 메시지를 반환 합니다. 그러나 Peek는 연결 되지 않은 스냅숏을 반환 하므로 메시지의 잠금 상태는 피킹 메시지에서 관찰할 수 없습니다. [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) 및 [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) 속성은 응용 프로그램에서 읽으려고 할 때 [InvalidOperationException](/dotnet/api/system.invalidoperationexception) 를 throw 합니다.

## <a name="peek-apis"></a>Peek API

[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) 및 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) 메서드는 .net 및 Java 클라이언트 라이브러리와 수신자 개체: **MessageReceiver** , **messagesession** 에 있습니다. 피킹 (peeking)은 큐, 구독 및 각각의 배달 못 한 편지 큐에서 작동 합니다.

반복적으로 호출 되 면 **피킹 (peeking** )은 큐 또는 구독 로그의 모든 메시지를 순서 대로 사용 가능한 가장 낮은 시퀀스 번호에서 가장 높은 순서로 열거 합니다. 메시지가 최종적으로 검색 될 수 있는 순서가 아니라 메시지가 큐에 배치 되는 순서입니다.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_)는 여러 메시지를 검색하여 열거형으로 반환합니다. 메시지가 없는 경우 열거 개체는 null이 아니고 비어 있습니다.

[SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) 와 함께 메서드의 오버 로드를 사용 하 여를 시작 하 고 매개 변수가 없는 메서드 오버 로드를 호출 하 여 추가로 열거할 수도 있습니다. **PeekBatch** 는 동등하게 작동하지만 메시지 집합을 모두 한번에 검색합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
