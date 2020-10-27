---
title: 범위 간 리소스 배포
description: 배포 중에 둘 이상의 범위를 대상으로 하는 방법을 보여 줍니다. 범위는 테 넌 트, 관리 그룹, 구독 및 리소스 그룹 일 수 있습니다.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558939"
---
# <a name="deploy-azure-resources-across-scopes"></a>범위 간 Azure 리소스 배포

Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하면 단일 배포에서 둘 이상의 범위에 배포할 수 있습니다. 사용 가능한 범위는 테 넌 트, 관리 그룹, 구독 및 리소스 그룹입니다. 예를 들어 리소스를 하나의 리소스 그룹에 배포 하 고 동일한 템플릿에서 다른 리소스 그룹에 리소스를 배포할 수 있습니다. 또는 관리 그룹에 리소스를 배포 하 고 해당 관리 그룹 내의 리소스 그룹에 리소스를 배포할 수도 있습니다.

[중첩 또는 연결 된 템플릿을](linked-templates.md) 사용 하 여 배포 작업에 대 한 기본 범위와 다른 범위를 지정 합니다.

## <a name="available-scopes"></a>사용 가능한 범위

배포 작업에 사용 하는 범위에 따라 사용할 수 있는 다른 범위가 결정 됩니다. [테 넌 트](deploy-to-tenant.md), [관리 그룹](deploy-to-management-group.md), [구독](deploy-to-subscription.md)또는 [리소스 그룹](deploy-powershell.md)에 배포할 수 있습니다. 기본 배포 수준에서 계층의 수준을 위로 이동할 수 없습니다. 예를 들어 구독에 배포 하는 경우 관리 그룹에 리소스를 배포 하는 수준을 한 단계씩 실행 할 수 없습니다. 그러나 관리 그룹에 배포 하 고 구독 또는 리소스 그룹에 배포 하는 데 사용할 수 있습니다.

모든 범위에서 템플릿을 배포 하는 사용자에 게는 리소스를 만드는 데 필요한 권한이 있어야 합니다.

## <a name="cross-resource-groups"></a>리소스 그룹 간

부모 템플릿의 리소스 그룹과 다른 리소스 그룹을 대상으로 하려면 [중첩되거나 연결된 템플릿](linked-templates.md)을 사용합니다. 배포 리소스 형식 내에서 중첩된 템플릿을 배포하려는 구독 ID 및 리소스 그룹의 값을 지정합니다. 리소스 그룹은 서로 다른 구독에 존재할 수 있습니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

구독 ID 또는 리소스 그룹을 지정하지 않으면 부모 템플릿의 구독 및 리소스 그룹이 사용됩니다. 모든 리소스 그룹은 배포를 실행하기 전에 존재해야 합니다.

> [!NOTE]
> 단일 배포의 **800개 리소스 그룹** 에만 배포할 수 있습니다. 일반적으로 이 제한 사항으로 인해 부모 템플릿에 지정된 하나의 리소스 그룹 및 중첩되거나 연결된 배포에서 최대 799개의 리소스 그룹에 배포할 수 있습니다. 그러나 부모 템플릿에 중첩되거나 연결된 템플릿만이 포함되고 자체적으로 리소스를 배포하지 않는 경우 중첩되거나 연결된 배포에서 최대 800개의 리소스 그룹이 포함될 수 있습니다.

다음 예제에서는 두 개의 스토리지 계정을 만듭니다. 첫 번째 스토리지 계정은 배포 작업에 지정된 리소스 그룹에 배포됩니다. 두 번째 스토리지 계정은 `secondResourceGroup` 및 `secondSubscriptionID` 매개 변수에 지정된 리소스 그룹에 배포됩니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

`resourceGroup`을 존재하지 않는 리소스 그룹의 이름으로 설정하면 배포에 실패합니다.

위의 템플릿을 테스트하고 결과를 확인하려면 PowerShell 또는 Azure CLI를 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

두 개의 스토리지 계정을 **동일한 구독** 의 두 리소스 그룹에 배포하려면 다음을 사용합니다.

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

두 개의 스토리지 계정을 **두 개의 구독** 에 배포하려면 다음을 사용합니다.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

두 개의 스토리지 계정을 **동일한 구독** 의 두 리소스 그룹에 배포하려면 다음을 사용합니다.

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

두 개의 스토리지 계정을 **두 개의 구독** 에 배포하려면 다음을 사용합니다.

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="cross-subscription-management-group-and-tenant"></a>구독 간, 관리 그룹 및 테 넌 트

구독, 관리 그룹 및 테 넌 트 수준 배포에 대해 서로 다른 범위를 지정 하는 경우 리소스 그룹에 대 한 예제와 같은 중첩 배포를 사용 합니다. 범위를 지정 하는 데 사용 하는 속성은 다를 수 있습니다. 이러한 시나리오는 배포 수준에 대 한 문서에서 설명 합니다. 자세한 내용은 다음을 참조하세요.

* [구독 수준에서 리소스 그룹 및 리소스 만들기](deploy-to-subscription.md)
* [관리 그룹 수준에서 리소스 만들기](deploy-to-management-group.md)
* [테넌트 수준에서 리소스 만들기](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>범위에서 함수를 확인 하는 방법

둘 이상의 범위에 배포 하는 경우 [resourceGroup ()](template-functions-resource.md#resourcegroup) 및 [subscription ()](template-functions-resource.md#subscription) 함수는 템플릿을 지정 하는 방법에 따라 다르게 확인 됩니다. 외부 템플릿에 연결하는 경우 함수는 항상 해당 템플릿에 대한 범위로 확인됩니다. 부모 템플릿 내에서 템플릿을 중첩하는 경우 `expressionEvaluationOptions` 속성을 사용하여 함수에서 부모 템플릿 또는 중첩된 템플릿에 대한 리소스 그룹 및 구독을 확인할지 여부를 지정합니다. 속성을 `inner`로 설정하여 중첩된 템플릿의 범위를 확인합니다. 속성을 `outer`로 설정하여 부모 템플릿의 범위를 확인합니다.

다음 표에서는 함수가 부모 또는 포함 리소스 그룹 및 구독을 확인하는지 여부를 보여줍니다.

| 템플릿 형식 | 범위 | 해결 방법 |
| ------------- | ----- | ---------- |
| 중첩        | outer(기본값) | 부모 리소스 그룹 |
| 중첩        | inner | 하위 리소스 그룹 |
| 연결        | 해당 없음   | 하위 리소스 그룹 |

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)은 다음을 보여줍니다.

* 기본(외부) 범위를 사용하는 중첩 템플릿
* 내부 범위를 포함하는 중첩 템플릿
* 연결된 템플릿

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

위의 템플릿을 테스트하고 결과를 확인하려면 PowerShell 또는 Azure CLI를 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

위 예제의 출력은 다음과 같습니다.

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

위 예제의 출력은 다음과 같습니다.

```output
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



## <a name="next-steps"></a>다음 단계

* 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조하세요.
* SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 프라이빗 템플릿 배포](secure-template-with-sas-token.md)를 참조하세요.
