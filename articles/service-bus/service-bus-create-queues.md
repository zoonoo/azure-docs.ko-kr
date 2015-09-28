<properties 
   pageTitle="서비스 버스 큐를 사용하는 응용 프로그램 만들기 | Microsoft Azure"
   description="서비스 버스를 사용하는 간단한 큐 기반 응용 프로그램을 작성하는 방법"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2015"
   ms.author="sethm" />

# 서비스 버스 큐를 사용하는 응용 프로그램 만들기

이 항목에서는 서비스 버스 큐를 설명하고 서비스 버스를 사용하는 간단한 큐 기반 응용 프로그램 작성 방법을 보여줍니다.

개별 POS 터미널의 판매 데이터가 재고 관리 시스템으로 전달되어 재고 보충 시점을 판단하는 데 사용되는 유통업 시나리오를 가정해 보겠습니다. 다음 그림에서처럼 이 솔루션에서는 서비스 버스 메시징을 터미널과 재고 관리 시스템 간 통신에 사용합니다.

![Service-Bus-Queues-Img1](./media/service-bus-create-queues/IC657161.gif)

각 POS 터미널은 **DataCollectionQueue**에 메시지를 보내 판매 데이터를 보고합니다. 이러한 메시지는 재고 관리 시스템에서 검색될 때까지 이 큐에 남아 있습니다. POS 터미널이 처리를 계속하기 위해 재고 관리 시스템의 응답을 기대할 필요가 없기 때문에 이 패턴을 종종 *비동기 메시징*이라고 합니다.

## 큐 작업 이유

이 응용 프로그램의 설정에 필요한 코드를 확인하기 전에, 이 시나리오에서 POS 터미널이 직접(동기로) 재고 관리 시스템에 이야기하는 대신 큐를 사용하는 것의 장점을 살펴보겠습니다.

### 임시 분리

비동기 메시징 패턴을 사용하면 생산자와 소비자가 동시에 온라인 상태일 필요가 없습니다. 소비하는 쪽에서 메시지를 수신할 준비가 될 때까지 메시징 인프라가 메시지를 안정적으로 저장합니다. 따라서 전체 시스템에 영향을 주지 않고 자발적으로(예: 유지 관리의 경우) 또는 구성 요소 크래시로 인해 분산 응용 프로그램 구성 요소의 연결을 끊을 수 있습니다. 또한 소비 응용 프로그램은 하루 중 특정 기간 동안만 온라인 상태로 전환해면 됩니다. 예를 들어 이 유통업 시나리오에서는 재고 관리 시스템이 영업일 종료 이후에만 온라인이 될 수 있습니다.

### 부하 평준화

많은 응용 프로그램에서 시스템 부하는 시간에 따라 다르지만 각 작업 단위에 필요한 처리 시간은 일반적으로 일정합니다. 큐를 사용한 메시지 생산자와 소비자 조정은 최대 부하가 아닌 평균 부하를 서비스하려면 소비 응용 프로그램(작업자)만 프로비전해야 함을 의미합니다. 수신 부하가 변경됨에 따라 큐의 깊이가 증가하고 축소됩니다. 따라서 응용 프로그램 부하를 제공하는 데 필요한 인프라의 크기와 관련하여 비용이 직접적으로 절약됩니다.

![Service-Bus-Queues-Img2](./media/service-bus-create-queues/IC657162.gif)

### 부하 분산

부하가 증가하면 작업자 큐에서 읽을 작업자 프로세스가 더 추가될 수 있습니다. 각 메시지는 하나의 작업자 프로세스를 통해서만 처리됩니다. 또한 이 가져오기 기반 부하 분산에서는 작업자 컴퓨터가 최대 속도로 메시지를 가져올 때 처리 능력이 다른 경우에도 작업자 컴퓨터의 최적 사용률을 허용합니다. 이 패턴을 종종 경쟁 소비자 패턴이라고 합니다.

