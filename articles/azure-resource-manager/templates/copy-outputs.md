---
title: 출력 값의 여러 인스턴스를 정의 합니다.
description: 배포에서 값을 반환할 때 Azure Resource Manager 템플릿에서 복사 작업을 사용 하 여 여러 번 반복 합니다.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 50c4b4b8f301ad88d3dfde98ace1aed4431693db
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583418"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 템플릿의 출력 반복

이 문서에서는 Azure Resource Manager (ARM) 템플릿에서 출력 값을 두 개 이상 만드는 방법을 보여 줍니다. **복사** 요소를 템플릿의 출력 섹션에 추가 하 여 배포 하는 동안 여러 항목을 동적으로 반환할 수 있습니다.

[리소스](copy-resources.md), [리소스의 속성](copy-properties.md)및 [변수와](copy-variables.md)함께 copy를 사용할 수도 있습니다.

## <a name="syntax"></a>구문

Copy 요소의 일반적인 형식은 다음과 같습니다.

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

**Count** 속성은 출력 값에 대해 원하는 반복 횟수를 지정 합니다.

**입력** 속성은 반복할 속성을 지정 합니다. **Input** 속성의 값에서 생성 된 요소의 배열을 만듭니다. 단일 속성 (예: 문자열) 또는 여러 속성이 있는 개체 일 수 있습니다.

## <a name="copy-limits"></a>복사 제한

개수는 800를 초과할 수 없습니다.

개수는 음수일 수 없습니다. 최신 버전의 Azure CLI, PowerShell 또는 REST API를 사용 하 여 템플릿을 배포 하는 경우에는 0이 될 수 있습니다. 특히 다음을 사용 해야 합니다.

* Azure PowerShell **2.6** 이상
* Azure CLI **2.0.74** 이상
* REST API 버전 **2019-05-10** 이상
* 배포 리소스 종류에는 [연결 된 배포](linked-templates.md) 에서 API 버전 **2019-05-10** 이상을 사용 해야 합니다.

이전 버전의 PowerShell, CLI 및 REST API는 count에 대해 0을 지원 하지 않습니다.

## <a name="outputs-iteration"></a>반복 출력

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

* 자습서를 진행 하려면 [자습서: ARM 템플릿을 사용 하 여 여러 리소스 인스턴스 만들기](template-tutorial-create-multiple-instances.md)를 참조 하세요.
* Copy 요소의 다른 용도는 다음을 참조 하세요.
  * [ARM 템플릿의 리소스 반복](copy-resources.md)
  * [ARM 템플릿의 속성 반복](copy-properties.md)
  * [ARM 템플릿의 변수 반복](copy-variables.md)
* 템플릿의 섹션에 대해 알아보려면 [ARM 템플릿 제작](template-syntax.md)을 참조 하세요.
* 템플릿을 배포 하는 방법에 대 한 자세한 내용은 [ARM 템플릿을 사용 하 여 응용 프로그램 배포](deploy-powershell.md)를 참조 하세요.

