<properties 
   pageTitle="분할된 메시징 엔터티 | Microsoft Azure"
   description="여러 메시지 broker를 사용하여 메시징 엔터티를 분할하는 방법을 설명합니다."
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
   ms.date="09/18/2015"
   ms.author="sethm" />

# 분할된 메시징 엔터티

Azure 서비스 버스에서는 여러 메시지 broker가 메시지를 처리하고 여러 메시징 저장소가 메시지를 저장하도록 합니다. 일반적인 큐 또는 항목은 단일 메시지 broker에서 처리되며 하나의 메시징 저장소에 저장됩니다. 또한 서비스 버스를 사용하면 큐 또는 항목이 여러 메시지 broker 및 메시징 저장소에 분할될 수 있습니다. 즉, 분할된 큐 또는 항목의 전체 처리량은 단일 메시지 broker 또는 메시징 저장소의 성능으로 제한되지 않습니다. 또한 메시징 스토어가 일시적으로 중단된 경우에도 분할된 큐 또는 항목을 계속 렌더링할 수 없습니다. 분할된 큐 및 항목은 트랜잭션 및 세션에 대한 지원 같은 모든 고급 서비스 버스 기능을 포함할 수 있습니다.

서비스 버스 내부 구조에 대한 자세한 내용은 [서비스 버스 아키텍처][] 항목을 참조하세요.

## 분할 큐 및 항목

분할된 큐 및 항목 각각은 여러 조각으로 구성됩니다. 각 조각은 다른 메시징 저장소에 저장되고 서로 다른 메시지 broker에서 처리됩니다. 분할된 큐 또는 항목으로 메시지를 보내면 서비스 버스는 메시지를 조각 중 하나에 할당합니다. 선택은 발신자가 지정할 수 있는 서비스 버스 또는 파티션 키에서 임의로 수행됩니다.

클라이언트가 분할된 큐 또는 분할된 항목의 구독에서 메시지를 받으려는 경우 서비스 버스는 메시지에 모든 조각을 쿼리한 다음 수신자에게 메시징 저장소 중 하나에서 반환되는 첫 번째 메시지를 반환합니다. 서비스 버스는 추가 수신 요청을 받을 때 다른 메시지를 캐시하고 반환합니다. 수신하는 클라이언트는 분할을 인식하지 못합니다. 분할된 큐 또는 항목의 클라이언트 연결 동작(예, 읽기, 완료, 연기, 효력 상실, 프리페치)은 일반적인 엔터티 동작과 동일합니다.

분할된 큐 또는 항목에 메시지를 보내거나 메시지를 받을 때 추가 비용이 없습니다.

## 분할 사용

Microsoft Azure 서비스 버스로 분할된 큐 및 항목을 사용하려면 Azure SDK 버전 2.2 이상을 사용하거나 HTTP 요청에서 `api-version=2013-10`를 지정합니다.

서비스 버스 큐 및 항목은 1, 2, 3, 4 또는 5GB 크기로 만들 수 있습니다(기본값은 1GB). 분할을 사용하는 경우 서비스 버스는 사용자가 지정한 각 GB마다 16개의 파티션을 만듭니다. 따라서 크기가 5GB인 큐를 만들 경우 16개의 파티션에서 최대 큐 크기는 (5 * 16) = 80GB가 됩니다. [Azure 포털][]에서 해당 항목을 보면 분할된 큐 또는 토픽의 최대 크기를 확인할 수 있습니다.

분할된 큐 또는 항목을 만들 수 있는 여러 방법이 있습니다. 응용 프로그램에서 큐 또는 항목을 만들 때 [QueueDescription.EnablePartitioning][] 또는 [TopicDescription.EnablePartitioning][] 속성을 각각 **true**로 설정하여 큐 또는 항목에 분할을 설정할 수 있습니다. 이러한 속성은 큐 또는 항목이 만들어진 시점에 설정되어야 합니다. 기존 큐 또는 항목에서 이러한 속성을 변경하는 것은 불가능합니다. 예:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

