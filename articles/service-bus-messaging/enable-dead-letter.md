---
title: Azure Service Bus 큐와 구독에서 배달 못 한 편지 기능 사용
description: 이 문서에서는 Azure Portal, PowerShell, CLI 및 프로그래밍 언어(c#, Java, Python 및 JavaScript)를 사용하여 큐와 구독에서 배달 못 한 편지 기능을 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 04/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 13082574510a0668076b7191b6b973e5a1a7cd77
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671481"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Azure Service Bus 큐와 구독에서 메시지 만료 시 배달 못 한 편지 기능 사용
Azure Service Bus 큐와 토픽 구독은 DLQ(배달 못 한 편지 큐)라는 보조 하위 큐를 제공합니다. 배달 못 한 편지 큐는 명시적으로 만들 필요가 없으며 주 엔터티와 독립적으로 삭제하거나 관리할 수 없습니다. 배달 못한 편지 큐의 목적은 수신기에 배달할 수 없는 메시지 또는 처리할 수 없는 메시지를 보관하는 것입니다. 자세한 정보는 [Service Bus 배달 못 한 편지 큐의 개요](service-bus-dead-letter-queues.md)를 참조하세요. 이 문서에서는 Service Bus 큐와 구독에서 배달 못 한 편지 기능을 사용하는 다양한 방법을 보여 줍니다. 

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal 토픽에 대한 **큐** 또는 **구독** 을 만들 때 다음 예제와 같이 **메시지 만료 시 배달 못 한 편지 기능 사용** 을 선택합니다. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>배달 못 한 편지 기능을 사용하여 큐 만들기
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="큐를 만들 때 배달 못 한 편지 기능 사용":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>배달 못 한 편지 기능을 사용하여 구독 만들기
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="구독을 만들 때 배달 못 한 편지 기능 사용":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>기존 큐에 대한 메시지 만료 설정에서 배달 못 한 편지 기능 업데이트
Service Bus 큐에 대한 **개요** 페이지에서 메시지 만료 설정의 **배달 못 한 편지 기능** 의 현재 값을 선택합니다. 다음 예에서 현재 값은 **사용 안 함** 입니다. 팝업 창에서 메시지 만료 시 배달 못 한 편지 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="기존 큐의 메시지 만료 시 배달 못한 편지 사용":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>기존 구독에 대한 메시지 만료 설정에서 배달 못 한 편지 기능 업데이트
Service Bus 구독의 **개요** 페이지에서 메시지 만료 설정의 **배달 못 한 편지 기능** 의 현재 값을 선택합니다. 다음 예에서 현재 값은 **사용 안 함** 입니다. 팝업 창에서 메시지 만료 시 배달 못 한 편지 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="기존 구독의 메시지 만료 시 배달 못 한 편지 기능 사용":::

## <a name="using-azure-cli"></a>Azure CLI 사용
**메시지 만료 시 배달 못 한 편지 기능을 사용하여 큐를 만들려면** `--enable-dead-lettering-on-message-expiration`을 `true`로 설정한 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 명령을 사용합니다. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

**기존 큐에 대한 메시지 만료 설정에서 배달 못 한 편지 기능을 사용하도록 설정하려면** `--enable-dead-lettering-on-message-expiration`을 `true`로 설정한 [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) 명령을 사용합니다. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


**메시지 만료 시 배달 못 한 편지 기능을 사용하는 토픽의 구독을 만들려면** `--enable-dead-lettering-on-message-expiration`을 `true`로 설정한 [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) 명령을 사용합니다.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

**토픽 구독에 대한 메시지 만료 설정에서 배달 못 한 편지 기능을 사용하도록 설정하려면** `--enable-dead-lettering-on-message-expiration`을 `true`로 설정한 [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) 명령을 사용합니다.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
**메시지 만료 시 배달 못 한 편지 기능을 사용하여 큐를 만들려면** `-DeadLetteringOnMessageExpiration`을 `$True`로 설정한 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

**기존 큐에 대한 메시지 만료 설정에서 배달 못 한 편지 기능을 사용하도록 설정하려면** 다음 예제와 같이 [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) 명령을 사용합니다.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

**메시지 만료 시 배달 못 한 편지 기능을 사용하는 토픽에 대한 구독을 만들려면** `-DeadLetteringOnMessageExpiration`을 `$True`로 설정한 [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

**기존 구독에 대한 메시지 만료 설정에서 배달 못 한 편지 기능을 사용하도록 설정하려면** 다음 예제를 참조하세요.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
**메시지 만료 시 배달 못 한 편지 기능을 사용하여 큐를 만들려면** 큐 속성 섹션에서 `deadLetteringOnMessageExpiration`를 `true`로 설정합니다. 자세한 정보는 [Microsoft.ServiceBus 네임스페이스/큐 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)에서 알아보세요. 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

**메시지 만료 시 배달 못 한 편지 기능을 사용하는 토픽에 대한 구독을 만들려면** 큐 속성 섹션에서 `deadLetteringOnMessageExpiration`를 `true`로 설정합니다. 자세한 정보는 [Microsoft.ServiceBus 네임스페이스/토픽/구독 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)에서 알아보세요. 

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
                "deadLetteringOnMessageExpiration": true
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

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
