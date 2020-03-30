---
title: 템플릿의 하위 리소스
description: Azure 리소스 관리자 템플릿에서 자식 리소스의 이름 및 형식을 설정하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 6de2b476fe19a057a62e4a54963dd8fde0d11579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207896"
---
# <a name="set-name-and-type-for-child-resources"></a>자식 리소스의 이름 및 유형 설정

하위 리소스는 다른 리소스의 컨텍스트 내에만 있는 리소스입니다. 예를 들어 [가상 컴퓨터 확장은 가상](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) [시스템](/azure/templates/microsoft.compute/2019-03-01/virtualmachines)없이 존재할 수 없습니다. 확장 리소스는 가상 시스템의 자식입니다.

리소스 관리자 템플릿에서 상위 리소스 내에서 또는 상위 리소스 외부에서 자식 리소스를 지정할 수 있습니다. 다음 예제에서는 상위 리소스의 리소스 속성에 포함된 자식 리소스를 보여 주습니다.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

다음 예제에서는 상위 리소스 외부의 자식 리소스를 보여 주습니다. 상위 리소스가 동일한 템플릿에 배포되지 않았거나 [복사본을](copy-resources.md) 사용하여 두 개 이상의 자식 리소스를 만들려는 경우 이 방법을 사용할 수 있습니다.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

리소스 이름 및 형식에 대해 제공하는 값은 하위 리소스가 상위 리소스 내부 또는 외부에 정의되어 있는지 여부에 따라 다릅니다.

## <a name="within-parent-resource"></a>상위 리소스 내에서

상위 리소스 유형 내에서 정의하면 슬래시 없이 형식 및 이름 값을 단일 단어로 서식을 지정합니다.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

다음 예제에서는 가상 네트워크와 서브넷을 보여 주습니다. 서브넷은 가상 네트워크의 리소스 배열에 포함됩니다. 이름은 **Subnet1로** 설정되고 형식은 **서브넷으로 설정됩니다.** 자식 리소스를 배포하기 전에 부모 리소스가 있어야 하기 때문에 하위 리소스가 부모 리소스에 종속된 것으로 표시됩니다.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

전체 리소스 유형은 여전히 **Microsoft.Network/가상 네트워크/서브넷입니다.** **Microsoft.Network/virtualNetworks/부모** 리소스 유형에서 가정 하기 때문에 제공 하지 않습니다.

자식 리소스 이름은 **Subnet1로** 설정되지만 전체 이름에는 상위 이름이 포함됩니다. VNet1은 상위 리소스에서 가정하기 때문에 **VNet1을** 제공하지 않습니다.

## <a name="outside-parent-resource"></a>외부 상위 리소스

상위 리소스 외부에서 정의된 경우 형식과 슬래시를 사용하여 부모 유형 과 이름을 포함하도록 서식을 지정합니다.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

다음 예제에서는 루트 수준에서 정의된 가상 네트워크와 서브넷을 보여 주습니다. 서브넷은 가상 네트워크의 리소스 배열에 포함되지 않습니다. 이름은 **VNet1/Subnet1로** 설정되고 형식은 **Microsoft.Network/가상 네트워크/서브넷으로 설정됩니다.** 자식 리소스를 배포하기 전에 부모 리소스가 있어야 하기 때문에 하위 리소스가 부모 리소스에 종속된 것으로 표시됩니다.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>다음 단계

* Azure 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](template-syntax.md)을 참조하세요.

* 리소스를 참조할 때 리소스 이름의 형식에 대해 알아보려면 [참조 함수를](template-functions-resource.md#reference)참조하십시오.