또는 Visual Studio나 [Azure 포털][]에서 분할된 큐나 항목을 만들 수 있습니다. 포털에서 새 큐 또는 항목을 만들 때 큐 또는 항목 창의 **구성** 탭에서 **분할 사용** 옵션을 확인합니다. Visual Studio에서 **새 큐** 또는 **새 항목** 대화 상자의 **분할 사용** 확인란을 클릭합니다.

## 파티션 키의 사용

메시지가 분할된 큐 또는 항목에 큐를 넣으면 서비스 버스는 파티션 키의 존재를 확인합니다. 파티션 키를 찾으면 해당 키를 기반으로 조각을 선택합니다. 파티션 키를 찾지 못하면 내부 알고리즘을 기반으로 조각을 선택합니다.

### 파티션 키 사용

세션 또는 트랜잭션과 같은 일부 시나리오에서는 특정 조각에 저장할 메시지가 필요합니다. 이러한 모든 시나리오에서는 파티션 키를 사용해야 합니다. 동일한 파티션 키를 사용하는 모든 메시지는 동일한 조각에 할당됩니다. 조각을 일시적으로 사용할 수 없는 경우 서비스 버스가 오류를 반환합니다.

시나리오에 따라 다양한 메시지 속성이 파티션 키로 사용됩니다.

**SessionId**: 메시지에 [BrokeredMessage.SessionId][] 속성 집합이 있으면 서비스 버스는 해당 속성을 파티션 키로 사용합니다. 이러한 방식으로 동일한 세션에 속한 모든 메시지가 동일한 메시지 broker에서 처리됩니다. 이를 사용하면 서비스 버스가 세션 상태의 일관성 뿐만 아니라 메시지 순서를 보장할 수 있습니다.

**PartitionKey**: 메시지에 [BrokeredMessage.PartitionKey][] 속성이 있지만 [BrokeredMessage.SessionId][] 속성 집합이 없으면 서비스 버스는 [PartitionKey][] 속성을 파티션 키로 사용합니다. 메시지에 [SessionId][] 및 [PartitionKey][] 속성 집합이 모두 있으면 속성이 모두 동일해야 합니다. [PartitionKey][] 속성이 [SessionId][] 속성이 아닌 다른 값으로 설정되면 서비스 버스는 **InvalidOperationException** 예외를 반환합니다. 발신자가 비 세션 인식 트랜잭션 메시지를 보내는 경우 [PartitionKey][] 속성을 사용해야 합니다. 파티션 키는 트랜잭션 내에서 전송되는 모든 메시지가 동일한 메시징 broker에서 처리되도록 합니다.

**MessageId**: 큐 또는 항목에 [QueueDescription.RequiresDuplicateDetection][] 속성이 **true**로 설정되고 [BrokeredMessage.SessionId][] 또는 [BrokeredMessage.PartitionKey][] 속성이 설정되지 않은 경우 [BrokeredMessage.MessageId][] 속성이 파티션 키로 제공됩니다. (보내는 응용 프로그램이 메시지 ID를 할당하지 않으면 Microsoft .NET 및 AMQP 라이브러리는 자동으로 메시지 ID를 할당함) 이 경우에 동일한 메시지의 모든 복사본이 동일한 메시지 broker에서 처리됩니다. 따라서 서비스 버스가 중복 메시지를 감지하고 제거할 수 있습니다. [QueueDescription.RequiresDuplicateDetection][] 속성이 **true**로 설정되지 않으면 서비스 버스는 [MessageId][] 속성을 파티션 키로 고려하지 않습니다.

### 파티션 키 사용하지 않음

파티션 키가 없으면 서비스 버스는 분할된 큐 또는 항목의 모든 조각에 라운드 로빈 방식으로 메시지를 분산합니다. 선택한 조각을 사용할 수 없으면 서비스 버스는 다른 조각에 메시지를 할당합니다. 이러한 방식으로 메시징 저장소를 일시적으로 사용할 수 없음에도 불구하고 보내기 작업은 성공합니다.

메시지를 다른 조각에 큐를 넣도록 서비스 버스에 충분한 시간을 주려면 메시지를 보내는 클라이언트에서 지정된 [MessagingFactorySettings.OperationTimeout][] 값은 15초 보다 커야 합니다. [OperationTimeout][] 속성을 기본값인 60초로 설정하는 것이 좋습니다.

