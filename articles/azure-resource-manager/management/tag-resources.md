---
title: 논리적 조직에 대 한 리소스, 리소스 그룹 및 구독 태그
description: 태그를 적용하여 대금 청구 및 관리를 위해 Azure 리소스를 구성하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: fa56fac79855b438a0cd1c10ed1008d69ca3e2fe
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323879"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>태그를 사용 하 여 Azure 리소스 및 관리 계층 구조 구성

Azure 리소스, 리소스 그룹 및 구독에 태그를 적용 하 여 논리적으로 분류로 구성 합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

태깅 전략을 구현 하는 방법에 대 한 권장 사항은 [리소스 명명 및 태그 지정 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)를 참조 하세요.

> [!IMPORTANT]
> 태그 이름은 작업에서 대/소문자를 구분 하지 않습니다. 대/소문자에 관계 없이 태그 이름을 가진 태그가 업데이트 되거나 검색 됩니다. 그러나 리소스 공급자는 태그 이름에 대해 제공 하는 대/소문자를 유지할 수 있습니다. 비용 보고서에서 대/소문자 구분이 표시 됩니다.
> 
> 태그 값은 대/소문자를 구분 합니다.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>필요한 액세스

리소스에 태그를 적용 하려면 **Microsoft .resources/tags** 리소스 유형에 대 한 쓰기 권한이 있어야 합니다. [태그 참여자](../../role-based-access-control/built-in-roles.md#tag-contributor) 역할을 사용 하면 엔터티 자체에 액세스 하지 않고도 엔터티에 태그를 적용할 수 있습니다. 현재 태그 참여자 역할은 포털을 통해 리소스 또는 리소스 그룹에 태그를 적용할 수 없습니다. 포털을 통해 구독에 태그를 적용할 수 있습니다. PowerShell 및 REST API를 통한 모든 태그 작업을 지원 합니다.  

또한 [참가자](../../role-based-access-control/built-in-roles.md#contributor) 역할은 모든 엔터티에 태그를 적용 하는 데 필요한 액세스 권한을 부여 합니다. 하나의 리소스 형식에만 태그를 적용하려면 해당 리소스에 대한 기여자 역할을 사용합니다. 예를 들어, 가상 머신에 태그를 적용하려면 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)를 사용합니다.

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>태그 적용

Azure PowerShell은 태그를 적용 하기 위한 두 가지 명령 ( [AzTag](/powershell/module/az.resources/new-aztag) 및 [AzTag](/powershell/module/az.resources/update-aztag))을 제공 합니다. Az. Resources 모듈이 1.12.0 이상 이어야 합니다. 을 사용 하 여 버전을 확인할 수 있습니다 `Get-Module Az.Resources` . 해당 모듈을 설치 하거나 Azure PowerShell 3.6.1 이상을 [설치할](/powershell/azure/install-az-ps) 수 있습니다.

**AzTag** 는 리소스, 리소스 그룹 또는 구독에 대 한 모든 태그를 대체 합니다. 명령을 호출할 때 태그를 추가할 엔터티의 리소스 ID를 전달 합니다.

다음 예에서는 저장소 계정에 태그 집합을 적용 합니다.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

명령이 완료 되 면 리소스에 두 개의 태그가 있습니다.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

명령을 다시 실행 했지만이 시간이 다른 태그를 사용 하는 경우 이전 태그가 제거 됩니다.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

태그가 이미 있는 리소스에 태그를 추가 하려면 **AzTag**를 사용 합니다. **-Operation** 매개 변수를 **Merge**로 설정 합니다.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

두 개의 새 태그가 기존의 두 태그에 추가 되었습니다.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

각 태그 이름에는 값이 하나만 있을 수 있습니다. 태그에 새 값을 제공 하면 병합 작업을 사용 하는 경우에도 이전 값이 바뀝니다. 다음 예에서는 상태 태그를 표준에서 녹색으로 변경 합니다.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

**-Operation** 매개 변수를 **Replace**로 설정 하면 기존 태그가 새 태그 집합으로 대체 됩니다.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

새 태그만 리소스에 남아 있습니다.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

동일한 명령도 리소스 그룹 또는 구독에 사용할 수 있습니다. 태그를 지정할 리소스 그룹 또는 구독에 대 한 식별자를 전달 합니다.

리소스 그룹에 새 태그 집합을 추가 하려면 다음을 사용 합니다.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

리소스 그룹에 대 한 태그를 업데이트 하려면 다음을 사용 합니다.

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

구독에 새 태그 집합을 추가 하려면 다음을 사용 합니다.

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

구독에 대 한 태그를 업데이트 하려면 다음을 사용 합니다.

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

리소스 그룹에 동일한 이름의 리소스가 둘 이상 있을 수 있습니다. 이 경우 다음 명령을 사용 하 여 각 리소스를 설정할 수 있습니다.

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>태그 나열

리소스, 리소스 그룹 또는 구독에 대 한 태그를 가져오려면 [AzTag](/powershell/module/az.resources/get-aztag) 명령을 사용 하 여 엔터티에 대 한 리소스 ID를 전달 합니다.

리소스에 대 한 태그를 보려면 다음을 사용 합니다.

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

리소스 그룹에 대 한 태그를 보려면 다음을 사용 합니다.

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

구독에 대 한 태그를 보려면 다음을 사용 합니다.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>태그별 목록

특정 태그 이름 및 값을 포함 하는 리소스를 가져오려면 다음을 사용 합니다.

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

태그 값이 있는 특정 태그 이름을 가진 리소스를 가져오려면 다음을 사용 합니다.

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

특정 태그 이름 및 값을 포함 하는 리소스 그룹을 가져오려면 다음을 사용 합니다.

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>태그 제거

특정 태그를 제거 하려면 **AzTag** 및 set **-Operation** 을 사용 하 여 **삭제**합니다. 삭제 하려는 태그를 전달 합니다.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

지정 된 태그가 제거 됩니다.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

모든 태그를 제거 하려면 [AzTag](/powershell/module/az.resources/remove-aztag) 명령을 사용 합니다.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>태그 적용

리소스 그룹 또는 리소스에 태그를 추가 하는 경우 기존 태그를 덮어쓰거나 기존 태그에 새 태그를 추가할 수 있습니다.

리소스의 태그를 덮어쓰려면 다음을 사용 합니다.

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

리소스의 기존 태그에 태그를 추가 하려면 다음을 사용 합니다.

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

리소스 그룹의 기존 태그를 덮어쓰려면 다음을 사용 합니다.

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

리소스 그룹의 기존 태그에 태그를 추가 하려면 다음을 사용 합니다.

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

현재 Azure CLI에는 태그를 구독에 적용 하는 명령이 없습니다. 그러나 CLI를 사용 하 여 구독에 태그를 적용 하는 ARM 템플릿을 배포할 수 있습니다. [리소스 그룹 또는 구독에 태그 적용을](#apply-tags-to-resource-groups-or-subscriptions)참조 하세요.

### <a name="list-tags"></a>태그 나열

리소스에 대 한 기존 태그를 보려면 다음을 사용 합니다.

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

리소스 그룹에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli-interactive
az group show -n examplegroup --query tags
```

그러면 스크립트가 다음 형식을 반환합니다.

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>태그별 목록

특정 태그 및 값이 있는 모든 리소스를 가져오려면 `az resource list`를 사용합니다.

```azurecli-interactive
az resource list --tag Dept=Finance
```

특정 태그가 있는 리소스 그룹을 가져오려면 `az group list`를 사용합니다.

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>공백 처리

태그 이름 또는 값에 공백이 포함 된 경우 몇 가지 추가 단계를 수행 해야 합니다. 다음 예제에서는 태그에 공백이 포함 될 수 있는 경우 리소스 그룹의 모든 태그를 해당 리소스에 적용 합니다.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>템플릿

리소스 관리자 템플릿을 사용 하 여 배포 하는 동안 리소스, 리소스 그룹 및 구독에 태그를 지정할 수 있습니다.

### <a name="apply-values"></a>값 적용

다음 예제에서는 세 개의 태그를 사용 하 여 저장소 계정을 배포 합니다. 태그 중 두 개 ( `Dept` 및 `Environment` )는 리터럴 값으로 설정 됩니다. 한 태그 ( `LastDeployed` )는 기본적으로 현재 날짜로 설정 된 매개 변수로 설정 됩니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>개체 적용

여러 태그를 저장하는 개체 매개 변수를 정의하고 해당 개체를 태그 요소에 적용할 수 있습니다. 이 방법은 개체가 다른 속성을 가질 수 있기 때문에 이전 예제 보다 더 많은 유연성을 제공 합니다. 개체의 각 속성은 리소스에 대한 별도의 태그가 됩니다. 다음 예제에는 태그 요소에 적용되는 `tagValues`라는 매개 변수가 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>JSON 문자열 적용

단일 태그에 여러 값을 저장하려면 값을 나타내는 JSON 문자열을 적용합니다. 전체 JSON 문자열은 256 자를 초과할 수 없는 하나의 태그로 저장 됩니다. 다음 예제에는 JSON 문자열의 여러 값을 포함하는 `CostCenter`라는 단일 태그를 포함합니다.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>리소스 그룹에서 태그 적용

리소스 그룹의 태그를 리소스에 적용 하려면 [resourceGroup ()](../templates/template-functions-resource.md#resourcegroup) 함수를 사용 합니다. 태그 값을 가져올 때 `tags[tag-name]` `tags.tag-name` 일부 문자는 점 표기법에서 올바르게 구문 분석 되지 않으므로 구문 대신 구문을 사용 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>리소스 그룹 또는 구독에 태그 적용

**Microsoft .resources/tags** 리소스 종류를 배포 하 여 리소스 그룹 또는 구독에 태그를 추가할 수 있습니다. 태그는 배포에 대 한 대상 리소스 그룹 또는 구독에 적용 됩니다. 템플릿을 배포할 때마다 이전에 적용 된 태그를 바꿉니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

리소스 그룹에 태그를 적용 하려면 PowerShell 또는 Azure CLI 중 하나를 사용 합니다. 태그를 지정할 리소스 그룹에 배포 합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

구독에 태그를 적용 하려면 PowerShell 또는 Azure CLI 중 하나를 사용 합니다. 태그를 지정할 구독에 배포 합니다.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

구독 배포에 대 한 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기](../templates/deploy-to-subscription.md)를 참조 하세요.

다음 템플릿은 개체의 태그를 리소스 그룹 또는 구독에 추가 합니다.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>포털

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure REST API를 통해 태그 작업을 수행 하려면 다음을 사용 합니다.

* [태그-범위에서 만들기 또는 업데이트](/rest/api/resources/tags/createorupdateatscope) (PUT 작업)
* [태그-범위에서 업데이트](/rest/api/resources/tags/updateatscope) (패치 작업)
* [태그-범위에서 가져오기](/rest/api/resources/tags/getatscope) (get 작업)
* [태그-범위에서 삭제](/rest/api/resources/tags/deleteatscope) (삭제 작업)

## <a name="inherit-tags"></a>태그 상속

리소스 그룹 또는 구독에 적용 되는 태그는 리소스에서 상속 되지 않습니다. 구독 또는 리소스 그룹의 태그를 리소스에 적용 하려면 [Azure 정책-태그](tag-policies.md)를 참조 하세요.

## <a name="tags-and-billing"></a>태그 및 청구

태그를 사용하여 청구 데이터를 그룹화할 수 있습니다. 예를 들어 다양한 구성에 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터별로 사용량을 그룹화할 수 있습니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용량과 같이 런타임 환경별로 비용을 분류하는 데 태그를 사용할 수도 있습니다.

태그에 대 한 정보는 [Azure 리소스 사용량 및 요금 카드 api](../../cost-management-billing/manage/usage-rate-card-overview.md) 또는 사용 쉼표로 구분 된 값 (CSV) 파일을 통해 검색할 수 있습니다. [Azure 계정 센터](https://account.azure.com/Subscriptions) 또는 Azure Portal에서 사용량 파일을 다운로드할 수 있습니다. 자세한 내용은 [Azure 청구서 및 일간 사용량 데이터 다운로드 또는 보기](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)를 참조하세요. Azure 계정 센터에서 사용량 파일을 다운로드하는 경우 **버전 2**를 선택합니다. 대금 청구에 태그를 지원하는 서비스의 경우 **태그** 열에 태그가 나타납니다.

REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](/rest/api/billing/)를 참조하세요.

## <a name="limitations"></a>제한 사항

다음 제한 사항이 태그에 적용됩니다.

* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
* 각 리소스, 리소스 그룹 및 구독에는 최대 50 개의 태그 이름/값 쌍이 있을 수 있습니다. 허용 되는 최대 수보다 많은 태그를 적용 해야 하는 경우 태그 값에 JSON 문자열을 사용 합니다. JSON 문자열은 단일 태그 이름에 적용되는 여러 값을 포함할 수 있습니다. 리소스 그룹 또는 구독에는 각각 50 개의 태그 이름/값 쌍이 있는 많은 리소스가 포함 될 수 있습니다.
* 태그 이름은 512자로 제한되며 태그 값은 256자로 제한됩니다. 스토리지 계정에서 태그 이름은 128자로 제한되며 태그 값은 256자로 제한됩니다.
* Cloud Services와 같은 클래식 리소스에는 태그를 적용할 수 없습니다.
* 태그 이름에는 다음 문자를 포함할 수 없습니다: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > 현재 Azure DNS 영역 및 Traffic Manager 서비스는 태그에서 공백 사용을 허용 하지 않습니다.
   >
   > Azure 전면 도어는 태그 이름에서의 사용을 지원 하지 않습니다 `#` .
   >
   > Azure Automation 및 Azure CDN는 리소스에서 15 개의 태그만 지원 합니다.

## <a name="next-steps"></a>다음 단계

* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
* 태깅 전략을 구현 하는 방법에 대 한 권장 사항은 [리소스 명명 및 태그 지정 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)를 참조 하세요.
