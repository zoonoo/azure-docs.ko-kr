---
title: 리소스 그룹 & 구독 간 리소스 배포
description: 배포 중에 둘 이상의 Azure 구독 및 리소스 그룹을 대상으로 하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0754895215384f76b1cb44224f3ba06c80181827
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978767"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>둘 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포

일반적으로 템플릿의 모든 리소스를 단일 [리소스 그룹](resource-group-overview.md)에 배포 합니다. 그러나 일련의 리소스를 함께 배포 하 고 다른 리소스 그룹 또는 구독에 배치 하려는 시나리오가 있습니다. 예를 들어 Azure Site Recovery에 대 한 백업 가상 컴퓨터를 별도의 리소스 그룹 및 위치에 배포 하려고 할 수 있습니다. 리소스 관리자를 사용 하면 중첩 된 템플릿을 사용 하 여 둘 이상의 구독 및 리소스 그룹을 대상으로 지정할 수 있습니다.

> [!NOTE]
> 단일 배포에서 5 개의 리소스 그룹에만 배포할 수 있습니다. 일반적으로이 제한 사항은 부모 템플릿에 대해 지정 된 하나의 리소스 그룹 및 중첩 또는 연결 된 배포에서 최대 4 개의 리소스 그룹에 배포할 수 있음을 의미 합니다. 그러나 부모 템플릿에 중첩 된 템플릿이나 연결 된 템플릿만 있고 자체 리소스를 배포 하지 않는 경우에는 중첩 또는 연결 된 배포에서 최대 5 개의 리소스 그룹을 포함할 수 있습니다.

## <a name="specify-subscription-and-resource-group"></a>구독 및 리소스 그룹 지정

다른 리소스 그룹 또는 구독을 대상으로 하려면 [중첩 되거나 연결 된 템플릿을](resource-group-linked-templates.md)사용 합니다. `Microsoft.Resources/deployments` 리소스 형식은 중첩 된 배포에 대 한 구독 및 리소스 그룹을 지정할 수 있도록 하는 `subscriptionId` 및 `resourceGroup`에 대 한 매개 변수를 제공 합니다. 구독 ID 또는 리소스 그룹을 지정 하지 않으면 부모 템플릿의 구독 및 리소스 그룹이 사용 됩니다. 모든 리소스 그룹은 배포를 실행 하기 전에 존재 해야 합니다.

템플릿을 배포 하는 데 사용 하는 계정에는 지정 된 구독 ID에 배포할 수 있는 권한이 있어야 합니다. 지정 된 구독이 다른 Azure Active Directory 테 넌 트에 있는 경우 [다른 디렉터리에서 게스트 사용자를 추가](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)해야 합니다.

다른 리소스 그룹 및 구독을 지정 하려면 다음을 사용 합니다.

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

리소스 그룹이 동일한 구독에 있는 경우 **subscriptionId** 값을 제거할 수 있습니다.

다음 예제에서는 두 개의 저장소 계정을 배포 합니다. 첫 번째 저장소 계정은 배포 중에 지정 된 리소스 그룹에 배포 됩니다. 두 번째 저장소 계정은 `secondResourceGroup` 및 `secondSubscriptionID` 매개 변수에 지정 된 리소스 그룹에 배포 됩니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        }
    ]
}
```

`resourceGroup`를 존재 하지 않는 리소스 그룹의 이름으로 설정 하면 배포에 실패 합니다.

위의 템플릿을 테스트 하 고 결과를 확인 하려면 PowerShell 또는 Azure CLI를 사용 합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

두 개의 저장소 계정을 **동일한 구독의**두 리소스 그룹에 배포 하려면 다음을 사용 합니다.

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

두 개의 저장소 계정을 **두 개의 구독**에 배포 하려면 다음을 사용 합니다.

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

두 개의 저장소 계정을 **동일한 구독의**두 리소스 그룹에 배포 하려면 다음을 사용 합니다.

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

두 개의 저장소 계정을 **두 개의 구독**에 배포 하려면 다음을 사용 합니다.

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Függvények használata

[ResourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) 및 [subscription ()](resource-group-template-functions-resource.md#subscription) 함수는 템플릿을 지정 하는 방법에 따라 다르게 확인 됩니다. 외부 템플릿에 연결 하는 경우 함수는 항상 해당 템플릿에 대 한 범위로 확인 됩니다. 부모 템플릿 내에서 템플릿을 중첩 하는 경우 `expressionEvaluationOptions` 속성을 사용 하 여 함수가 부모 템플릿 또는 중첩 된 템플릿에 대 한 리소스 그룹 및 구독을 확인할 지 여부를 지정 합니다. 속성을 `inner` 설정 하 여 중첩 된 템플릿의 범위를 확인 합니다. 부모 템플릿의 범위로 확인 되도록 속성을 `outer` 설정 합니다.

다음 표에서는 함수가 부모 또는 포함 된 리소스 그룹 및 구독을 확인 하는지 여부를 보여 줍니다.

| 템플릿 형식 | Hatókör | Felbontás |
| ------------- | ----- | ---------- |
| 내포        | outer (기본값) | 부모 리소스 그룹 |
| 내포        | 내부 | 하위 리소스 그룹 |
| #b0        | –   | 하위 리소스 그룹 |

다음 [예제 템플릿]((https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)) 에서는 다음을 보여 줍니다.

* 기본 (외부) 범위를 사용 하는 중첩 된 템플릿
* 내부 범위를 포함 하는 중첩 된 템플릿
* 연결 된 템플릿

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "defaultScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "name": "innerScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "linkedGroup",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
                },
                "parameters": {}
            }
        }
    ],
    "outputs": {
        "parentRG": {
            "type": "string",
            "value": "[concat('Parent resource group is ', resourceGroup().name)]"
        },
        "defaultScopeRG": {
            "type": "string",
            "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "innerScopeRG": {
            "type": "string",
            "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "linkedRG": {
            "type": "string",
            "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
        }
    }
}
```

위의 템플릿을 테스트 하 고 결과를 확인 하려면 PowerShell 또는 Azure CLI를 사용 합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

이전 예제의 출력은 다음과 같습니다.

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

이전 예제의 출력은 다음과 같습니다.

```azurecli
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Következő lépések

* 템플릿에서 매개 변수를 정의 하는 방법을 이해 하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조 하세요.
* 일반적인 배포 오류를 해결 하는 방법에 대 한 팁은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조 하세요.
* SAS 토큰이 필요한 템플릿을 배포 하는 방법에 대 한 자세한 내용은 [sas 토큰을 사용 하 여 개인 템플릿 배포](resource-manager-powershell-sas-token.md)를 참조 하세요.
