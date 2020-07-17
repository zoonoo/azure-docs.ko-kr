---
title: 템플릿 리소스 위치
description: Azure Resource Manager 템플릿에서 리소스 위치를 설정 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: e0bf0ac79c7e304a61561808dc2290ceb4a379e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84672683"
---
# <a name="set-resource-location-in-arm-template"></a>ARM 템플릿에서 리소스 위치 설정

ARM (Azure Resource Manager) 템플릿을 배포할 때 각 리소스의 위치를 제공 해야 합니다. 위치가 리소스 그룹 위치와 동일한 위치가 아니어도 됩니다.

## <a name="get-available-locations"></a>사용 가능한 위치 가져오기

다양한 리소스 형식이 다양한 위치에서 지원됩니다. 리소스 종류에 대해 지원 되는 위치를 가져오려면 Azure PowerShell 또는 Azure CLI를 사용 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Location 매개 변수 사용

템플릿을 배포할 때 유연성을 허용 하려면 매개 변수를 사용 하 여 리소스의 위치를 지정 합니다. 매개 변수의 기본값을로 설정 `resourceGroup().location` 합니다.

다음 예제에서는 매개 변수로 지정된 위치에 배포되는 스토리지 계정을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* 템플릿 함수의 전체 목록을 보려면 [Azure 리소스 관리자 템플릿 함수](template-functions.md)를 참조하세요.
* 템플릿 파일에 대 한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
