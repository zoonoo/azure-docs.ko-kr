---
title: 소비자 그룹을 사용하여 이벤트 허브 만들기 - Azure Event Hubs | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 이벤트 허브 및 소비자 그룹이 있는 Event Hubs 네임스페이스 만들기
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 992b27622328bab85fcfaf06ad5a941aceed446f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051461"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>빠른 시작: Azure Resource Manager 템플릿을 사용하여 이벤트 허브 만들기
Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 장치에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

빠른 시작에서 Azure Resource Manager 템플릿을 사용하여 이벤트 허브를 만듭니다. Azure Resource Manager 템플릿을 사용하여 하나의 이벤트 허브 및 하나의 소비자 그룹이 있는 [Event Hubs](event-hubs-what-is-event-hubs.md) 형식의 네임스페이스를 만듭니다. 또한 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 설명합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다. 템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성][Authoring Azure Resource Manager templates]을 참조하세요. 템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.EventHub 리소스 종류](/azure/templates/microsoft.eventhub/allversions)를 참조하세요.

> [!NOTE]
> 전체 템플릿은 GitHub에서 [이벤트 허브 및 소비자 그룹 템플릿][Event Hub and consumer group template]을 참조하세요. 이 템플릿은 이벤트 허브 네임스페이스 및 이벤트 허브 외에 소비자 그룹을 만들었습니다. 최신 템플릿을 확인하려면 [Azure 빠른 시작 템플릿][Azure Quickstart Templates] 갤러리를 방문하여 Event Hubs를 검색하세요.

## <a name="prerequisites"></a>필수 조건
이 빠른 시작을 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

**Azure PowerShell**을 사용하여 Resource Manager 템플릿을 배포하려면 [Azure PowerShell을 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)합니다.

**Azure CLI**를 사용하여 Resource Manager 템플릿을 배포하려면 [Azure CLI를 설치]( /cli/azure/install-azure-cli)합니다.

## <a name="create-the-resource-manager-template-json"></a>Resource Manager 템플릿 JSON 만들기
다음 콘텐츠를 사용하여 MyEventHub.json이라는 JSON 파일을 만들고 폴더(예: C:\EventHubsQuickstarts\ResourceManagerTemplate)에 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>매개 변수 JSON 만들기
Azure Resource Manager 템플릿에 대한 매개 변수를 포함하는 MyEventHub-Parameters.json이라는 JSON 파일을 만듭니다. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
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

### <a name="provision-resources"></a>리소스 프로비전
Azure PowerShell을 사용하여 리소스로 배포/프로비전하려면 C:\EventHubsQuickStart\ARM\ 폴더로 전환하고 다음 명령을 실행합니다.

> [!IMPORTANT]
> 명령을 실행하기 전에 Azure 리소스 그룹의 이름을 $resourceGroupName의 값으로 지정합니다. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzureRmResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Azure CLI를 사용하여 템플릿 배포

## <a name="sign-in-to-azure"></a>Azure에 로그인

Cloud Shell에서 명령을 실행하는 경우에는 다음 단계가 필요하지 않습니다. CLI를 로컬로 실행하는 경우 다음 단계를 수행하여 Azure에 로그인하고 현재 구독을 설정합니다.

다음 명령을 실행하여 Azure에 로그인합니다.

```azurecli
az login
```

현재 구독 컨텍스트를 설정합니다. `MyAzureSub`를 사용할 Azure 구독의 이름으로 바꿉니다.

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>리소스 프로비전
Azure CLI를 사용하여 리소스를 배포하려면 C:\EventHubsQuickStart\ARM\ 폴더로 전환하고 다음 명령을 실행합니다.

> [!IMPORTANT]
> az group create 명령에서 Azure 리소스 그룹의 이름을 지정합니다. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

축하합니다! Azure Resource Manager 템플릿을 사용하여 Event Hubs 네임스페이스 및 해당 네임스페이스 내에 이벤트 허브를 만들었습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Event Hubs 네임스페이스를 만들었으며, 이벤트 허브에서 이벤트를 보내고 받기 위해 응용 프로그램 예제를 사용했습니다. 이벤트 허브에서 이벤트를 보내고 받는 단계별 지침은 다음 자습서를 참조하세요. 

- **이벤트 허브로 이벤트 전송**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **이벤트 허브에서 이벤트 수신**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
