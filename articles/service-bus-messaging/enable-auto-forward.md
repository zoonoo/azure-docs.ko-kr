---
title: Azure Service Bus 큐 및 구독에 대해 자동 전달을 사용하도록 설정
description: 이 문서에서는 Azure Portal, PowerShell, CLI 및 프로그래밍 언어(C#, Java, Python 및 JavaScript)를 사용하여 큐 및 구독에 대해 자동 전달을 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: da5af50941263c54a08de27df43fc85b5bd75b4a
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671498"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Azure Service Bus 큐 및 구독에 대해 자동 전달을 사용하도록 설정
Service Bus 자동 전달 기능을 통해 동일한 네임스페이스의 일부인 다른 큐 또는 토픽에 큐 또는 구독을 연결할 수 있습니다. 자동 전달을 사용하도록 설정하면 Service Bus가 자동으로 첫 번째 큐 또는 구독(원본)에 있는 메시지를 제거하고 두 번째 큐 또는 토픽(대상)에 넣습니다. 대상 엔터티에 직접 메시지를 보내는 것은 여전히 가능합니다. 자세한 내용은 [자동 전달을 사용한 Service Bus 엔터티 연결](service-bus-auto-forwarding.md)을 참조하세요. 이 문서에서는 Service Bus 큐 및 구독에 대해 자동 전달을 사용하도록 설정하는 다양한 방법을 보여 줍니다. 

> [!IMPORTANT]
> Service Bus의 기본 계층은 자동 전달 기능을 지원하지 않습니다. 표준 및 프리미엄 계층은 이 기능을 지원합니다. 이러한 계층 간의 차이점은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal의 토픽에 대해 **큐** 또는 **구독** 을 만들 때 다음 예에서 보는 것처럼 **큐/토픽으로 메시지 전달** 을 선택합니다. 그런 다음 메시지를 큐 또는 토픽으로 전달할 것인지 지정합니다. 이 예제에서는 **큐** 옵션이 선택되고 동일한 네임스페이스에서 큐가 선택됩니다.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>자동 전달이 사용하도록 설정된 큐 만들기
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="큐를 만드는 시점에 자동 전달을 사용하도록 설정":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>자동 전달이 사용하도록 설정된 토픽에 대해 구독 만들기
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="구독을 만드는 시점에 자동 전달을 사용하도록 설정":::

### <a name="update-the-auto-forward-setting-for-an-existing-queue"></a>기존 큐에 대한 자동 전달 설정 업데이트
Service Bus 큐의 **개요** 페이지에서 **메시지 전달 대상** 설정의 현재 값을 선택합니다. 다음 예에서 현재 값은 **사용 안 함** 입니다. **큐/토픽에 메시지 전달** 창에서 메시지를 전달할 큐 또는 토픽을 선택할 수 있습니다. 

:::image type="content" source="./media/enable-auto-forward/queue-auto-forward.png" alt-text="기존 큐에 대해 자동 전달 사용":::

### <a name="update-the-auto-forward-setting-for-an-existing-subscription"></a>기존 구독에 대한 자동 전달 설정 업데이트
Service Bus 구독의 **개요** 페이지에서 **메시지 전달 대상** 설정의 현재 값을 선택합니다. 다음 예에서 현재 값은 **사용 안 함** 입니다. **큐/토픽에 메시지 전달** 창에서 메시지를 전달할 큐 또는 토픽을 선택할 수 있습니다. 

:::image type="content" source="./media/enable-auto-forward/subscription-auto-forward.png" alt-text="기존 구독에 대해 자동 전달 사용":::

## <a name="using-azure-cli"></a>Azure CLI 사용
**자동 전달이 사용하도록 설정된 큐를 만들려면** `--forward-to`를 메시지가 전달되기를 원하는 큐 또는 토픽의 이름으로 설정한 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 명령을 사용합니다. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

**기존 큐에 대한 자동 전달 설정을 업데이트** 하려면 `--forward-to`가 메시지를 전달하려는 큐 또는 토픽의 이름으로 설정된 [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) 명령을 사용합니다. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```


**자동 전달이 사용하도록 설정된 토픽에 대한 구독을 만들** 려면 `--forward-to`가 메시지를 전달하려는 큐 또는 토픽의 이름으로 설정된 [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) 명령을 사용합니다.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

**토픽에 구독에 대한 자동 전달 설정을 업데이트** 하려면 `--forward-to`가 메시지를 전달하려는 큐 또는 토픽의 이름으로 설정된 [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) 명령을 사용합니다.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
**자동 전달이 사용하도록 설정된 큐를 만들려면** `-ForwardTo`를 메시지가 전달되기를 원하는 큐 또는 토픽의 이름으로 설정한 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

**기존 큐에 대한 자동 전달 설정을 업데이트** 하려면 다음 예제와 같이 [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) 명령을 사용합니다.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.ForwardTo='myqueue2'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

**자동 전달이 사용하도록 설정된 토픽에 대해 구독을 만들려면** `-ForwardTo`를 메시지가 전달되기를 원하는 큐 또는 토픽의 이름으로 설정한 [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) 명령을 사용합니다.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

**기존 구독에 대한 자동 전달 설정을 업데이트** 하려면 다음 예제를 참조하세요.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.ForwardTo='mytopic2'

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
**자동 전달이 사용하도록 설정된 큐를 만들려면** 큐 속성 섹션의 `forwardTo`를 메시지가 전달되기를 원하는 큐 또는 토픽의 이름으로 설정합니다. 자세한 내용은 [Microsoft.ServiceBus namespaces/queues 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)를 참조하세요. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

**자동 전달이 사용하도록 설정된 큐에 대해 구독을 만들려면** 큐 속성 섹션의 `forwardTo`를 메시지가 전달되기를 원하는 큐 또는 토픽의 이름으로 설정합니다. 자세한 내용은 [Microsoft.ServiceBus namespaces/topics/subscriptions 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)를 참조하세요. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "forwardTo": "myqueue2"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="net"></a>.NET 

### <a name="azuremessagingservicebus-latest"></a>Azure.Messaging.ServiceBus(최신)
[CreateQueueOptions.ForwardTo](/dotnet/api/azure.messaging.servicebus.administration.createqueueoptions.forwardto) 또는 [CreateSubscriptionOptions.ForwardTo](/dotnet/api/azure.messaging.servicebus.administration.createsubscriptionoptions.forwardto)를 설정한 다음, `CreateQueueOptions` 또는 `CreateSubscriptionOptions` 매개 변수를 사용하는 [CreateQueueAsync](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient.createqueueasync#Azure_Messaging_ServiceBus_Administration_ServiceBusAdministrationClient_CreateQueueAsync_Azure_Messaging_ServiceBus_Administration_CreateQueueOptions_System_Threading_CancellationToken_) 또는 [CreateSubscriptionAsync](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient.createsubscriptionasync#Azure_Messaging_ServiceBus_Administration_ServiceBusAdministrationClient_CreateSubscriptionAsync_Azure_Messaging_ServiceBus_Administration_CreateSubscriptionOptions_System_Threading_CancellationToken_) 메서드를 사용하여 자동 전달 기능을 사용하도록 설정할 수 있습니다. 

### <a name="microsoftazureservicebus-legacy"></a>Microsoft.Azure.ServiceBus(레거시)
다음 예제와 같이 원본에 대해 [QueueDescription.ForwardTo](/dotnet/api/microsoft.servicebus.messaging.queuedescription) 또는 [SubscriptionDescription.ForwardTo](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)를 설정하여 자동 전달을 사용하도록 설정할 수 있습니다.

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

## <a name="java"></a>Java

### <a name="azure-messaging-servicebus-latest"></a>azure-messaging-servicebus(최신)
[CreateQueueOptions.setForwardTo(String forwardTo)](/java/api/com.azure.messaging.servicebus.administration.models.createqueueoptions.setforwardto) 메서드 또는 [CreateSubscriptionOptions.setForwardTo(String forwardTo)](/java/api/com.azure.messaging.servicebus.administration.models.createsubscriptionoptions.setforwardto)를 사용한 다음, `CreateQueueOptions` 또는 `CreateSubscriptionOptions` 매개 변수를 사용하는 [createQueue](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient.createqueue#com_azure_messaging_servicebus_administration_ServiceBusAdministrationClient_createQueue_java_lang_String_com_azure_messaging_servicebus_administration_models_CreateQueueOptions_) 메서드 또는 [createSubscription](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient.createsubscription#com_azure_messaging_servicebus_administration_ServiceBusAdministrationClient_createSubscription_java_lang_String_java_lang_String_com_azure_messaging_servicebus_administration_models_CreateSubscriptionOptions_) 메서드를 사용하여 자동 전달 기능을 사용하도록 설정할 수 있습니다. 

### <a name="azure-servicebus-legacy"></a>azure-servicebus(레거시)
원본에 대해 [QueueDescription.setForwardTo(String forwardTo)](/java/api/com.microsoft.azure.servicebus.management.queuedescription.setforwardto#com_microsoft_azure_servicebus_management_QueueDescription_setForwardTo_java_lang_String_) 또는 [SubscriptionDescription.setForwardTo(String forwardTo)](/java/api/com.microsoft.azure.servicebus.management.subscriptiondescription.setforwardto)를 사용하여 자동 전달을 사용하도록 설정할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
