---
title: 템플릿 리소스 위치
description: Azure 리소스 관리자 템플릿 (ARM 템플릿)에서 리소스 위치를 설정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b2eba8e48a9b5ccc1ddb731690e99dcbbaa8f6fb
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108321630"
---
# <a name="set-resource-location-in-arm-template"></a>ARM 템플릿에서 리소스 위치 설정

Azure 리소스 관리자 템플릿 (ARM 템플릿)을 배포하는 경우 각 리소스의 위치를 제공해야 합니다. 위치가 리소스 그룹 위치와 동일한 위치가 아니어도 됩니다.

## <a name="get-available-locations"></a>사용 가능한 위치 가져오기

다양한 리소스 형식이 다양한 위치에서 지원됩니다. 리소스 종류에 대해 지원되는 위치를 가져오려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

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

## <a name="use-location-parameter"></a>위치 매개 변수 사용

템플릿을 배포할 때 유연성을 허용하려면 매개 변수를 사용하여 리소스의 위치를 지정합니다. 매개 변수의 기본값을 `resourceGroup().location`로 가져옵니다.

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

* 템플릿에서 사용할 수 있는 함수 전체 목록은 [ARM 템플릿 함수](template-functions.md)를 참조하세요.
* 템플릿 파일에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
