---
title: "Azure Service Bus 토픽 및 구독을 사용하는 응용 프로그램 만들기 | Microsoft Docs"
description: "Service Bus 토픽 및 구독에서 제공하는 게시-구독 기능 소개입니다."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a48fc9b0-b7b0-464e-8187-a517bf8b4eb4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 75dbbff8e666ed6c9b11fcfc1aa5be0eb759a0bb
ms.contentlocale: ko-kr
ms.lasthandoff: 04/13/2017

---
# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Service Bus 토픽 및 구독을 사용하는 응용 프로그램 만들기
Azure 서비스 버스는 신뢰할 수 있는 메시지 큐 및 지속형 게시/구독 메시징을 포함하여 클라우드 기반, 메시지 지향 미들웨어 기술 집합을 지원합니다. 이 문서는 [Service Bus 큐를 사용하는 응용 프로그램 만들기](service-bus-create-queues.md)에서 제공한 정보를 바탕으로 하며 Service Bus 토픽에서 제공하는 게시/구독 기능을 소개합니다.

## <a name="evolving-retail-scenario"></a>유통업 확장 시나리오
이 문서에서는 [Service Bus 큐를 사용하는 응용 프로그램 만들기](service-bus-create-queues.md)에서 사용한 유통업 시나리오를 계속 사용합니다. 개별 POS 터미널의 판매 데이터가 재고 관리 시스템으로 전달되어 재고 보충 시점을 판단하는 데 사용되는 상황을 다시 떠올려 보겠습니다. 각 POS 터미널은 **DataCollectionQueue** 큐에 메시지를 보내 판매 데이터를 보고합니다. 아래와 같이 인벤토리 관리 시스템에서 수신할 때까지 이 큐에 남아 있게 됩니다.

