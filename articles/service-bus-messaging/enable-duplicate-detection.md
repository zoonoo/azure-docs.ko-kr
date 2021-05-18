---
title: 중복 메시지 검색을 사용하도록 설정 - Azure Service Bus
description: 이 문서에서는 Azure Portal, PowerShell, CLI 및 프로그래밍 언어(C#, Java, Python 및 JavaScript)를 사용하여 중복 메시지 검색을 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755099"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Azure Service Bus 큐 또는 토픽에 대해 중복 메시지 검색을 사용하도록 설정
큐 또는 토픽에 대해 중복 검색을 사용하도록 설정할 때 Azure Service Bus는 큐 또는 토픽으로 전송된 모든 메시지의 기록을 구성된 시간 동안 유지합니다. 이 기간 동안에는 큐 또는 토픽에 중복된 메시지가 저장되지 않습니다. 이 속성을 사용하도록 설정하면 사용자가 정의한 기간 동안 정확히 한 번만 전달됩니다. 자세한 내용은 [중복 검색](duplicate-detection.md)을 참조하세요. 이 문서에서는 Service Bus 큐 또는 토픽에 대해 중복 메시지 검색을 사용하도록 설정하는 다양한 방법을 보여 줍니다. 


> [!NOTE]
> - Service Bus의 기본 계층에서는 중복 검색을 지원하지 않습니다. 표준 및 프리미엄 계층은 중복 검색을 지원합니다. 이러한 계층 간의 차이점은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.
> - 큐 또는 토픽을 만든 후에는 중복 검색을 사용하거나 사용하지 않도록 설정할 수 없습니다. 큐 또는 토픽을 만들 때에만 수행할 수 있습니다. 

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal에서 **큐** 를 만들 때 다음 이미지에 표시된 것처럼 **중복 검색 사용** 을 선택합니다. 큐 또는 토픽을 만들 때 중복 검색 기간을 구성할 수 있습니다. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="큐를 만드는 시점에 중복 검색을 사용하도록 설정":::

Azure Portal에서 토픽을 만들 때 다음 이미지에 표시된 것처럼 **중복 검색 사용** 을 선택합니다. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="토픽을 만드는 시점에 중복 검색을 사용하도록 설정":::

또한 만드는 시점에 중복 검색을 사용하도록 설정한 경우 기존 큐 또는 토픽에 대해 이 설정을 구성할 수 있습니다. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>기존 큐 또는 토픽에 대해 중복 검색 기간 업데이트
기존 큐 또는 토픽에 대해 중복 검색 기간을 변경하려면 **개요** 페이지에서 **중복 검색 창** 에 대해 **변경** 을 선택합니다.  

#### <a name="queue"></a>큐
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="큐에 대해 중복 검색 기간 설정":::

#### <a name="topic"></a>항목
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="토픽에 대해 중복 검색 기간 설정":::


## <a name="using-azure-cli"></a>Azure CLI 사용
**중복 검색이 사용하도록 설정된 큐를 만들려면** `--enable-duplicate-detection`을 `true`로 설정한 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 명령을 사용합니다. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

**중복 검색이 사용하도록 설정된 토픽을 만들려면** `--enable-duplicate-detection`을 `true`로 설정한 [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) 명령을 사용합니다.

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

또한 위의 예에서는 `--duplicate-detection-history-time-window` 매개 변수를 사용하여 중복 검색 기간을 설정합니다. 기간은 1일로 설정됩니다. 기본값은 10분이고 허용되는 최대값은 7일입니다.

**새 검색 기간으로 큐를 업데이트하려면** `--duplicate-detection-history-time-window` 매개 변수와 함께 [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) 명령을 사용합니다. 이 예에서는 기간이 7일로 업데이트됩니다. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

유사하게 **새 검색 기간으로 토픽을 업데이트하려면** `--duplicate-detection-history-time-window` 매개 변수와 함께 [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) 명령을 사용합니다. 이 예에서는 기간이 7일로 업데이트됩니다.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Azure PowerShell 사용
**중복 검색이 사용하도록 설정된 큐를 만들려면** `-RequiresDuplicateDetection`을 `$True`로 설정한 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

**중복 검색이 사용하도록 설정된 토픽을 만들려면** `-RequiresDuplicateDetection`을 `true`로 설정한 [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

또한 위의 예에서는 `-DuplicateDetectionHistoryTimeWindow` 매개 변수를 사용하여 중복 검색 기간을 설정합니다. 기간은 1일로 설정됩니다. 기본값은 10분이고 허용되는 최대값은 7일입니다.

**새 검색 기간으로 큐를 업데이트하려면** 다음 예를 참조하세요.  이 예에서는 기간이 7일로 업데이트됩니다.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

**새 검색 기간으로 토픽을 업데이트하려면** 다음 예를 참조하세요. 이 예에서는 기간이 7일로 업데이트됩니다.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
**중복 검색이 사용하도록 설정된 큐를 만들려면** 큐 속성 섹션에서 `requiresDuplicateDetection`을 `true`로 설정합니다. 자세한 내용은 [Microsoft.ServiceBus namespaces/queues 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)를 참조하세요. `duplicateDetectionHistoryTimeWindow` 속성의 값을 지정하여 중복 검색 기간을 설정합니다. 다음 예에서는 1일로 설정됩니다.  

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

**중복 검색이 사용하도록 설정된 토픽을 만들려면** 토픽 속성 섹션에서 `requiresDuplicateDetection`을 `true`로 설정합니다. 자세한 내용은 [Microsoft.ServiceBus namespaces/topics 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)를 참조하세요. 

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
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