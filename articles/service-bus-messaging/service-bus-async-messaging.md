---
title: Service Bus 비동기 메시징 | Microsoft Docs
description: Azure Service Bus 비동기 메시지에 대한 설명입니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531109"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>비동기 메시징 패턴 및 고가용성

비동기 메시징은 다양한 방식으로 구현할 수 있습니다. 큐, 토픽 및 구독으로 Azure Service Bus는 저장소 및 전달 메커니즘을 통해 비동기를 지원합니다. 정상(동기) 작업에서 큐 및 토픽에 메시지를 보내고 큐 및 구독에서 메시지를 받습니다. 작성하는 애플리케이션은 항상 사용 가능한 이러한 엔터티에 따라 다릅니다. 다양한 상황으로 인해 엔터티 상태가 변경되면 대부분의 요구를 충족할 수 있는 축소된 기능 엔터티를 제공하는 방법이 필요합니다.

일반적으로 애플리케이션은 비동기 메시징 패턴을 사용하여 다양한 통신 시나리오를 사용합니다. 서비스가 실행 중이지 않을 경우에도 클라이언트가 서비스로 메시지를 보낼 수 있는 애플리케이션을 빌드할 수 있습니다. 통신의 버스트를 경험한 애플리케이션의 경우 큐는 통신을 버퍼링할 장소를 제공하여 부하를 일정하게 할 수 있습니다. 마지막으로 여러 컴퓨터 간에 메시지를 배포하는 간단하면서도 효율적인 부하 분산 장치를 얻을 수 있습니다.

이러한 엔터티의 가용성을 유지 관리하기 위해 이러한 엔터티가 지속 가능한 메시징 시스템에 사용 불가하다고 나타날 수 있는 다양한 다른 방법을 고려합니다. 일반적으로 말해서 다음의 다른 방법으로 작성한 애플리케이션에서 사용할 수 없게 되는 엔터티를 봅니다.

* 메시지를 보낼 수 없습니다.
* 메시지를 받을 수 없습니다.
* 엔터티를 관리할 수 없습니다(엔터티 만들기, 검색, 업데이트 또는 삭제).
* 서비스에 연결할 수 없습니다.

이러한 각 오류의 경우 다른 오류 모드는 애플리케이션이 기능이 축소된 수준에서 계속 작업을 수행할 수 있도록 존재합니다. 예를 들어 메시지를 보낼 수 있지만 받지 않는 시스템은 고객의 주문을 받을 수 있지만 이러한 주문을 처리할 수 없습니다. 이 항목에서는 발생할 수 있는 잠재적인 문제 및 이러한 문제를 줄이는 방법을 설명합니다. Service Bus는 선택해야 하는 다양한 완화 방법을 도입하고 또한 이 항목도 이러한 옵트인 완화 방법의 사용을 제어하는 규칙을 설명합니다.

## <a name="reliability-in-service-bus"></a>Service Bus의 안정성
메시지와 엔터티 문제를 처리하는 여러 방법이 있고 이러한 완화 방법을 적절하게 사용하도록 관리하는 지침이 있습니다. 지침을 이해하려면 먼저 Service Bus에서 무엇이 실패할 수 있는지 이해해야 합니다. Azure 시스템의 설계로 인해 이러한 문제는 모두 수명이 짧은 경향이 있습니다. 높은 수준에서 사용할 수 없는 다른 원인은 다음과 같이 나타납니다.

* Service Bus가 의존하는 외부 시스템에서 제한합니다. 제한은 계산 및 스토리지 리소스와의 상호작용에서 발생합니다.
* Service Bus가 의존하는 시스템에서 발생하는 문제입니다. 예를 들어 저장소의 특정 부분에 문제가 발생할 수 있습니다.
* 단일 하위 시스템에서 Service Bus의 오류입니다. 이 상황에서 계산 노드는 일관성이 없는 상태일 수 있으며 자체를 다시 시작해야 하며 이는 부하를 다른 노드로 분산하는 모든 엔터티를 발생시킵니다. 단기간 동안 차례로 메시지 처리 속도가 느려질 수 있습니다.
* Azure 데이터 센터 내에서 Service Bus의 오류입니다. 몇 분 또는 몇 시간 동안 시스템에 연결할 수 없는 "치명적 오류"입니다.

