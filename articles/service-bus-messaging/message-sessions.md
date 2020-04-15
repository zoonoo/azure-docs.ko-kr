---
title: Azure Service Bus 메시지 세션 | Microsoft Docs
description: 이 문서에서는 세션을 사용하여 관련 메시지의 무한시퀀스를 공동 및 정렬된 처리로 설정하는 방법을 설명합니다.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314051"
---
# <a name="message-sessions"></a>메시지 세션
Microsoft Azure Service Bus 세션을 사용하면 관련 메시지의 무제한 시퀀스를 공동으로 순서를 지정하여 처리할 수 있습니다. 세션은 선에서, 선외(FIFO) 및 요청 응답 패턴에서 사용할 수 있습니다. 이 문서에서는 Service Bus를 사용할 때 세션을 사용하여 이러한 패턴을 구현하는 방법을 보여 줍니다. 

## <a name="first-in-first-out-fifo-pattern"></a>선착지, 선착선(FIFO) 패턴
서비스 버스에서 FIFO 보증을 실현하려면 세션을 사용합니다. Service Bus는 메시지 간의 관계의 특성에 대해 규범적이지 않으며 메시지 시퀀스가 시작또는 끝나는 위치를 결정하기 위한 특정 모델을 정의하지도 않습니다.

> [!NOTE]
> 서비스 버스의 기본 계층은 세션을 지원하지 않습니다. 표준 및 프리미엄 계층은 세션을 지원합니다. 이러한 계층 간의 차이점은 [서비스 버스 가격](https://azure.microsoft.com/pricing/details/service-bus/)책정을 참조하십시오.

발신자는 메시지를 토픽이나 큐에 제출할 때 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 속성을 세션에 고유한 애플리케이션 정의 식별자로 설정하여 세션을 만들 수 있습니다. AMQP 1.0 프로토콜 수준에서 이 값은 * group-id * 속성에 매핑됩니다.

세션 인식 큐 또는 구독에서 세션의 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)가 하나 이상의 메시지가 있을 때 세션이 존재합니다. 세션이 존재하면 세션이 만료되거나 사라지는 시점에 대해 정의된 시간이나 API가 없습니다. 이론적으로 메시지는 오늘 세션에서 수신되고 1년 후 다음 메시지가 수신될 수 있으며 **SessionId**가 일치하면 세션은 Service Bus 측면에서 동일합니다.

하지만 일반적으로 애플리케이션에는 관련된 메시지 집합이 시작되고 끝나는 위치에 대한 명확한 개념이 있지만 서비스 버스는 특정 규칙을 설정하지 않습니다.

파일 전송 시퀀스를 서술하는 방법의 예에는 첫 번째 메시지의 **Label** 속성을 **start**로 설정하고 중간 메시지는 **content**로 설정하고 마지막 메시지는 **end**라고 설정하는 방법이 있습니다. 콘텐츠 메시지의 상대적인 위치는 **start** 메시지의 *SequenceNumber*에서 현재 메시지의 *SequenceNumber* 델타로 계산될 수 있습니다.

Service Bus의 세션 기능을 사용하면 C# 및 Java API의 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 형태로 특정 수신 작업이 가능합니다. 이 기능은 Azure Resource Manager를 통해 큐 또는 구독에 대한 [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 속성을 설정하거나 포털에서 플래그를 설정하여 사용하도록 설정합니다. 관련 API 작업을 사용하려면 전에 필요합니다.

포털에서 다음 확인란을 사용하여 플래그를 지정합니다.

![][2]

> [!NOTE]
> 큐 또는 구독에서 세션을 사용하도록 설정하면 클라이언트 응용 프로그램은 더 이상 일반 메시지를 보내거나 받을 수 ***없습니다.*** 모든 메시지는 세션의 일부로 전송되어야 하며(세션 ID를 설정하여) 세션을 수신하여 수신해야 합니다.