![Service Bus 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

이 시나리오를 확대하기 위해 새로운 요구 사항을 시스템에 추가했습니다. 즉 매장 소유자가 실시간으로 매장 운영을 모니터링하고자 합니다.

이 요구 사항에 부합하기 위해 시스템은 판매 데이터 스트림을 "해제"해야 합니다. POS 터미널이 재고 관리 시스템에 보낸 각각의 메시지는 이전처럼 유지하고자 하지만 매장 소유자에게 대시보드 보기를 제시할 때 사용하기 위해 각 메시지의 다른 사본이 필요합니다.

이와 같이 여러 대상이 각 메시지를 소비하는 모든 상황에서 Service Bus *토픽*을 사용할 수 있습니다. 토픽은 게시된 각 메시지를 토픽에 등록된 하나 이상의 구독에서 사용할 수 있게 하는 게시/구독 패턴을 제공합니다. 반면 큐에서는 각각의 메시지를 단일 소비자가 수신합니다.

메시지는 큐로 보내질 때와 같은 방식으로 토픽에 전달됩니다. 그러나 메시지가 토픽으로부터 직접 수신되는 것이 아니라 구독으로부터 수신됩니다. 토픽 구독은 토픽에 전송된 메시지의 사본을 받는 가상 큐로 간주할 수 있습니다. 메시지는 큐에서 수신하는 방식과 동일하게 구독에서 수신됩니다.

유통업 시나리오를 다시 생각해보면 큐를 토픽이 대체하며, 재고 관리 시스템 구성 요소에서 사용할 수 있는 구독을 추가할 수 있습니다. 이제 시스템은 다음과 같이 나타납니다.

![Service Bus 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

여기서의 구성은 이전 큐 기반 디자인과 동일하게 수행됩니다. 즉, 토픽에 보내는 메시지가 **재고** 구독으로 전달되어 **인벤토리 관리 시스템**에서 이를 소비하게 됩니다.

관리 대시보드를 지원하기 위해 다음과 같이 토픽에 두 번째 구독을 만듭니다.

![Service Bus 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

이 구성에서는 POS 터미널로부터의 각 메시지가 **대시보드** 및 **인벤토리** 구독에서 사용할 수 있게 됩니다.

## <a name="show-me-the-code"></a>코드 표시
[Service Bus 큐를 사용하는 응용 프로그램 만들기](service-bus-create-queues.md)에서는 Azure 계정에 등록하고 서비스 네임스페이스를 만드는 방법을 설명합니다. Service Bus 종속성을 참조하는 가장 쉬운 방법은 Service Bus [Nuget 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)를 설치하는 것입니다. 또한 Azure SDK의 일부로 Service Bus 라이브러리를 찾을 수 있습니다. 이 다운로드는 [Azure SDK 다운로드 페이지](https://azure.microsoft.com/downloads/)에서 제공합니다.

### <a name="create-the-topic-and-subscriptions"></a>토픽 및 구독 만들기
Service Bus 메시징 엔터티(큐 및 토픽 게시/구독)에 대한 관리 작업은 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) 클래스를 통해 수행됩니다. 특정 네임스페이스에 대한 **NamespaceManager** 인스턴스를 만들려면 적합한 자격 증명이 필요합니다. Service Bus는 [SAS(공유 액세스 서명)](service-bus-sas.md) 기반 보안 모델을 사용합니다. [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) 클래스는 잘 알려진 일부 토큰 공급자를 반환하는 기본 제공 팩터리 메서드를 사용하여 보안 토큰 공급자를 나타냅니다. [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) 메서드를 사용하여 SAS 자격 증명을 유지할 것입니다. 그러면 Service Bus 네임스페이스와 토큰 공급자의 기본 주소를 통해 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) 인스턴스가 생성됩니다.

[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) 클래스는 메시징 엔터티를 만들고 열거 및 삭제하기 위한 메서드를 제공합니다. 여기에 표시된 코드는 **NamespaceManager** 인스턴스를 생성하여 **DataCollectionTopic** 토픽을 만드는 데 사용하는 방법을 보여 줍니다.

```csharp
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";

TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);

namespaceManager.CreateTopic("DataCollectionTopic");
```

토픽의 속성을 설정하도록 하는 [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) 메서드의 오버로드가 있습니다. 예를 들어, 토픽에 전송된 메시지에 적용할 기본 TTL(Time-to-Live) 값을 설정할 수 있습니다. 다음으로 **인벤토리** 및 **대시보드** 구독을 추가합니다.

```csharp
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>토픽에 메시지 보내기
Service Bus 엔터티의 런타임 작업(예: 메시지 송수신)을 위해서는 응용 프로그램이 먼저[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#microsoft_servicebus_messaging_messagingfactory) 개체를 만들어야 합니다. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) 클래스와 유사하게 **MessagingFactory** 인스턴스는 서비스 네임스페이스와 토큰 공급자의 기본 주소로부터 생성됩니다.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Service Bus 토픽으로 보내고 받은 메시지는 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 클래스 인스턴스입니다. 이 클래스에는 표준 속성 집합(예: [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) 및 [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.timetolive?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), 응용 프로그램 속성을 저장하는 데 사용되는 사전 및 임의 응용 프로그램 데이터 본문이 있습니다. 응용 프로그램은 직렬화된 개체를 전달하여 본문을 설정할 수 있습니다(다음 예에서는 POS 터미널의 판매 데이터를 나타내는 **SalesData** 개체를 전달함). 개체 직렬화에는 [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx)를 사용합니다. 또는 [스트림](https://msdn.microsoft.com/library/system.io.stream.aspx) 개체를 제공할 수 있습니다.

```csharp
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

토픽에 메시지를 보내는 가장 쉬운 방법은 [CreateMessageSender](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageSender_System_String_)를 사용하여 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 인스턴스에서 직접 [MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender) 개체를 만드는 것입니다.

```csharp
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기
큐 사용과 유사하게, 구독에서 메시지를 수신하기 위해 [CreateMessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_)를 사용하여 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)로부터 직접 만든 [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) 개체를 사용할 수 있습니다. [Service Bus 큐를 사용하는 응용 프로그램 만들기](service-bus-create-queues.md)에서 설명한 것처럼 두 가지 수신 모드(**ReceiveAndDelete** 및 **PeekLock**) 중 하나를 사용할 수 있습니다.

구독에 대해 **MessageReceiver**를 만들 경우 *entityPath* 매개 변수는 `topicPath/subscriptions/subscriptionName` 형태입니다. 따라서 **DataCollectionTopic** 토픽의 **인벤토리** 구독에 대해 **MessageReceiver**를 만들려면 *entityPath*를 `DataCollectionTopic/subscriptions/Inventory`로 설정해야 합니다. 코드는 다음과 같이 나타납니다.

```csharp
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## <a name="subscription-filters"></a>구독 필터
지금까지 이 시나리오에서는 토픽에 보낸 모든 메시지를 모든 등록된 구독에서 사용할 수 있었습니다. 여기서 핵심은 "사용할 수 있다"는 것입니다. Service Bus 구독이 토픽으로 전송된 모든 메시지를 확인하는 동안 가상 구독 큐로 이러한 메시지의 하위 집합을 복사할 수 있습니다. 이 작업은 구독 *필터*를 사용하여 수행합니다. 구독을 만들 때 메시지 속성(예: [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label))과 응용 프로그램 속성(예: 이전 예제에서의 **StoreName**)에 모두 적용되는 SQL92 스타일 조건자 형태로 필터 식을 제공할 수 있습니다.

이를 설명하기 위해 시나리오를 확장해 보겠습니다. 즉 두 번째 매장을 유통업 시나리오에 추가합니다. 여전히 두 매장의 모든 POS 터미널에서 판매 데이터를 중앙 집중식 재고 관리 시스템에 전달해야 하지만 대시보드 도구를 사용하는 매장 관리자는 해당 매장의 성과에만 관심이 있습니다.   이를 위해 구독 필터링을 사용할 수 있습니다. POS 터미널이 메시지를 게시할 때는 메시지에 대해 **StoreName** 응용 프로그램 속성을 설정합니다. 두 매장이 **레드몬드**와 **시애틀**이라고 하면 레드몬드 매장의 POS 터미널은 판매 데이터 메시지에 **StoreName**을 **Redmond**라고 표시하며 시애틀 매장의 POS 터미널은 **StoreName**을 **Seattle**이라고 표시합니다. Redmond 매장의 매장 관리자는 자신의 POS 터미널의 데이터만 보고자 합니다. 시스템은 다음과 같이 나타납니다.

![Service-Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

이 전달을 설정하기 위해 다음과 같이 **대시보드** 구독을 만듭니다.

```csharp
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

이 [구독 필터](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)를 사용하면 **StoreName** 속성이 **Redmond**로 설정된 메시지만 **대시보드** 구독의 가상 큐에 복사됩니다. 이 밖에도 구독 필터링에는 여러 기능이 있습니다. 응용 프로그램은 구독의 가상 큐에 전달되는 메시지 속성을 수정할 수 있을 뿐 아니라 구독마다 여러 필터링 규칙을 적용할 수 있습니다.

## <a name="summary"></a>요약
[Service Bus 큐를 사용하는 응용 프로그램 만들기](service-bus-create-queues.md)에서 설명한 큐를 사용하는 모든 이유가 토픽에도 적용됩니다. 특히 다음 내용이 그렇습니다.

* 임시 분리 - 메시지 생산자와 소비자가 동시에 온라인 상태가 아니어도 됩니다.
* 부하 평준화 – 토픽을 통해 소비 응용 프로그램이 최대 부하 대신 평균 부하를 프로비저닝할 수 있으므로 부하의 최대치가 완화됩니다.
* 부하 분산 – 큐와 유사하게, 단일 구독에 대해 여러 소비자가 경쟁적으로 수신 대기할 수 있습니다. 각 메시지는 소비자 중 하나에게만 전달되므로 부하가 분산됩니다.
* 느슨한 결합 – 기존 끝점에 미치는 영향 없이 메시징 네트워크를 확장할 수 있습니다. 예를 들어, 토픽에 대한 구독을 추가하거나 필터를 변경하여 새 소비자를 허용합니다.

## <a name="next-steps"></a>다음 단계

POS 유통업 시나리오에서 큐를 사용하는 방법에 대한 정보는 [Service Bus 큐를 사용하는 응용 프로그램 만들기](service-bus-create-queues.md)를 참조하세요.


