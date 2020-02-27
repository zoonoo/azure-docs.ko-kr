---
title: 출력 값의 여러 인스턴스를 정의 합니다.
description: 배포에서 값을 반환할 때 Azure Resource Manager 템플릿에서 복사 작업을 사용 하 여 여러 번 반복 합니다.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624775"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 출력 반복

이 문서에서는 Azure Resource Manager 템플릿에서 출력에 대해 둘 이상의 값을 만드는 방법을 보여 줍니다. **복사** 요소를 템플릿의 출력 섹션에 추가 하 여 배포 하는 동안 여러 항목을 동적으로 반환할 수 있습니다.

[리소스](copy-resources.md), [리소스의 속성](copy-properties.md)및 [변수와](copy-variables.md)함께 copy를 사용할 수도 있습니다.

## <a name="outputs-iteration"></a>반복 출력

Copy 요소의 일반적인 형식은 다음과 같습니다.

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**Count** 속성은 출력 값에 대해 원하는 반복 횟수를 지정 합니다.

**입력** 속성은 반복할 속성을 지정 합니다. **Input** 속성의 값에서 생성 된 요소의 배열을 만듭니다. 단일 속성 (예: 문자열) 또는 여러 속성이 있는 개체 일 수 있습니다.

다음 예에서는 다양 한 저장소 계정을 만들고 각 저장소 계정에 대 한 끝점을 반환 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

위의 템플릿은 다음 값을 포함 하는 배열을 반환 합니다.

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

다음 예에서는 새 저장소 계정에서 3 개의 속성을 반환 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

앞의 예제에서는 다음 값을 포함 하는 배열을 반환 합니다.

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>다음 단계

* 자습서를 살펴보려면 [자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](template-tutorial-create-multiple-instances.md)를 참조하세요.
* Copy 요소의 다른 용도는 다음을 참조 하세요.
  * [Azure Resource Manager 템플릿의 리소스 반복](copy-resources.md)
  * [Azure Resource Manager 템플릿의 속성 반복](copy-properties.md)
  * [Azure Resource Manager 템플릿의 변수 반복](copy-variables.md)
* 템플릿 섹션에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](template-syntax.md)을 참조하세요.
* 템플릿 배포 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 애플리케이션 배포](deploy-powershell.md)를 참조하세요.

