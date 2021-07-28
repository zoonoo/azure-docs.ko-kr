---
title: Azure Service Bus 메시지 프리페치 | Microsoft Docs
description: Azure Service Bus 메시지를 프리페치하여 성능을 향상시킵니다. 애플리케이션에서 메시지를 요청하기 전에 로컬 검색에 메시지를 쉽게 사용할 수 있습니다.
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: 992fa9228d3cf8bafb3d2ea9110b515ff4b56498
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989093"
---
# <a name="prefetch-azure-service-bus-messages"></a>Azure Service Bus 메시지 프리페치
공식 Service Bus 클라이언트에 대해 *프리페치* 기능을 사용하도록 설정하면 지정된 최대 프리페치 수까지 애플리케이션이 처음에 요청한 것보다 많은 수의 메시지가 수신기에 제공됩니다. JavaScript 및 TypeScript 클라이언트에서는 이 기능이 아직 지원되지 않습니다.

메시지가 애플리케이션에 반환될 때 프리페치 버퍼를 채우기 위해 백그라운드에서 추가 메시지가 클라이언트에 제공됩니다.

## <a name="enabling-prefetch"></a>프리페치 사용
프리페치 기능을 사용하도록 설정하려면 큐 또는 구독 클라이언트의 프리페치 수를 0보다 큰 숫자로 설정합니다. 이 값을 0으로 설정하면 프리페치가 해제됩니다. 

# <a name="net"></a>[.NET](#tab/dotnet)
최신 Azure.Messaging.ServiceBus 라이브러리를 사용하는 경우 [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) 및 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount) 개체에서 프리페치 수 속성을 설정할 수 있습니다. 

Service Bus에 대해 이전의 .NET 클라이언트 라이브러리를 사용하는 경우(Microsoft.Azure.ServiceBus) [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount), [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) 또는 [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount)에서 프리페치 수 속성을 설정할 수 있습니다.
 
# <a name="java"></a>[Java](#tab/java)
최신 azure-messaging-servicebus 라이브러리를 사용하는 경우 [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) 및 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount) 개체에서 프리페치 수 속성을 설정할 수 있습니다. 

