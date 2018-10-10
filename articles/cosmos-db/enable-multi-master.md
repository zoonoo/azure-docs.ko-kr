---
title: Azure Cosmos DB 계정에 다중 마스터 사용
description: 이 문서에서는 Azure Portal, PowerShell, CLI 또는 Azure Resource Manager 템플릿을 통해 Azure Cosmos DB 계정을 만드는 동안 다중 마스터 지원이 가능하도록 설정하는 방법에 대해 설명합니다.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 71579e28f389d91498ef5f37c5d43dc9d5140234
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963222"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Azure Cosmos DB 계정에 다중 마스터 사용

다중 마스터 지원은 Azure Cosmos DB 계정을 만드는 동안 사용하도록 설정됩니다. Azure Cosmos DB 계정은 Azure Portal, PowerShell, CLI 또는 Azure Resource Manager 템플릿을 사용하여 만들 수 있습니다.

> [!IMPORTANT]
> 현재 다중 마스터 지원은 새로운 Azure Cosmos DB 계정에 대해서만 사용하도록 설정할 수 있습니다. 기존 Azure Cosmos DB 계정은 이 기능을 사용할 수 없습니다. 기존 계정에 대한 지원을 제공하기 위해 노력하고 있으며 지원이 가능해 지면 알려드리겠습니다.

다중 마스터 지원이 포함된 Azure Cosmos DB 계정을 만든 후에는 데이터베이스, 컨테이너를 만들고, 문서를 업로드하고, 충돌 해결 정책을 할당할 수 있습니다. 다중 마스터의 충돌 해결 및 코드 샘플은 [다중 마스터 코드 샘플](multi-master-conflict-resolution.md#code-samples) 문서를 참조하세요.

## <a name="enable-multi-master-using-azure-portal"></a>Azure Portal을 사용하여 다중 마스터 사용

1. [Azure 포털](https://portal.azure.com/)

2. 선택 **리소스 만들기 > 데이터베이스 > Azure Cosmos DB**를 클릭합니다.

3. **새 계정** 창에 새 Azure Cosmos DB 계정에 대한 설정을 입력합니다.

   |**설정**  |**제안 값** |**설명**|
   |---------|---------|---------|
   |구독   | {내 구독}  |이 Azure Cosmos DB 계정에 사용할 Azure 구독을 선택합니다.  |
   |리소스 그룹  |   {내 리소스 그룹 이름}    |  기존 리소스 그룹을 선택하거나  **새로 만들기**를 선택한 다음, 계정의 새 리소스 그룹 이름을 입력합니다. |
   |계정 이름 | {내 계정 이름}   |  내 Azure Cosmos DB 계정을 식별하는 고유한 이름을 입력합니다.        |
   |API  |   모두   |  API 형식을 선택합니다.   |
   |위치  | 원하는 지역 선택   | Azure Cosmos DB 계정을 호스트할 지리적 위치를 선택합니다. 이 계정은 여러 지역에 있으므로 어느 지역이든 선택할 수 있습니다.  |
   |지리적 중복 사용   |  사용  |  아래에서 선택할 다중 마스터를 사용하도록 선택합니다.   |
   |다중 마스터 사용 | 사용  | 이 계정에 대해 다중 마스터를 사용하도록 선택합니다. |


## <a name="using-multi-master-in-sdks"></a>SDK에서 다중 마스터 사용

다중 마스터를 사용하도록 설정된 계정을 사용하는 경우, 응용 프로그램 내에서, 아래 그림과 같이 ConnectionPolicy를 활용하여 다중 마스터링을 활용할 수 있습니다.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add("West US");
policy.PreferredLocations.Add("North Europe");
policy.PreferredLocations.Add("Southeast Asia");
```

## <a name="enable-multi-master-using-powershell"></a>PowerShell을 사용하여 다중 마스터 사용

`enableMultipleWriteLocations` 매개 변수를 "true"로 설정하여 다중 마스터 사용이 가능한 Cosmos DB 계정을 만들 수도 있습니다. 다중 마스터를 사용할 수 있는 Azure Cosmos DB 계정을 만들려면 PowerShell 창을 열고 다음 스크립트를 실행합니다.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>CLI를 사용하여 다중 마스터 사용

enable-multiple-write-locations 매개 변수를 "true"로 설정하여 다중 마스터를 사용하도록 설정할 수 있습니다. 다중 마스터를 사용할 수 있는 Azure Cosmos DB 계정을 만들려면 Azure CLI 또는 클라우드 쉘을 열고 다음 명령을 실행합니다.

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 다중 마스터 사용

다음 JSON 코드는 Azure Cosmos DB 계정을 배포하는 데 사용할 수 있는 예제 Resource Manager 템플릿입니다. Resource Manager 템플릿 형식 및 구문에 대해 알아보려면 [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 설명서를 참조하세요. 이 템플릿에서 유의해야 할 주요 매개 변수는 "enableMultipleWriteLocations": true입니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Cosmos DB 계정에 대해 다중 마스터 지원을 사용하도록 설정하는 방법을 알아보았습니다. 다음으로 아래 리소스를 살펴보겠습니다.

* [오픈 소스 NoSQL 데이터베이스로 다중 마스터 사용](multi-master-oss-nosql.md)

* [Azure Cosmos DB의 충돌 해결 이해](multi-master-conflict-resolution.md)