세션용 API는 큐 및 구독 클라이언트에 있습니다. 세션 및 메시지를 수신하는 시기를 제어하는 명령형 모델과 수신 루프 관리의 복잡성을 숨기는 *OnMessage와*유사한 처리기 기반 모델이 있습니다.

### <a name="session-features"></a>세션 기능

세션은 순차적 전달을 보존하고 보장하면서 인터리브된 메시지 스트림에 대한 동시 역멀티플렉싱을 제공합니다.

![][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 수신기는 세션을 허용하는 클라이언트에서 생성됩니다. 클라이언트는 C#의 [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) 또는 [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)를 호출합니다. 반응성 콜백 모델에서는 세션 처리기를 등록합니다.

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 개체가 수락되고 클라이언트가 보유하는 동안 해당 클라이언트는 큐 또는 구독에 있는 해당 세션의 [SessionId를](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) 사용하는 모든 메시지와 세션이 유지되는 동안 여전히 도착하는 **SessionId가** 있는 모든 메시지에 대해 단독 잠금을 보유합니다.

**닫기** 또는 **CloseAsync** 호출 될 때 잠금 해제 또는 응용 프로그램 닫기 작업을 수행할 수 없는 경우 잠금 만료 됩니다. 세션 잠금은 파일의 단독 잠금처럼 처리되어야 하며, 이는 응용 프로그램이 더 이상 필요하지 않거나 추가 메시지를 기대하지 않는 즉시 세션을 닫아야 한다는 것을 의미합니다.

다수의 동시 수신자가 큐에서 풀링되면 특정 세션에 속하는 메시지는 해당 세션에 대한 잠금을 보유하고 있는 특정 수신자에게 전달됩니다. 이 작업을 사용하면 한 큐 또는 구독의 인터리브 메시지 스트림이 다른 수신기에 깔끔하게 다중 화되고 이러한 수신기는 서비스 버스 내부에서 서비스 측에서 서비스 측에서 발생하기 때문에 다른 클라이언트 컴퓨터에서도 사용할 수 있습니다.

앞의 그림에는 3개의 동시 세션 수신기가 나와 있습니다. `SessionId` = 4인 한 세션에 활성, 소유 클라이언트가 없습니다. 즉, 이 특정 세션에서 전달된 메시지가 없습니다. 세션은 하위 큐와 같이 여러 가지 방법으로 작동합니다.

세션 수신자가 보유한 세션 잠금은 *peek-lock* 정산 모드에서 사용되는 메시지 잠금에 대한 우산입니다. 수신자가 동시에 "진행 중"이라는 두 개의 메시지를 가질 수는 없지만 메시지는 순서대로 처리되어야 합니다. 이전 메시지가 완료되거나 배달 못 한 편지로 처리된 경우에만 새 메시지를 얻을 수 있습니다. 메시지를 버리면 다음 수신 작업으로 동일한 메시지가 다시 제공됩니다.

### <a name="message-session-state"></a>메시지 세션 상태

워크플로가 대규모 고가용성 클라우드 시스템에서 처리되는 경우 특정 세션과 연결된 워크플로 핸들러는 예기치 않은 오류로부터 복구할 수 있어야 하며 작업이 시작된 다른 프로세스 또는 컴퓨터에서 부분적으로 완료된 작업을 다시 시작할 수 있어야 합니다.

세션 상태 기능을 통해 broker 내부의 메시지 세션에 대한 애플리케이션 정의 주석을 사용할 수 있기 때문에 세션을 새 프로세서로 가져올 때 해당 세션에 대해 기록된 처리 상태를 즉시 사용할 수 있습니다.

Service Bus 측면에서 메시지 세션 상태는 한 개 메시지 크기의 데이터(Service Bus Standard의 경우 256KB 및 Service Bus Premium의 경우 1MB)를 보유할 수 있는 불투명한 이진 개체입니다. 세션과 관련된 처리 상태는 세션 상태 내에 유지되거나 이러한 정보를 보유하는 스토리지 위치 또는 데이터베이스 레코드를 세션 상태가 가리킬 수 있습니다.

세션 상태를 관리하는 API, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 및 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)는 C# 및 Java API의 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 개체에서 찾을 수 있습니다. 이전에 세션 상태가 설정되지 않은 세션은 **GetState**에 대한 **null** 참조를 반환합니다. 이전에 설정된 세션 상태를 지우는 것은 [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_)을 통해 수행됩니다.

