<properties 
   pageTitle="서비스 버스 메시징 엔터티 자동 전달 | Microsoft Azure"
   description="큐 또는 구독을 같은 네임스페이스의 일부인 다른 큐 또는 항목에 연결하는 방법을 알아봅니다."
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
   ms.date="12/28/2015"
   ms.author="sethm" />

# 자동 전달을 사용한 서비스 버스 엔터티 연결

*자동 전달* 기능을 사용하면 큐나 동일한 서비스 네임스페이스의 일부인 다른 큐 또는 항목으로의 구독을 연결할 수 있습니다. 자동 전달을 사용하도록 설정하면 서비스 버스가 자동으로 첫번째 큐 또는 구독(원본)에 있는 메시지를 제거하고 두 번째 큐 또는 항목(대상)에 넣습니다. 대상 엔터티에 직접 메시지를 보내는 것은 여전히 가능합니다. 배달 못한 메시지 큐와 같은 하위 큐를 다른 큐나 항목으로 연결하는 것은 불가능합니다.

## 자동 전달 사용

자동 전달은 다음 예와 같이 [QueueDescription][] 또는 [SubscriptionDescription][] 개체를 원본으로 하여 [QueueDescription.ForwardTo][] 또는 [SubscriptionDescription.ForwardTo][] 속성을 설정하여 사용할 수 있습니다.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

원본 엔터티가 생성될 시점에 대상 엔터티가 존재해야 합니다. 대상 엔터티가 없는 경우 서비스 버스에 원본 엔터티를 만드는 요청을 보내면 예외가 반환됩니다.

개별 항목을 자동 전달하도록 확장할 수도 있습니다. 서비스 버스는 지정된 항목에서 구독의 수를 제한합니다. 두 번째 수준의 항목을 만들면 구독의 수를 늘릴 수 있습니다. 서비스 버스의 구독 수 제한에 묶여 있는 경우가 아니라도, 두 번째 수준의 항목을 추가하면 항목의 전체적인 처리량을 늘릴 수 있습니다.

![자동 전달 시나리오][0]

자동 전달 기능을 메시지 발신자와 수신자를 분리하는 데에도 사용할 수 있습니다. 주문 처리, 재고 관리 및 고객 관계 관리라는 세 모듈로 구성된 ERP 시스템을 예로 들어 보겠습니다. 이러한 각 모듈은 해당 항목의 큐에 저장되는 메시지를 생성합니다. Alice와 Bob은 고객에 관련된 모든 메시지에 관심이 있는 영업 담당자입니다. Alice와 Bob이 이러한 메시지를 받으려면 각 ERP 항목에 개인 큐와 구독을 만들어 모든 메시지가 자신의 큐로 자동으로 전달되도록 해야 합니다.

![자동 전달 시나리오][1]

Alice가 휴가를 가면 ERP 항목이 아닌 그녀의 개인 큐가 채워집니다. 이러한 경우 영업 담당자가 메시지를 확인하지 못하기 때문에 어떤 ERP 항목도 할당량에 도달하지 못합니다.

## 자동 전달 고려 사항

대상 엔터티에 많은 메시지가 누적되어 있어 할당량을 초과했거나 대상 엔터티가 사용하지 않도록 설정된 경우, 원본 엔터티는 대상에 공간이 생길 때까지(또는 엔터티가 다시 사용하도록 설정됨) 메시지를 배달 못한 큐에 추가합니다. 이러한 메시지는 배달 못한 큐에 계속 남아 있으므로, 배달 못한 큐에서 직접 수령하여 처리해야 합니다.

여러 구독이 있는 복합 항목을 받기 위해 개별 항목을 연결하는 경우, 첫 번째 수준의 항목에 있는 구독의 수를 조정하고 두 번째 수준의 항목에 많인 구독이 있도록 하는 것이 좋습니다. 예를 들어 첫 번째 수준의 항목에 20개의 구독이 있고, 각 구독이 두 번째 수준의 항목에 있는 200개 구독에 연결될 경우, 첫 번째 수준의 항목에 200개 구독이 있고 각 구독이 두 번째 항목에 있는 20개의 구독에 연결되는 것보다 처리량이 높습니다.

서비스 버스는 전달된 각 메시지당 하나의 작업을 요청합니다. 예를 들어, 20개의 구독이 있는 항목이 다른 큐나 항목으로 메시지를 자동 전달하도록 구성된 경우, 이 항목으로 메시지를 보내면 모든 첫 번째 수준의 구독이 메시지 복사본을 받을 경우 21개 작업이 요청됩니다.

다른 큐나 항목에 연결될 구독을 만들려면 구독을 만든 사람에게 원본과 대상 엔터티에 대한 **관리** 권한이 있어야 합니다. 원봉 항목에 메시지를 보낼 때는 원본 항목에 대한 **보내기** 권한만 있으면 됩니다.

## 다음 단계

자동 전달에 대한 자세한 내용은 다음 참조 항목을 참조하세요.

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

서비스 버스 성능 향상에 대한 자세한 내용은 [분할된 메시징 엔터티][]를 참조하세요.

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [분할된 메시징 엔터티]: service-bus-partitioning.md

<!---HONumber=AcomDC_0107_2016-->