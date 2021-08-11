---
title: Azure Service Bus - 메시지 지연
description: 이 문서에서는 Azure Service Bus 메시지 배달을 지연하는 방법을 설명합니다. 메시지는 큐나 구독에 남아 있지만 따로 분리됩니다.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71446f7989a235e79ca3ab208579edd0043dfeb2
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987257"
---
# <a name="message-deferral"></a>메시지 지연
큐 또는 구독 클라이언트가 처리하려는 메시지를 받았으나 특수한 상황으로 인해 처리가 현재 가능하지 않을 경우 메시지의 검색을 나중으로 "연기"하기 위한 옵션이 제공됩니다. 메시지는 큐나 구독에 남아 있지만 따로 분리됩니다.

> [!NOTE]
> 지연된 메시지는 [만료된 후](./service-bus-dead-letter-queues.md#time-to-live) 배달 못 한 편지 큐로 자동으로 이동되지 않습니다. 이 동작은 의도된 것입니다.

## <a name="sample-scenarios"></a>샘플 시나리오:
연기는 워크플로 처리 시나리오를 위해 특별히 만들어진 기능입니다. 워크플로 프레임워크에서는 특정 작업을 특정 순서로 처리해야 할 수 있습니다. 다른 메시지에 의해 알려지는 규정된 사전 작업이 완료될 때까지 수신된 일부 메시지의 처리를 연기해야 할 수도 있습니다.

간단한 참고 예제는 일치하는 구매 주문서가 매장에서 결제 처리 시스템으로 전파되기 전에 외부 결제 공급자의 결제 알림이 시스템에 표시되는 주문 처리 시퀀스입니다. 이 경우 결제 처리 시스템은 연결된 주문이 있을 때까지 결제 알림 처리를 연기할 수 있습니다. 여러 다른 원본의 메시지가 워크플로를 구동하는 랑데부 시나리오에서는 실제로 실시간 실행 순서는 올바르지만 결과를 반영하는 메시지의 순서가 맞지 않을 수 있습니다.

궁극적으로, 연기 기능은 처리를 연기해야 하는 메시지를 메시지 저장소에 안전하게 보관하면서, 메시지 순서를 도착 순서에서 처리할 수 있는 순서로 다시 정렬하는 데 도움이 됩니다.

해당 메시지를 처리하기 위한 특정 리소스를 일시적으로 사용할 수 없으나 메시지 처리를 즉시 일시 중단하면 안 되기 때문에 메시지를 처리할 수 없는 경우, 몇 분 동안 해당 메시지를 보류하는 방법은 몇 분 후에 게시할 [예약된 메시지](message-sequencing.md)의 시퀀스 번호를 기억해둔 후, 예약된 메시지가 도착할 때 연기된 메시지를 다시 검색하는 것입니다. 메시지 처리기가 모든 작업을 위해 데이터베이스에 의존하며 해당 데이터베이스를 일시적으로 사용할 수 없는 경우 연기를 사용하지 말고 데이터베이스를 다시 사용할 수 있게 될 때까지 메시지 수신을 일시 중단하는 것이 좋습니다. 

## <a name="retrieving-deferred-messages"></a>지연된 메시지 검색
연기된 메시지는 다른 모든 활성 메시지와 함께 기본 큐에 남아 있지만(하위 큐에 유지되는 배달 못 한 메시지와 다름), 더 이상 일반 수신 작업을 사용하여 수신될 수 없습니다. 연기된 메시지는 애플리케이션이 추적하지 못할 경우 [메시지 찾아보기](message-browsing.md)를 통해 검색할 수 있습니다.

지연된 메시지를 검색하기 위해 소유자는 메시지를 연기할 때 시퀀스 번호를 기억해야 합니다. 지연된 메시지의 시퀀스 번호를 알고 있는 모든 수신자는 시퀀스 번호를 매개 변수로 사용하는 수신 방법을 사용하여 나중에 메시지를 수신할 수 있습니다. 시퀀스 번호에 대한 자세한 내용은 [메시지 순서 지정 및 타임 스탬프](message-sequencing.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - **Settling Messages** 샘플을 참조하세요. 
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/) - **receive_deferred_message_queue.py** 를 참조하세요. 
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/) - **advanced/deferral.js** 샘플을 참조하세요. 
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/) - **advanced/deferral.js** 샘플을 참조하세요. 

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - **Deferral** 샘플을 참조하세요. 
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
