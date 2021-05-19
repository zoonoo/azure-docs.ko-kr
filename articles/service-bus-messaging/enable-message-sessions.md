---
title: Azure Service Bus 메시지 세션 사용 | Microsoft Docs
description: 이 문서에서는 Azure Portal, PowerShell, CLI 및 프로그래밍 언어(C#, Java, Python 및 JavaScript)를 사용하여 메시지 세션을 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755094"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Azure Service Bus 큐 또는 구독에 대해 메시지 세션을 사용하도록 설정
Azure Service Bus 세션을 사용하면 관련 메시지의 무제한 시퀀스를 공동으로 순서를 지정하여 처리할 수 있습니다. 세션은 **FIFO(선입 선출)** 및 **요청-응답** 패턴으로 사용할 수 있습니다. 자세한 내용은 [메시지 세션](message-sessions.md)을 참조하세요. 이 문서에서는 Service Bus 큐 또는 구독에 대해 세션을 사용하도록 설정하는 다양한 방법을 보여 줍니다. 

> [!IMPORTANT]
> - Service Bus의 기본 계층에서는 세션을 지원하지 않습니다. 표준 및 프리미엄 계층은 세션을 지원합니다. 이러한 계층 간의 차이점은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.
> - 큐 또는 구독을 만든 후에는 메시지 세션을 사용하거나 사용하지 않도록 설정할 수 없습니다. 큐 또는 구독을 만드는 시점에만 수행할 수 있습니다. 

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal에서 **큐** 를 만들 때 다음 이미지에 표시된 것처럼 **세션 사용** 을 선택합니다. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="큐를 만드는 시점에 세션을 사용하도록 설정":::

Azure Portal에서 토픽에 대해 구독을 만들 때 다음 이미지에 표시된 것처럼 **세션 사용** 을 선택합니다. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="구독을 만드는 시점에 세션을 사용하도록 설정":::

## <a name="using-azure-cli"></a>Azure CLI 사용
**메시지 세션이 사용하도록 설정된 큐를 만들려면** `--enable-session`을 `true`로 설정한 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 명령을 사용합니다.

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

**메시지 세션이 사용하도록 설정된 토픽에 대해 구독을 만들려면** `--enable-session`을 `true`로 설정한 [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) 명령을 사용합니다.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
**메시지 세션이 사용하도록 설정된 큐를 만들려면** `-RequiresSession`을 `$True`로 설정한 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

**메시지 세션이 사용하도록 설정된 토픽에 대해 구독을 만들려면** `-RequiresSession`을 `true`로 설정한 [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
**메시지 세션이 사용하도록 설정된 큐를 만들려면** 큐 속성 섹션에서 `requiresSession`을 `true`로 설정합니다. 자세한 내용은 [Microsoft.ServiceBus namespaces/queues 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)를 참조하세요. 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

**메시지 세션이 사용하도록 설정된 토픽에 대해 구독을 만들려면** 구독 속성 세션에서 `requiresSession`을 `true`로 설정합니다. 자세한 내용은 [Microsoft.ServiceBus namespaces/topics/subscriptions 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)를 참조하세요. 

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
                "requiresSession": true
              }
            }
          ]
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>다음 단계
선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [.NET용 Azure.Messaging.ServiceBus 샘플](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Microsoft.Azure.ServiceBus 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java용 azure-servicebus 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)