![Service-Bus-Queues-Img3](./media/service-bus-create-queues/IC657163.gif)

### 느슨한 결합

메시지 생산자와 소비자 간을 중개하는 메시지 큐를 사용하면 구성 요소 간에 내재하는 느슨한 연결을 제공합니다. 생산자와 소비자가 서로를 인식하지 않기 때문에 생산자에 영향을 주지 않고 소비자를 업그레이드할 수 있습니다. 또한 메시징 토폴로지는 기존 끝점에 미치는 영향 없이 확대할 수 있습니다. 게시/구독에 대해 설명할 때 더 자세히 논의할 것입니다.

## 코드 표시

다음 섹션에서는 서비스 버스를 사용하여 이 응용 프로그램을 빌드하는 방법을 보여줍니다.

### 서비스 버스 계정 및 구독에 등록

서비스 버스 작업을 시작하려면 Azure 계정이 필요합니다. 아직 등록하지 않은 경우 [여기서](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF) 무료 평가판에 가입할 수 있습니다.

### 서비스 네임스페이스 만들기

구독이 있으면 새 네임스페이스를 만들 수 있습니다. 모든 서비스 버스 계정에서 새 네임 스페이스에 고유한 이름을 지정해야 합니다. 각각의 네임스페이스는 서비스 버스 엔터티 집합에 대한 컨테이너입니다. 자세한 내용은 [방법: 서비스 버스 네임스페이스 만들기 또는 수정](https://msdn.microsoft.com/library/azure/hh690931.aspx)을 참조하세요.

### NuGet 패키지 설치

서비스 버스 서비스 네임스페이스를 사용하려면 특히 Microsoft.ServiceBus.dll 등, 응용 프로그램이 서비스 버스 어셈블리를 참조해야 합니다. Microsoft Azure SDK의 일부로 이 어셈블리를 찾을 수 있으며 [Azure SDK 다운로드 페이지](http://azure.microsoft.com/downloads/)에서 다운로드가 제공됩니다. 그러나 서비스 버스 NuGet 패키지는 서비스 버스 API를 가져오고 모든 서비스 버스 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법입니다. NuGet 및 서비스 버스 패키지 사용에 대한 자세한 내용은 [NuGet 서비스 버스 패키지 사용](https://msdn.microsoft.com/library/dn741354.aspx)을 참조하십시오.

### 큐 만들기

서비스 버스 메시징 엔터티(큐 및 토픽 게시/구독)에 대한 관리 작업은 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 클래스를 통해 수행됩니다. 서비스 버스는 [SAS(공유 액세스 서명) 기반](service-bus-sas-overview.md) 보안 모델을 사용합니다. [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) 클래스는 잘 알려진 일부 토큰 공급자를 반환하는 기본 제공 팩터리 메서드를 사용하여 보안 토큰 공급자를 나타냅니다. [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) 메서드를 사용하여 SAS 자격 증명을 유지할 것입니다. 그러면 서비스 버스 네임스페이스와 토큰 공급자의 기본 주소를 통해 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 인스턴스가 생성됩니다.

[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 클래스는 메시징 엔터티를 만들고 열거 및 삭제하기 위한 메서드를 제공합니다. 여기에 표시된 코드는 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 인스턴스가 생성되어 **DataCollectionQueue** 큐를 만드는 데 사용되는 방법을 보여줍니다.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

큐 속성의 튜닝을 구현하는 [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) 메서드의 오버로드가 있습니다. 예를 들어, 큐로 전송된 메시지에 적용할 기본 TTL(Time-to-Live) 값을 설정할 수 있습니다.

### 큐에 메시지 보내기

서비스 버스 엔터티의 런타임 작업(예: 메시지 송수신)을 위해서는 응용 프로그램이 먼저[MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 개체를 만들어야 합니다. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 클래스와 유사하게 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 인스턴스는 서비스 네임스페이스와 토큰 공급자의 기본 주소로부터 생성됩니다.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

서비스 버스 큐로 보내고 받은 메시지는 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 클래스 인스턴스입니다. 이 클래스에는 표준 속성 집합(예: [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 및 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), 응용 프로그램 속성을 저장하는 데 사용되는 사전 및 임의 응용 프로그램 데이터 본문이 있습니다. 응용 프로그램은 직렬화된 개체를 전달하여 본문을 설정할 수 있습니다(다음 예에서는 POS 터미널의 판매 데이터를 나타내는 **SalesData** 개체를 전달함). 개체 직렬화에는 [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx)를 사용합니다. 또는 [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) 개체를 제공할 수 있습니다.

특정 큐, 여기서는 **DataCollectionQueue**에 메시지를 보내는 가장 쉬운 방법은 [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx)를 사용하여 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 인스턴스에서 직접 [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) 개체를 만드는 것입니다.

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### 큐에서 메시지 받기

큐에서 메시지를 수신하는 가장 쉬운 방법은 [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx)를 사용하여 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)로부터 직접 만들 수 있는 [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx)를 사용하는 것입니다. 메시지 수신기는 **ReceiveAndDelete** 및 **PeekLock**의 두 가지 모드로 작동할 수 있습니다. [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)는 메시지 수신기가 만들어졌을 때 [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) 호출에 대한 매개 변수 형태로 설정됩니다.


**ReceiveAndDelete** 모드를 사용할 경우 수신은 1단계 작업으로, 서비스 버스가 요청을 받으면 메시지를 이용되는 것으로 표시하고 응용 프로그램에 반환합니다. **ReceiveAndDelete** 모드는 가장 단순한 모델이며, 응용 프로그램이 장애 상황에서 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 서비스 버스가 메시지를 이용되는 것으로 표시했기 때문에 응용 프로그램이 다시 시작되고 메시지 이용을 다시 시작할 때 크래시 전에 이용된 메시지는 누락됩니다.

**PeekLock** 모드에서는 수신이 2단계 작업이므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지에 대해 [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. 서비스 버스가 [완료](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 호출을 확인하면 메시지를 이용한 것으로 표시하고 큐에서 제거합니다.

다른 두 결과 가능합니다. 첫 번째는 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 [중단](https://msdn.microsoft.com/library/azure/hh181837.aspx)을 호출할 수 있습니다([완료](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 대신). 그러면 서비스 버스에서 메시지의 잠금을 해제하므로 동일한 소비나 다른 경쟁적 소비자에게서 메시지를 다시 받을 수 있습니다. 두 번째는 잠금과 연결된 시간 제한으로, 응용 프로그램에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 종종 *한 번 이상* 처리라고 합니다. 즉, 각 메시지가 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램이 중복 항목을 탐지하기 위한 추가적인 논리가 필요합니다. 이 작업은 메시지의 [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 속성을 기반으로 수행할 수 있습니다. 이 속성의 값은 전체 배달 시도에서 일관되게 유지됩니다. 이를 *정확히 한 번* 처리라고 합니다.

여기에 표시된 코드는 **PeekLock**를 사용하여 메시지를 받아 처리하며, [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 값이 명시적으로 제공되지 않은 경우의 기본값입니다.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
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

### 큐 클라이언트 사용

이 섹션 앞 부분의 예제에서는 [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) 및 [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) 개체를 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)에서 직접 만들어 각각 큐로부터 메시지를 보내고 받습니다. 또 다른 방법은 세션 등의 다른 고급 기능과 함께 송수신 작업을 지원하는 [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) 클래스를 사용하는 것입니다.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## 다음 단계

이제 큐의 기본 사항을 학습했으므로 서비스 버스 조정된 메시징의 게시/구독 기능을 사용하여 이 논의를 계속하려면 [서비스 버스 토픽 및 구독을 사용하는 응용 프로그램 만들기](service-bus-create-topics-subscriptions.md)를 참조하세요.

<!---HONumber=Sept15_HO3-->