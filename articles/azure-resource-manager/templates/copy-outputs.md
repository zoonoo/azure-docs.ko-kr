---
title: 출력 값의 여러 인스턴스 정의
description: Azure Resource Manager 템플릿에서 복사 작업을 사용하여 배포에서 값을 반환할 때 여러 번 반복합니다.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617834"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 템플릿의 출력 반복

이 문서에서는 ARM(Azure Resource Manager) 템플릿에서 출력에 대해 두 개 이상의 값을 만드는 방법을 보여 줍니다. **복사** 요소를 템플릿의 출력 섹션에 추가하면 배포 중에 여러 항목을 동적으로 반환할 수 있습니다.

[리소스, 리소스의](copy-resources.md)속성 및 [변수가 있는](copy-variables.md) [복사본을](copy-properties.md)사용할 수도 있습니다.

## <a name="outputs-iteration"></a>출력 반복

복사 요소에는 다음과 같은 일반적인 형식이 있습니다.

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

**count** 속성은 출력 값에 대해 원하는 반복 수를 지정합니다.

**input** 속성은 반복할 속성을 지정합니다. **입력** 속성의 값에서 생성 된 요소의 배열을 만듭니다. 단일 속성(예: 문자열) 또는 여러 속성이 있는 개체일 수 있습니다.

다음 예제는 가변 수의 저장소 계정을 만들고 각 저장소 계정에 대한 끝점을 반환합니다.

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

앞의 템플릿은 다음 값을 가진 배열을 반환합니다.

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

다음 예제는 새 저장소 계정에서 세 개의 속성을 반환 합니다.

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

앞의 예제는 다음 값을 가진 배열을 반환합니다.

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

* 자습서를 진행하려면 [자습서: ARM 템플릿을 사용하여 여러 리소스 인스턴스만들기](template-tutorial-create-multiple-instances.md)를 참조하십시오.
* 복사 요소의 다른 용도는 다음을 참조하십시오.
  * [ARM 템플릿의 리소스 반복](copy-resources.md)
  * [ARM 템플릿의 속성 반복](copy-properties.md)
  * [ARM 템플릿의 가변 반복](copy-variables.md)
* 템플릿의 섹션에 대해 알아보려면 ARM [템플릿 작성](template-syntax.md)을 참조하십시오.
* 템플릿을 배포하는 방법을 알아보려면 [ARM 템플릿을 사용하여 응용 프로그램 배포를](deploy-powershell.md)참조하세요.

