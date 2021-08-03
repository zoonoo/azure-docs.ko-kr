---
title: 템플릿의 자식 리소스
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 자식 리소스의 이름 및 형식을 설정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 48b44cad1fef7f09fe07fbb1c0e416ce947e586a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969557"
---
# <a name="set-name-and-type-for-child-resources"></a>자식 리소스에 대한 이름 및 형식 설정

자식 리소스는 다른 리소스의 컨텍스트 내에만 존재하는 리소스입니다. 예를 들어 [가상 머신](/azure/templates/microsoft.compute/virtualmachines) 없이는 [가상 머신 확장](/azure/templates/microsoft.compute/virtualmachines/extensions)이 존재할 수 없습니다. 확장 리소스는 가상 머신의 자식입니다.

각 부모 리소스는 특정 리소스 종류만 자식 리소스로 허용합니다. 자식 리소스의 리소스 종류는 부모 리소스에 대한 리소스 종류를 포함합니다. 예를 들어 `Microsoft.Web/sites/config`와 `Microsoft.Web/sites/extensions`는 둘 다 `Microsoft.Web/sites`의 자식 리소스입니다. 허용되는 리소스 종류는 부모 리소스의 [템플릿 스키마](https://github.com/Azure/azure-resource-manager-schemas)에서 지정됩니다.

ARM 템플릿(Azure Resource Manager 템플릿)에서 부모 리소스 내에서 또는 부모 리소스 외부에서 자식 리소스를 지정할 수 있습니다. 리소스 이름 및 리소스 종류에 대해 제공하는 값은 자식 리소스가 부모 리소스의 내부에 정의되는지 아니면 외부에 정의되는지에 따라 달라집니다.

## <a name="within-parent-resource"></a>부모 리소스 내에서

다음 예에서는 부모 리소스의 리소스 속성 내에 포함된 자식 리소스를 보여 줍니다.

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

자식 리소스는 5개 수준 깊이까지만 정의할 있습니다.

부모 리소스 종류 내에서 정의된 경우 유형 및 이름 값을 슬래시 없이 단일 세그먼트로 서식을 지정합니다.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

다음 예는 가상 네트워크와 서브넷을 보여 줍니다. 서브넷은 가상 네트워크에 대한 리소스 배열에 포함되어 있습니다. 이름은 **Subnet1** 로 지정되며 유형은 **서브넷** 으로 설정됩니다. 부모 리소스는 자식 리소스를 배포하기 전에 존재해야 하므로 자식 리소스가 부모 리소스에 종속된 것으로 표시되어 있습니다.

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

전체 리소스 종류는 여전히 `Microsoft.Network/virtualNetworks/subnets`입니다. `Microsoft.Network/virtualNetworks/`는 부모 리소스 종류에서 유추되므로 입력하지 않습니다.

자식 리소스 이름은 **Subnet1** 로 설정되지만 전체 이름에는 부모 이름이 포함됩니다. **VNet1** 은 부모 리소스에서 유추되므로 입력하지 않습니다.

## <a name="outside-parent-resource"></a>부모 리소스 외부에

다음 예에서는 부모 리소스 외부의 자식 리소스를 보여 줍니다. 부모 리소스가 동일한 템플릿에서 배포되지 않는 경우 또는 [copy](copy-resources.md)를 사용하여 둘 이상의 자식 리소스를 만들려는 경우, 이 방법을 사용할 수 있습니다.

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

부모 리소스 외부에 정의된 경우 부모 유형 및 이름을 포함하도록 슬래시를 사용하여 유형의 서식을 지정합니다.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

다음 예에서는 둘 다 루트 수준에서 정의된 가상 네트워크 및 서브넷을 보여 줍니다. 서브넷은 가상 네트워크에 대한 리소스 배열 내에 포함되지 않습니다. 이름은 **VNet1/Subnet1** 로 지정되며 유형은 `Microsoft.Network/virtualNetworks/subnets`서브넷으로 설정됩니다. 부모 리소스는 자식 리소스를 배포하기 전에 존재해야 하므로 자식 리소스가 부모 리소스에 종속된 것으로 표시되어 있습니다.

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

* ARM 템플릿을 만드는 방법에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
* 리소스를 참조할 때 리소스 이름 형식에 대한 자세한 내용은 [참조 함수](template-functions-resource.md#reference)를 참조하세요.