> [!NOTE]
> 용어 **스토리지**는 Azure Storage 및 SQL Azure를 의미할 수 있습니다.
> 
> 

Service Bus는 이런 문제에 대한 다양한 완화 방법을 포함합니다. 다음 섹션에서는 각 문제 및 해당 완화 방법을 설명합니다.

### <a name="throttling"></a>제한
Service Bus로 제한을 사용하면 공동으로 메시지 속도를 관리할 수 있습니다. 각 개별 Service Bus 노드가 여러 엔터티가 있습니다. 이러한 각 엔터티는 CPU, 메모리, 저장소 및 기타 측면에서 시스템에 요청을 만듭니다. 패싯이 정의된 임계값을 초과하는 사용을 감지하면 Service Bus는 지정된 요청을 거부할 수 있습니다. 호출자는 [ServerBusyException][ServerBusyException]을 수신하고 10 초 후에 다시 시도합니다.

완화 방법으로 코드는 오류를 읽고 적어도 10초 동안 메시지의 다시 시도를 중단해야 합니다. 오류는 고객 애플리케이션의 조각에 발생할 수 있으므로 각 조각이 재시도 논리를 독립적으로 실행한다고 예상됩니다. 코드는 큐 또는 토픽에서 분할을 사용하여 제한될 가능성을 줄일 수 있습니다.

### <a name="issue-for-an-azure-dependency"></a>Azure 종속성에서 발생하는 문제
Azure 내의 다른 구성 요소에는 서비스 문제가 있는 경우도 있습니다. 예를 들어 Service Bus가 사용하는 시스템이 업그레이드되는 경우 해당 시스템은 일시적으로 기능이 축소될 수 있습니다. 이러한 유형의 문제를 해결하려면 Service Bus가 정기적으로 완화 방법을 조사하고 구현합니다. 이러한 완화의 부작용도 물론 나타납니다. 예를 들어 저장소에서 발생하는 일시적인 문제를 처리하려면 Service Bus가 일관되 게 작동하도록 메시지 보내기 작업을 허용하는 시스템을 구현합니다. 완화 방법의 특성 상 보낸 메시지는 영향을 받는 큐 또는 구독에 표시되고 수신 작업을 준비하는 데 최대 15분이 걸릴 수 있습니다. 일반적으로 엔터티에는 대부분 이 문제가 발생하지 않습니다. 그러나 Azure 내에서 Service Bus의 엔터티 수를 고려하면 이 완화 방법은 종종 Service Bus 고객의 작은 하위 집합에 필요합니다.

### <a name="service-bus-failure-on-a-single-subsystem"></a>단일 하위 시스템에서 Service Bus 오류
애플리케이션의 경우 Service Bus의 내부 구성 요소가 일치하지 않는 상황이 발생할 수 있습니다. Service Bus가 이를 감지하는 경우 애플리케이션에서 데이터를 수집하여 상황을 진단하기 위해 지원합니다. 데이터가 수집되면 애플리케이션은 일관 된 상태로 반환하기 위해 다시 시작됩니다. 이 프로세스는 매우 신속하게 발생하고 엔터티가 최대 몇 분 동안 사용 불가 상태로 나타날 수 있지만 일반적인 가동 중지 시간은 훨씬 짧습니다.

이러한 경우 클라이언트 애플리케이션은 [System.TimeoutException][System.TimeoutException] 또는 [MessagingException][MessagingException] 예외를 생성합니다. Service Bus는 자동화된 클라이언트 다시 시도 논리의 형태로 이 문제에 대한 완화 방법을 포함합니다. 다시 시도 기간이 끝나고 메시지가 전달되지 않으면 [중단 및 재해 처리][handling outages and disasters]에 대한 문서에 언급된 다른 기능을 사용하여 검색할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 Service Bus에서 비동기 메시징의 기본 사항을 알아보았으므로 [중단 및 재해 처리][handling outages and disasters]에 대한 자세한 내용을 읽습니다.

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
