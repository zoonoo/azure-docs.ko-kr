---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/11/2019
ms.author: tomfitz
ms.openlocfilehash: 104bd13a0cf97a8605670adde479c2a2eeb29c15
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182585"
---
배포 중 리소스 태그를 추가 합니다 `tags` 요소를 배포 하는 리소스입니다. 태그 이름 및 값을 제공합니다.

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

태그 날짜/시간 값을 설정 하려면 사용 합니다 [utcNow 함수](../articles/azure-resource-manager/resource-group-template-functions-string.md#utcnow)합니다.

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

단일 태그에 여러 값을 저장하려면 값을 나타내는 JSON 문자열을 적용합니다. 전체 JSON 문자열은 256 자를 초과할 수 없는 하나의 태그로 저장 됩니다. 다음 예제에는 JSON 문자열의 여러 값을 포함하는 `CostCenter`라는 단일 태그를 포함합니다.  

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