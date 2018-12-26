---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: 5e483ecfcbddfcf5aa7f8a41c1ee75136c86b656
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440295"
---
배포 중 리소스에 태그를 지정하려면 배포 중인 리소스에 `tags` 요소를 추가합니다. 태그 이름 및 값을 제공합니다.

### <a name="apply-a-literal-value-to-the-tag-name"></a>태그 이름에 리터럴 값 적용
다음 예제에서는 리터럴 값으로 설정된 두 개의 태그(`Dept` 및 `Environment`)가 있는 저장소 계정을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

### <a name="apply-an-object-to-the-tag-element"></a>개체를 태그 요소에 적용
여러 태그를 저장하는 개체 매개 변수를 정의하고 해당 개체를 태그 요소에 적용할 수 있습니다. 개체의 각 속성은 리소스에 대한 별도의 태그가 됩니다. 다음 예제에는 태그 요소에 적용되는 `tagValues`라는 매개 변수가 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
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

### <a name="apply-a-json-string-to-the-tag-name"></a>태그 이름에 JSON 문자열 적용

단일 태그에 여러 값을 저장하려면 값을 나타내는 JSON 문자열을 적용합니다. 전체 JSON 문자열은 256자를 초과할 수 없는 하나의 태그로 저장됩니다. 다음 예제에는 JSON 문자열의 여러 값을 포함하는 `CostCenter`라는 단일 태그를 포함합니다.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```