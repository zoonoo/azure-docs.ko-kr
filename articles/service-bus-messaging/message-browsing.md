---
title: Azure Service Bus-메시지 찾아보기
description: Service Bus 메시지 찾아보기 및 피킹 Azure Service Bus 클라이언트에서 큐 또는 구독에 있는 모든 메시지를 열거할 수 있습니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 6e50fc737f6c81c07854ff07d8cc64061306749b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827442"
---
# <a name="message-browsing"></a>메시지 찾아보기

메시지 찾아보기 또는 피킹(Peeking)을 통해 Service Bus 클라이언트는 큐 또는 구독에 있는 모든 메시지를 일반적으로 진단 및 디버깅 용도로 열거할 수 있습니다.

미리 보기(peek) 작업은 `Receive()` 또는 `OnMessage()` 루프를 사용하여 즉시 가져올 수 있는 메시지뿐만 아니라 큐 또는 구독 메시지 로그에 있는 모든 메시지를 반환합니다. 각 메시지의 `State` 속성은 메시지가 활성(수신 가능)인지, [지연되었는지](message-deferral.md) 또는[예약되었는지](message-sequencing.md)를 알려줍니다.

사용되고 만료된 메시지는 비동기 "가비지 수집" 실행에 의해 정리되며 정확히 메시지가 만료될 때 정리되는 것은 아닙니다. 따라서 `Peek`은 큐 또는 구독에서 수신 작업이 다음에 호출되면 이미 만료되어 삭제될 메시지나 배달 못한 메시지를 반환할 수 있습니다.

이런 내용은 지연된 메시지를 큐에서 복구하려고 시도할 때 특히 유의해야 합니다. [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) 인스턴트를 통과한 메시지는 어떠한 방법으로도(Peek에 의해 반환되는 경우에도) 일반 검색을 수행할 수 없습니다. Peek은 로그의 현재 상태를 반영하는 진단 도구이기 때문에 이러한 메시지를 반환하는 것은 의도적입니다.

또한 Peek는 잠긴 상태에서 다른 수신기가 현재 처리하고 있지만 아직 완료되지 않은 메시지를 반환합니다. 하지만 Peek는 연결이 끊긴 스냅샷을 반환하기 때문에 미리 본 메시지에서 메시지의 잠금 상태를 관찰할 수 없으며 애플리케이션이 해당 메시지를 읽으려고 하면 [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) 및 [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) 속성이 [InvalidOperationException](/dotnet/api/system.invalidoperationexception)을 throw합니다.

## <a name="peek-apis"></a>Peek API

[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) 및 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) 메서드는 모든 .net 및 Java 클라이언트 라이브러리와 모든 받는 사람 개체: **MessageReceiver**, **messagesession**에 존재 합니다. Peek은 모든 큐와 구독 및 각각의 배달 못한 메시지 큐에서 작동합니다.

반복적으로 호출되면 Peek 메서드는 큐 또는 구독 로그에 있는 모든 메시지를 시퀀스 번호 순서대로(가장 낮은 시퀀스 번호에서 가장 높은 시퀀스 번호로) 열거합니다. 다음은 메시지를 큐에 넣은 순서이며 최종적으로 메시지가 검색될 수 있는 순서는 아닙니다.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_)는 여러 메시지를 검색하여 열거형으로 반환합니다. 메시지가 없는 경우 열거 개체는 null이 아니고 비어 있습니다.

[SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) 와 함께 메서드의 오버 로드를 사용 하 여를 시작 하 고 매개 변수가 없는 메서드 오버 로드를 호출 하 여 추가로 열거할 수도 있습니다. **PeekBatch**는 동등하게 작동하지만 메시지 집합을 모두 한번에 검색합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