세션 상태는 세션의 모든 메시지가 사용되더라도 정리되지 않는 **한(null**반환)으로 유지됩니다.

큐 또는 구독의 모든 기존 세션은 Java API의 **SessionBrowser** 메서드와 .NET Framework 클라이언트의 [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) 및 [구독 클라이언트에서](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) [GetMessageSessions를](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) 사용하여 열거할 수 있습니다.

큐 또는 구독에 보관된 세션 상태는 해당 엔터티의 스토리지 할당량에 포함됩니다. 애플리케이션이 세션을 끝내면 외부 관리 비용을 피하기 위해 애플리케이션이 보유 상태를 정리하는 것이 좋습니다.

### <a name="impact-of-delivery-count"></a>배달 수의 영향

세션 컨텍스트에서 메시지당 배달 수의 정의는 세션이 없는 경우의 정의와 약간 다릅니다. 다음은 배달 수가 증가하는 시기를 요약한 표입니다.

| 시나리오 | 메시지의 배달 수가 증가합니까? |
|----------|---------------------------------------------|
| 세션이 수락되었지만 시간 으로 인해 세션 잠금이 만료됩니다. | 예 |
| 세션이 수락되고 세션 내의 메시지가 완료되지 않고(잠긴 경우에도) 세션이 닫힙됩니다. | 예 |
| 세션이 수락되고 메시지가 완료된 다음 세션이 명시적으로 닫힙됩니다. | 해당/A(표준 흐름입니다. 여기에서 메시지는 세션에서 제거됩니다) |

## <a name="request-response-pattern"></a>요청 응답 패턴
[요청 회신 패턴은](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) 발신자 응용 프로그램이 요청을 보낼 수 있도록 하는 잘 설정된 통합 패턴이며 수신자가 보낸 사람에게 응답을 올바르게 보낼 수 있는 방법을 제공합니다. 이 패턴은 일반적으로 응용 프로그램이 응답을 보내려면 수명이 짧은 큐 또는 토픽이 필요합니다. 이 시나리오에서 세션은 비슷한 의미 체계를 가진 간단한 대체 솔루션을 제공합니다. 

여러 응용 프로그램이 보낸 사람의 응용 프로그램을 고유하게 식별하도록 설정된 특정 헤더 매개 변수를 사용하여 요청을 단일 요청 큐로 보낼 수 있습니다. 수신자 응용 프로그램은 큐에 들어오는 요청을 처리하고 세션 사용 큐에 대한 회신을 보내 보낸 사람이 요청 메시지에 보낸 고유 식별자로 세션 ID를 설정할 수 있습니다. 요청을 보낸 응용 프로그램은 특정 세션 ID로 메시지를 수신하고 회신을 올바르게 처리할 수 있습니다.

> [!NOTE]
> 초기 요청을 보내는 응용 프로그램은 세션 ID에 `SessionClient.AcceptMessageSession(SessionID)` 대해 알고 응답을 기대하는 세션을 잠그는 데 사용해야 합니다. 응용 프로그램의 인스턴스를 세션 ID로 고유하게 식별하는 GUID를 사용하는 것이 좋습니다. 특정 수신기에서 응답을 `AcceptMessageSession(timeout)` 잠그고 처리할 수 있도록 세션 처리기 나 큐에 없어야합니다.

## <a name="next-steps"></a>다음 단계

- 세션 인식 메시지를 처리하기 위해 .NET Framework 클라이언트를 사용하는 예제는 [Microsoft.Azure.ServiceBus 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) 또는 [Microsoft.ServiceBus.Messaging 샘플을](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) 참조하십시오. 

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
