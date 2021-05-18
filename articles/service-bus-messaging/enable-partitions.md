---
title: Azure Service Bus 큐 및 토픽에서 분할을 사용하도록 설정
description: 이 문서에서는 Azure Portal, PowerShell, CLI 및 프로그래밍 언어(C#, Java, Python 및 JavaScript)를 사용하여 Azure Service Bus 큐 및 토픽에서 분할을 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755086"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Azure Service Bus 큐 또는 토픽에 대해 분할을 사용하도록 설정
Service Bus 파티션을 사용하면 큐 및 항목 또는 메시징 엔터티가 여러 메시지 broker 및 메시징 저장소에 분할될 수 있습니다. 분할은 분할된 엔터티의 전체 처리량이 단일 메시지 broker 또는 메시징 저장소의 성능으로 제한되지 않는다는 의미입니다. 또한 메시징 저장소가 일시적으로 중단된 경우에도 분할된 큐 또는 토픽을 계속 사용할 수 있습니다. 분할된 큐 및 항목은 트랜잭션 및 세션에 대한 지원 같은 모든 고급 Service Bus 기능을 포함할 수 있습니다. 자세한 내용은 [분할된 큐 및 토픽](service-bus-partitioning.md)을 참조하세요. 이 문서에서는 Service Bus 큐 또는 토픽에 대해 중복 메시지 검색을 사용하도록 설정하는 다양한 방법을 보여 줍니다. 

> [!IMPORTANT]
> - 분할은 기본 또는 표준 SKU의 모든 큐와 항목에서 엔터티 생성에 지원됩니다. 프리미엄 메시지 SKU에 지원되지 않지만 프리미엄 네임스페이스에서 기존에 분할된 엔터티는 정상적으로 계속 작동합니다.
> - 기존 큐 또는 토픽에서는 분할 옵션을 변경할 수 없습니다. 큐 또는 토픽을 만들 때만 옵션을 설정할 수 있습니다. 

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal에서 **큐** 를 만들 때 다음 이미지에 표시된 것처럼 **분할 사용** 을 선택합니다. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="큐를 만드는 시점에 분할을 사용하도록 설정":::

Azure Portal에서 토픽을 만들 때 다음 이미지에 표시된 것처럼 **분할 사용** 을 선택합니다. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="토픽을 만드는 시점에 분할을 사용하도록 설정":::

## <a name="using-azure-cli"></a>Azure CLI 사용
**분할이 사용하도록 설정된 큐를 만들려면** `--enable-partitioning`을 `true`로 설정한 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 명령을 사용합니다.

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

**분할이 사용하도록 설정된 토픽을 만들려면** `--enable-partitioning`을 `true`로 설정한 [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) 명령을 사용합니다.

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
**분할이 사용하도록 설정된 큐를 만들려면** `-EnablePartitioning`을 `$True`로 설정한 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

**분할이 사용하도록 설정된 토픽을 만들려면** `-EnablePartitioning`을 `true`로 설정한 [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) 명령을 사용합니다. 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
**분할이 사용하도록 설정된 큐를 만들려면** 큐 속성 섹션에서 `enablePartitioning`을 `true`로 설정합니다. 자세한 내용은 [Microsoft.ServiceBus namespaces/queues 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)를 참조하세요. 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

**중복 검색이 사용하도록 설정된 토픽을 만들려면** 토픽 속성 섹션에서 `enablePartitioning`을 `true`로 설정합니다. 자세한 내용은 [Microsoft.ServiceBus namespaces/topics 템플릿 참조](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)를 참조하세요. 

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
            "enablePartitioning": true
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