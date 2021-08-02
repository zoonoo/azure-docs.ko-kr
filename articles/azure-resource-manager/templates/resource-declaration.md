---
title: 템플릿의 리소스 선언
description: Azure Resource Manager 템플릿(ARM 템플릿)에 배포하는 리소스를 선언하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 05/11/2021
ms.openlocfilehash: 7d7bae8adec81aa3344c5c571f0e40556928a1a9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960128"
---
# <a name="resource-declaration-in-arm-templates"></a>ARM 템플릿의 리소스 선언

Azure Resource Manager 템플릿 (ARM 템플릿)을 통해 리소스를 배포하려면 리소스 선언을 추가합니다. JSON 템플릿에서 `resources` 배열을 사용합니다.

## <a name="set-resource-type-and-version"></a>리소스 유형 및 버전 설정

템플릿에 리소스를 추가하는 경우 리소스 유형 및 API 버전을 설정하여 시작합니다. 이러한 값은 리소스에 사용할 수 있는 다른 속성을 결정합니다.

다음 예제에서는 스토리지 계정에 리소스 유형 및 API 버전을 설정하는 방법을 보여 줍니다. 예제에는 전체 리소스 선언이 표시되지 않습니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

## <a name="set-resource-name"></a>리소스 이름 설정

각 리소스는 하나의 이름을 갖습니다. 리소스 이름을 설정할 때, [리소스 이름에 대한 규칙 및 제한 사항](../management/resource-name-rules.md)에 주의하십시오.

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

## <a name="set-location"></a>위치 설정

많은 리소스에는 위치가 필요합니다. 리소스에 intellisense 또는 [템플릿 참조](/azure/templates/)를 통해 위치가 필요한지 여부를 판단할 수 있습니다. 다음 예제에서는 스토리지 계정에 사용되는 위치 매개 변수를 추가합니다.

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

자세한 내용은 [ARM 템플릿에서 리소스 위치 설정](resource-location.md)을 참조하세요.

## <a name="set-tags"></a>태그 설정

배포 중 리소스에 태그를 적용할 수 있습니다. 태그를 통해 배포된 리소스를 논리적으로 구성할 수 있습니다. 태그를 지정할 수 있는 다양한 방법의 예는 [ARM 템플릿 태그](../management/tag-resources.md#arm-templates)를 참조하세요.

## <a name="set-resource-specific-properties"></a>리소스 관련 속성 설정

위의 속성은 대부분의 리소스 형식에 대해 일반적입니다. 이러한 값을 설정한 후에는 배포하고 있는 리소스 유형과 관련된 속성을 설정해야 합니다.

Intellisense 또는 [템플릿 참조](/azure/templates/)를 사용하여 사용 가능한 속성과 필요한 속성을 판단합니다. 다음 예제에서는 스토리지 계정에 나머지 속성을 설정합니다.

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

## <a name="next-steps"></a>다음 단계

* 리소스를 조건부로 배포하려면, [ARM 템플릿에서 조건부 배포](conditional-resource-deployment.md)를 참조하세요.
* 리소스 종속성을 설정하려면, [ARM 템플릿에서 리소스를 배포하는 순서 정의](./resource-dependency.md)를 참조하세요.