파티션 키는 특정 조각에 메시지를 "고정"합니다. 이 조각이 보관된 메시징 저장소를 사용할 수 없으면 서비스 버스는 오류를 반환합니다. 파티션 키가 없으면 서비스 버스는 다른 조각을 선택할 수 있고 작업은 성공합니다. 그러므로 필요한 경우가 아니면 파티션 키를 제공하지 않는 것이 좋습니다.

## 고급 항목: 분할된 엔터티로 트랜잭션 사용

트랜잭션의 일부로 전송되는 메시지는 파티션 키를 지정해야 합니다. 다음 속성 중 하나일 수 있습니다. [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][], 또는 [BrokeredMessage.MessageId][]. 동일한 트랜잭션의 일부로 전송되는 모든 메시지는 동일한 파티션 키를 지정해야 합니다. 트랜잭션 내에서 파티션 키 없이 메시지를 보내려면 서비스 버스는 **InvalidOperationException** 예외를 반환합니다. 다른 파티션 키를 가진 동일한 트랜잭션 내에서 여러 메시지를 보내려면 서비스 버스는 **InvalidOperationException** 예외를 반환합니다. 예:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

파티션 키로 제공한 속성을 설정하면 서비스 버스는 특정 조각에 메시지를 고정합니다. 이 동작은 트랜잭션이 사용되는지 여부와 상관 없이 발생합니다. 필요하지 않은 경우 파티션 키를 지정하지 않는 것이 좋습니다.

## 분할된 엔터티로 세션 사용

메시지 세션 인식 항목 또는 큐에 트랜잭션 메시지를 보내려면 메세지에는 [BrokeredMessage.SessionId][] 속성 집합이 있어야 합니다. [BrokeredMessage.PartitionKey][] 속성이 지정되면 [SessionId][] 속성과 동일해야 합니다. 다른 경우 서비스 버스가 **InvalidOperationException** 예외를 반환합니다.

일반(분할되지 않은) 큐 또는 항목과 달리 단일 트랜잭션을 사용하여 다른 세션에 여러 메시지를 보낼 수 없습니다. 시도하는 경우 서비스 버스가 **InvalidOperationException **예외를 반환합니다. 예:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## 분할된 엔터티로 자동 메시지 전달

Azure 서비스 버스는 분할된 엔터티 간에 자동 메시지 전달을 지원합니다. 자동 메시지 전달을 사용하려면 원본 큐 또는 구독에 [QueueDescription.ForwardTo][] 속성을 설정합니다. 메시지가 파티션 키를 지정하는 경우([SessionId][], [PartitionKey][], 또는 [MessageId][]) 해당 파티션 키를 대상 엔터티에 사용합니다.

## 분할된 엔터티 제한 사항

현재 구현에서 서비스 버스에서는 분할된 큐 및 항목에 다음과 같은 제한 사항이 적용됩니다.

-   분할된 큐 및 항목은 SBMP 또는 HTTP/HTTPS 뿐아니라 AMQP을 통해 사용할 수 있습니다.

-   분할된 큐 및 항목은 단일 트랜잭션에서 다른 세션에 속한 보내는 메시지를 지원하지 않습니다.

-   서비스 버스는 현재 네임스페이스당 최대 100개의 분할된 큐 또는 항목을 허용합니다. 각 분할된 큐 또는 항목은 네임스페이스 당 10,000개의 엔터티를 할당량으로 계산합니다.

-   분할된 큐 및 항목은 Windows Server 버전 1.0 및 1.1용 서비스 버스에서 지원되지 않습니다.

## 다음 단계

서비스 버스 분할된 큐 및 항목에 대한 AMQP 1.0 지원 설명을 참조하여(출시 예정!) 메시징 엔터티 분할에 대해 자세히 알아봅니다.

  [서비스 버스 아키텍처]: service-bus-architecture.md
  [Azure 포털]: http://manage.windowsazure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-entities-amqp-overview.md

<!---HONumber=Sept15_HO4-->