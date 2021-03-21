---
title: 템플릿에서 리소스 선언
description: Azure Resource Manager 템플릿 (ARM 템플릿)에 배포할 리소스를 선언 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745652"
---
# <a name="resource-declaration-in-arm-templates"></a>ARM 템플릿의 리소스 선언

Azure Resource Manager 템플릿 (ARM 템플릿)을 통해 리소스를 배포 하려면 리소스 선언을 추가 합니다. `resources`JSON 템플릿에 배열을 사용 하거나 `resource` Bicep에 대 한 키워드를 사용 합니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>리소스 유형 및 버전 설정

템플릿에 리소스를 추가 하는 경우 리소스 유형 및 API 버전을 설정 하 여 시작 합니다. 이러한 값은 리소스에 사용할 수 있는 다른 속성을 결정 합니다.

다음 예제에서는 저장소 계정에 대 한 리소스 유형 및 API 버전을 설정 하는 방법을 보여 줍니다. 예제에는 전체 리소스 선언이 표시 되지 않습니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Bicep의 경우 리소스에 대 한 기호화 된 이름을 설정 합니다. 앞의 예제에서 기호화 된 이름은 `sa` 입니다. 기호화 된 이름에 모든 값을 사용할 수 있지만 템플릿의 다른 리소스, 매개 변수 또는 변수와 같을 수는 없습니다. 기호화 된 이름이 리소스 이름과 동일 하지 않습니다. 심볼 이름을 사용 하 여 템플릿의 다른 부분에 있는 리소스를 쉽게 참조할 수 있습니다.

## <a name="set-resource-name"></a>리소스 이름 설정

각 리소스에는 이름이 있습니다. 리소스 이름을 설정할 때 [리소스 이름에 대 한 규칙 및 제한 사항](../management/resource-name-rules.md)에 주의 하십시오.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>위치 설정

많은 리소스에 위치가 필요 합니다. 리소스에 intellisense 또는 [템플릿 참조](/azure/templates/)를 통해 위치가 필요한 지 여부를 확인할 수 있습니다. 다음 예에서는 저장소 계정에 사용 되는 위치 매개 변수를 추가 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

자세한 내용은 [ARM 템플릿에서 리소스 위치 설정](resource-location.md)을 참조 하세요.

## <a name="set-tags"></a>태그 설정

배포 하는 동안 리소스에 태그를 적용할 수 있습니다. 태그를 통해 배포 된 리소스를 논리적으로 구성할 수 있습니다. 태그를 지정할 수 있는 다양 한 방법의 예는 [ARM 템플릿 태그](../management/tag-resources.md#arm-templates)를 참조 하세요.

## <a name="set-resource-specific-properties"></a>리소스 관련 속성 설정

위의 속성은 대부분의 리소스 형식에 대해 일반적입니다. 이러한 값을 설정한 후에는 배포 하는 리소스 종류와 관련 된 속성을 설정 해야 합니다.

Intellisense 또는 [템플릿 참조](/azure/templates/) 를 사용 하 여 사용 가능한 속성과 필요한 속성을 확인 합니다. 다음 예에서는 저장소 계정에 대 한 나머지 속성을 설정 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>다음 단계

* 리소스를 조건부로 배포 하려면 [ARM 템플릿에서 조건부 배포](conditional-resource-deployment.md)를 참조 하세요.
* 리소스 종속성을 설정 하려면 [ARM 템플릿에서 리소스를 배포 하는 순서 정의](define-resource-dependency.md)를 참조 하세요.
