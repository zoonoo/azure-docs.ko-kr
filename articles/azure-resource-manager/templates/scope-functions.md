---
title: 범위 지정 배포의 템플릿 함수
description: 범위 지정 배포에서 템플릿 함수를 확인 하는 방법을 설명 합니다. 범위는 테 넌 트, 관리 그룹, 구독 및 리소스 그룹 일 수 있습니다.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb02a3dc808604a80fd9943138c1cd0d8648904e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681587"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>배포 범위의 ARM 템플릿 함수

Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 리소스 그룹, 구독, 관리 그룹 또는 테 넌 트에 배포할 수 있습니다. 일반적으로 [ARM 템플릿 함수](template-functions.md) 는 모든 범위에 대해 동일 하 게 작동 합니다. 이 문서에서는 범위에 따라 일부 함수에 존재 하는 차이점을 설명 합니다.

## <a name="supported-functions"></a>지원되는 함수

다른 범위에 배포할 때는 다음과 같은 몇 가지 중요 한 사항을 고려해 야 합니다.

* [ResourceGroup ()](template-functions-resource.md#resourcegroup) 함수는 리소스 그룹 배포에 대해 **지원** 됩니다.
* [Subscription ()](template-functions-resource.md#subscription) 함수는 리소스 그룹 및 구독 배포에 대해 **지원** 됩니다.
* [Reference ()](template-functions-resource.md#reference) 및 [list ()](template-functions-resource.md#list) 함수는 모든 범위에 대해 **지원** 됩니다.
* [ResourceId ()](template-functions-resource.md#resourceid) 를 사용 하 여 리소스 그룹에 배포 된 리소스에 대 한 ID를 가져옵니다.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* [Subscriptionresourceid ()](template-functions-resource.md#subscriptionresourceid) 함수를 사용 하 여 구독에 배포 된 리소스에 대 한 ID를 가져옵니다.

  예를 들어 구독에 배포 되는 정책 정의에 대 한 리소스 ID를 가져오려면 다음을 사용 합니다.

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* 관리 그룹의 확장으로 구현 된 리소스에는 [Extensionresourceid ()](template-functions-resource.md#extensionresourceid) 함수를 사용 합니다. 관리 그룹에 배포 되는 사용자 지정 정책 정의는 관리 그룹의 확장입니다.

  관리 그룹 수준에서 사용자 지정 정책 정의에 대 한 리소스 ID를 가져오려면 다음을 사용 합니다.
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* [Tenantresourceid](template-functions-resource.md#tenantresourceid) 함수를 사용 하 여 테 넌 트에 배포 된 리소스에 대 한 ID를 가져옵니다. 기본 제공 정책 정의는 테 넌 트 수준 리소스입니다. 관리 그룹 수준에서 기본 제공 정책을 할당할 때 tenantResourceId 함수를 사용 합니다.

  기본 제공 정책 정의에 대 한 리소스 ID를 가져오려면 다음을 사용 합니다.
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>범위에서 함수 해결

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
