---
title: 변수의 여러 인스턴스 정의
description: Azure 리소스 관리자 템플릿에서 복사 작업을 사용하여 변수를 만들 때 여러 번 반복합니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153304"
---
# <a name="variable-iteration-in-arm-templates"></a>ARM 템플릿의 가변 반복

이 문서에서는 ARM(Azure Resource Manager) 템플릿에서 변수에 대해 두 개 이상의 값을 만드는 방법을 보여 줍니다. **복사** 요소를 템플릿의 변수 섹션에 추가하면 배포 하는 동안 변수에 대 한 항목 수를 동적으로 설정할 수 있습니다. 또한 템플릿 구문을 반복하지 않아도 됩니다.

[리소스, 리소스의](copy-resources.md) [속성](copy-properties.md)및 출력이 있는 복사본을 사용할 수도 [있습니다.](copy-outputs.md)

## <a name="variable-iteration"></a>변수 반복

복사 요소에는 다음과 같은 일반적인 형식이 있습니다.

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**name** 속성은 루프를 식별하는 모든 값입니다. **count** 속성은 변수에 대해 원하는 반복 수를 지정합니다.

**input** 속성은 반복할 속성을 지정합니다. **입력** 속성의 값에서 생성 된 요소의 배열을 만듭니다. 단일 속성(예: 문자열) 또는 여러 속성이 있는 개체일 수 있습니다.

다음 예제에서는 문자열 값의 배열을 만드는 방법을 보여 주며 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

앞의 템플릿은 다음 값을 가진 배열을 반환합니다.

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

다음 예제에서는 이름, diskSizeGB 및 diskIndex의 세 가지 속성을 가진 개체 배열을 만드는 방법을 보여 주며 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

앞의 예제는 다음 값을 가진 배열을 반환합니다.

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> 가변 반복은 오프셋 인수를 지원합니다. 오프셋은 copyIndex('diskNames', 1)와 같은 반복 이름 의 이름을 따라 와야 합니다. 오프셋 값을 제공하지 않으면 첫 번째 인스턴스의 기본값은 0으로 설정됩니다.
>

변수 내에서 복사 요소를 사용할 수도 있습니다. 다음 예제는 배열이 해당 값 중 하나로 있는 개체를 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

앞의 예제는 다음 값을 가진 객체를 반환합니다.

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

다음 예제에서는 변수가 있는 복사본을 사용할 수 있는 여러 가지 방법을 보여 주입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>복사 제한

개수는 800을 초과할 수 없습니다.

개수는 음수일 수 없습니다. Azure PowerShell 2.6 이상, Azure CLI 2.0.74 이상 또는 REST API 버전 **2019-05-10** 이상과 함께 템플릿을 배포하는 경우 개수를 0으로 설정할 수 있습니다. 이전 버전의 PowerShell, CLI 및 REST API는 카운트에 대해 0을 지원하지 않습니다.

## <a name="example-templates"></a>예제 템플릿

다음 예제는 변수에 대해 두 개 이상의 값을 만드는 일반적인 시나리오를 보여 주며 있습니다.

|템플릿  |설명  |
|---------|---------|
|[변수 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |변수를 반복하는 다양한 방법을 보여 줍니다. |
|[다중 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |네트워크 보안 그룹에 여러 보안 규칙을 배포합니다. 매개 변수에서 보안 규칙을 구성합니다. 매개 변수는 [여러 NSG 매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)을 참조합니다. |

## <a name="next-steps"></a>다음 단계

* 자습서를 진행하려면 [자습서: ARM 템플릿을 사용하여 여러 리소스 인스턴스만들기](template-tutorial-create-multiple-instances.md)를 참조하십시오.
* 복사 요소의 다른 용도는 다음을 참조하십시오.
  * [ARM 템플릿의 리소스 반복](copy-resources.md)
  * [ARM 템플릿의 속성 반복](copy-properties.md)
  * [ARM 템플릿의 출력 반복](copy-outputs.md)
* 템플릿의 섹션에 대해 알아보려면 ARM [템플릿 작성](template-syntax.md)을 참조하십시오.
* 템플릿을 배포하는 방법을 알아보려면 [ARM 템플릿을 사용하여 응용 프로그램 배포를](deploy-powershell.md)참조하세요.

