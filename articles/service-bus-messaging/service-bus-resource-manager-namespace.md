---
title: Azure Resource Manager 템플릿을 사용하여 Service Bus 메시징 네임스페이스 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Service Bus 메시징 네임스페이스 만들기
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: e5c4eca772cf17f04ea10f4d5ae166ea41eaa830
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496924"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Service Bus 네임스페이스 만들기
이 빠른 시작에서는 **표준** SKU가 있는 **메시지** 형식의 Service Bus 네임스페이스를 만드는 Azure Resource Manager 템플릿을 만듭니다. 이 문서는 또한 배포의 실행에 대해 지정된 매개 변수도 정의합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다. 템플릿 만들기에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성하기][Authoring Azure Resource Manager templates]를 참조하세요. 전체 템플릿은 GitHub에서 [Service Bus 네임스페이스 템플릿][Service Bus namespace template]을 참조하세요.

> [!NOTE]
> 다음 Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다. 
> 
> * [큐가 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-queue.md)
> * [토픽 및 구독이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
> * [큐 및 권한 부여 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-auth-rule.md)
> * [토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 최신 템플릿을 확인하려면 Service Bus에 대한 [Azure 빠른 시작 템플릿][Azure Quickstart Templates] 갤러리 및 검색을 방문하세요.

## <a name="quick-deployment"></a>빠른 배포
JSON을 작성하지 않고 PowerShell/CLI 명령을 실행하지 않고 샘플을 실행하려면 다음 단추를 선택합니다.

[![Azure에 배포](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

템플릿을 수동으로 만들고 배포하려면 이 문서의 다음 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 빠른 시작을 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

**Azure PowerShell**을 사용하여 Resource Manager 템플릿을 배포하려면 [Azure PowerShell을 설치](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)합니다.

**Azure CLI**를 사용하여 Resource Manager 템플릿을 배포하려면 [Azure CLI를 설치]( /cli/azure/install-azure-cli)합니다.

## <a name="create-the-resource-manager-template-json"></a>Resource Manager 템플릿 JSON 만들기 
다음과 같은 내용이 포함된 **MyServiceBusNamespace.json**이라는 JSON 파일을 만듭니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
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
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

이 템플릿은 표준 Service Bus 네임스페이스를 만듭니다. JSON 구문 및 속성의 경우 [네임스페이스](/azure/templates/microsoft.servicebus/namespaces) 템플릿 참조를 참조하세요.

## <a name="create-the-parameters-json"></a>매개 변수 JSON 만들기
이전 단계에서 만든 템플릿에는 `Parameters`라는 섹션이 있습니다. 배포하는 프로젝트 또는 대상 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의합니다. 이 템플릿은 **serviceBusNamespaceName**, **serviceBusSku** 및 **location** 매개 변수를 정의합니다. Service Bus의 SKU에 대한 자세한 내용은 만들려는 [Service Bus SKU](https://azure.microsoft.com/pricing/details/service-bus/)를 참조하세요.

다음과 같은 내용이 포함된 **MyServiceBusNamespace-Parameters.json**이라는 JSON 파일을 만듭니다. 

> [!NOTE] 
> Service Bus 네임스페이스의 이름을 지정합니다. 


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Azure PowerShell을 사용하여 템플릿 배포

### <a name="sign-in-to-azure"></a>Azure에 로그인
1. Azure PowerShell 시작

2. 다음 명령을 실행하여 Azure에 로그인합니다.

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. 로그인했으면 다음 명령을 실행하여 현재 구독 컨텍스트를 설정합니다.

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>리소스 배포
Azure PowerShell을 사용하여 리소스를 배포하려면 JSON 파일을 저장한 폴더로 전환하고 다음 명령을 실행합니다.

> [!IMPORTANT]
> 명령을 실행하기 전에 Azure 리소스 그룹의 이름을 $resourceGroupName의 값으로 지정합니다. 

1. 리소스 그룹 이름에 대한 변수를 선언하고 값을 지정합니다. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Azure 리소스 그룹 만들기

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. Resource Manager 템플릿을 배포합니다. 배포 자체, 리소스 그룹, 템플릿용 JSON 파일, 매개 변수용 JSON 파일의 이름을 지정합니다.

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Azure CLI를 사용하여 템플릿 배포

### <a name="sign-in-to-azure"></a>Azure에 로그인

1. 다음 명령을 실행하여 Azure에 로그인합니다.

    ```azurecli
    az login
    ```
2. 현재 구독 컨텍스트를 설정합니다. `MyAzureSub`를 사용할 Azure 구독의 이름으로 바꿉니다.

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>리소스 배포
Azure CLI를 사용하여 리소스를 배포하려면 JSON 파일이 있는 폴더로 전환하고 다음 명령을 실행합니다.

> [!IMPORTANT]
> az group create 명령에서 Azure 리소스 그룹의 이름을 지정합니다. .

1. Azure 리소스 그룹 만들기 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Resource Manager 템플릿을 배포합니다. 리소스 그룹, 배포, 템플릿용 JSON 파일, 매개 변수용 JSON 파일의 이름을 지정합니다.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>다음 단계
이 문서에서는 Service Bus 네임스페이스를 만들었습니다. 다른 빠른 시작을 참조하여 큐, 토픽/구독을 만들고 사용하는 방법에 대해 알아봅니다. 

- [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus 큐 토픽 시작](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: https://azure.microsoft.com/pricing/details/service-bus/
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
