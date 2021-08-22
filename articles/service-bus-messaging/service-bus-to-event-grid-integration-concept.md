---
title: Azure Service Bus-Event Grid 통합 개요 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus 메시징이 Azure Event Grid와 통합되는 방법에 대해 설명합니다.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 999bfb9278f3c355e2b431c0fe3ca13648aa42c9
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413018"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Azure Service Bus-Event Grid 통합 개요
이제 Service Bus는 수신기가 없을 때 큐 또는 구독에 메시지가 있으면 Event Grid로 이벤트를 내보낼 수 있습니다. Service Bus 네임스페이스에 대한 Event Grid 구독을 만들고, 수신기를 시작하여 이러한 이벤트를 수신 대기하고 대응할 수 있습니다. 이 기능을 통해 사후 프로그래밍 모델에 Service Bus를 사용할 수 있습니다. 이 기능의 주요 시나리오는 메시지 볼륨이 적은 Service Bus 큐 또는 구독은 항상 메시지에 대한 수신기 폴링이 필요 없다는 것입니다. 

기능을 사용하려면 다음 항목이 필요합니다.

* Service Bus 큐가 하나 이상 있는 Service Bus 프리미엄 네임스페이스 또는 구독이 하나 이상 있는 Service Bus 토픽.
* Service Bus 네임스페이스에 대한 참가자 액세스. Azure Portal에서 Service Bus 네임스페이스로 이동한 다음, **액세스 제어(IAM)** 를 선택하고 **역할 할당** 탭을 선택합니다. 기여자에게 네임스페이스에 대한 액세스 권한이 있는지 확인합니다. 
* 또한 Service Bus 네임스페이스에 대한 Event Grid 구독이 필요합니다. 이 구독은 Event Grid에서 선택할 메시지가 있다는 알림을 수신합니다. 일반적으로 구독자는 Azure App Service의 Logic Apps 기능, Azure Functions 또는 웹앱에 연결한 웹 후크일 수 있습니다. 그런 다음, 구독자는 메시지를 처리합니다. 

![19][]

[!INCLUDE [event-grid-service-bus.md](./includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Service Bus 네임스페이스에 대한 Event Grid 구독
세 가지 방법으로 Service Bus 네임스페이스에 대한 Event Grid 구독을 만들 수 있습니다.

- Azure Portal Azure Portal을 통해 Azure Logic Apps 및 Azure Functions를 처리기로 사용하는 Service Bus 이벤트에 대한 Event Grid 구독을 만드는 방법을 알아보려면 다음 자습서를 참조하세요. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI. 다음 CLI 예제에서는 Service Bus 네임스페이스로 만든 [시스템 항목](../event-grid/system-topics.md)에 대한 Azure Functions 구독을 만드는 방법을 보여줍니다.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell을 사용하여 키 백업 파일 복원 예를 들면 다음과 같습니다.
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>이벤트를 보내는 빈도 및 이벤트 수는 얼마나 되나요?

네임스페이스에 큐 및 토픽 또는 구독이 여러 개 있는 경우 큐당 이벤트 하나 이상, 구독당 이벤트 하나 이상이 발생합니다. Service Bus 엔터티에 메시지가 없고 새 메시지가 도착하는 경우 해당 이벤트를 즉시 내보냅니다. 또는 Service Bus가 활성화된 수신기를 검색하지 않는 한 해당 이벤트를 매 2분마다 내보냅니다. 메시지 찾아보기가 이벤트를 중단하지 않습니다.

기본적으로 Service Bus는 네임스페이스의 모든 엔터티에 대한 이벤트를 내보냅니다. 특정 엔터티에 대한 이벤트만 발생시키려면 다음 섹션을 참조하세요.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>이벤트를 얻는 장소를 제한하려면 필터 사용

네임스페이스 내부의 특정 큐 또는 특정 구독에 대한 이벤트만 발생시키려는 경우 Event Grid에서 제공하는 *시작 문자* 또는 *끝 문자* 필터를 사용하면 됩니다. 일부 인터페이스에서는 해당 필터를 *사전* 및 *접미사* 필터라고 합니다. 전부는 아니지만 여러 큐 및 구독에 대한 이벤트를 발생시키려는 경우 여러 개의 Event Grid 구독을 만들고 각각에 필터를 제공하면 됩니다.

## <a name="next-steps"></a>다음 단계
다음 자습서를 참조하세요. 
- [Event Grid를 통해 받은 Service Bus 메시지를 처리하는 Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Event Grid를 통해 받은 Service Bus 메시지를 처리하는 Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