Service Bus에 대해 이전의 Java 클라이언트 라이브러리를 사용하는 경우(azure-servicebus) [MessageReceiver](/java/api/com.microsoft.azure.servicebus.imessagereceiver.setprefetchcount#com_microsoft_azure_servicebus_IMessageReceiver_setPrefetchCount_int_), [QueueClient](/java/api/com.microsoft.azure.servicebus.queueclient.setprefetchcount#com_microsoft_azure_servicebus_QueueClient_setPrefetchCount_int_) 또는 [SubscriptionClient](/java/api/com.microsoft.azure.servicebus.subscriptionclient.setprefetchcount#com_microsoft_azure_servicebus_SubscriptionClient_setPrefetchCount_int_)에 프리페치 수 속성을 설정할 수 있습니다.
 
# <a name="python"></a>[Python](#tab/python)

[azure.servicebus.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.servicebusreceiver) 또는 [azure.servicebus.aio.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.aio.servicebusreceiver)에서 **prefetch_count** 를 설정할 수 있습니다.

---

> [!NOTE]
> Java Script SDK는 **프리페치** 기능을 지원하지 않습니다. 

프리페치 버퍼에서 메시지를 사용할 수 있지만 이후의 수신 호출은 버퍼로부터 즉시 수행됩니다. 공간을 사용할 수 있으면 버퍼가 백그라운드에서 보충됩니다. 배달할 수 있는 메시지가 없으면 수신 작업은 버퍼를 비우고 예상대로 대기하거나 차단됩니다.

## <a name="why-is-prefetch-not-the-default-option"></a>프리페치가 기본 옵션이 아닌 이유는 무엇인가요?
프리페치는 애플리케이션이 메시지를 요청하기도 전에 로컬 검색에 사용할 수 있도록 메시지를 준비하여 메시지 흐름을 빠르게 합니다. 이러한 처리량 혜택은 애플리케이션 작성자가 명시적으로 수행해야 하는 절충의 결과입니다.

[수신 및 삭제](message-transfers-locks-settlement.md#receiveanddelete) 모드에서는 프리페치 버퍼로 제공되는 모든 메시지를 더 이상 큐에서 사용할 수 없습니다. 메시지는 애플리케이션에 수신될 때까지만 메모리 내 프리페치 버퍼에 유지됩니다. 메시지가 애플리케이션에 수신되기 전에 애플리케이션이 종료되면 해당 메시지를 복구할 수 없습니다(메시지가 손실됨).

[보기 및 잠금](message-transfers-locks-settlement.md#peeklock) 수신 모드에서는 프리페치 버퍼에 페치된 메시지가 버퍼에 잠금 상태로 제공됩니다. 이러한 메시지에는 잠금 표시를 위한 시간 제한 클록이 있습니다. 프리페치 버퍼가 크고 처리하는 데 너무 오래 걸려서 메시지가 프리페치 버퍼에 머무르는 동안 또는 심지어 애플리케이션이 메시지를 처리하는 동안에도 메시지 잠금이 만료되는 경우 애플리케이션이 처리할 이벤트에 혼동이 발생할 수 있습니다.

애플리케이션은 만료되었거나 만료가 임박한 잠금이 적용된 메시지를 획득할 수 있습니다. 이 경우 애플리케이션이 메시지를 처리할 수 있지만 잠금 만료로 인해 메시지를 완료할 수 없음을 발견할 수 있습니다. 애플리케이션은`LockedUntilUtc` 속성을 확인할 수 있습니다(브로커 및 로컬 컴퓨터 클록 간의 클록 오차 적용). 메시지 잠금이 만료되었으면 애플리케이션이 메시지를 무시하고 해당 메시지에 대해 API 호출을 수행하지 않아야 합니다. 메시지가 만료되지 않았지만 만료가 임박한 경우에는 잠금을 갱신하고 기본 잠금 기간을 연장할 수 있습니다.

잠금이 프리페치 버퍼에서 자동으로 만료되면 메시지는 중단된 것으로 처리되며 큐에서 다시 검색할 수 있습니다. 그러면 메시지가 프리페치 버퍼에 페치되고 마지막에 배치될 수 있습니다. 일반적으로 메시지 만료 동안 프리페치 버퍼를 사용할 수 없는 경우 메시지가 반복적으로 프리페치되지만 유용한(유효하게 잠금 처리) 상태로 전달되지 못하며 결과적으로 최대 배달 횟수가 초과되면 배달 못한 메시지 큐로 이동됩니다.

메시지 처리에 대해 높은 수준의 안정성이 필요하며 처리에 많은 작업과 시간이 소요될 경우 프리페치 기능을 신중하게 사용하거나 아예 사용하지 않는 것이 좋습니다.

높은 처리량이 필요하고 메시지 처리 비용이 일반적으로 저렴한 경우 프리페치 기능을 사용하면 처리량이 크게 개선됩니다.

큐 또는 구독에 구성된 최대 프리페치 수와 잠금 기간을 적절히 조정하여, 적어도 잠금 시간 제한이 프리페치 버퍼의 최대 크기+1개 메시지에 대해 예상되는 누적 메시지 처리 시간을 초과하도록 해야 합니다. 동시에 메시지가 실수로 삭제되었을 때 유지될 수 있는 최대 시간을 초과할 정도로 그리고 메시지를 다시 전달하기 전 잠금 만료를 기다려야 할 정도로 잠금 제한 시간을 길게 설정하지는 않아야 합니다.

## <a name="next-steps"></a>다음 단계

선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - **프리페치** 샘플을 참조하세요. 
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus) - **프리페치** 샘플을 참조하세요. 
