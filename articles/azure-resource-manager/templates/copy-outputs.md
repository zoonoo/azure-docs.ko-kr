---
title: 출력 값의 여러 인스턴스 정의
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 복사 작업을 사용하여 배포에서 값을 반환할 때 여러 번 반복합니다.
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 880ed42afdbb2082821c216a50da438ec45bd680
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954680"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 템플릿의 출력 반복

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에서 출력에 대한 값을 두 개 이상 만드는 방법을 보여줍니다. 템플릿의 출력 섹션에 복사 루프를 추가하면 배포하는 동안 여러 항목을 동적으로 반환할 수 있습니다.

[리소스](copy-resources.md), [리소스의 속성](copy-properties.md) 및 [변수](copy-variables.md)와 함께 복사 루프를 사용할 수도 있습니다.

## <a name="syntax"></a>Syntax

`copy` 요소를 템플릿의 출력 섹션에 추가하여 여러 항목을 반환합니다. 복사 요소의 일반적인 형식은 다음과 같습니다.

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

`count` 속성은 출력 값에 대해 원하는 반복 횟수를 지정합니다.

`input` 속성은 반복할 속성을 지정합니다. `input` 속성의 값에서 생성된 요소 배열을 만듭니다. 단일 속성(예: 문자열) 또는 여러 속성이 있는 개체일 수 있습니다.

## <a name="copy-limits"></a>복사 제한

개수는 800개를 초과할 수 없습니다.

개수는 음수가 될 수 없습니다. 최신 버전의 Azure CLI, PowerShell 또는 REST API를 사용하여 템플릿을 배포하는 경우에는 0이 될 수 있습니다. 특히 다음을 사용해야 합니다.

- Azure PowerShell **2.6** 이상
- Azure CLI **2.0.74** 이상
- REST API 버전 **2019-05-10** 이상
- [연결된 배포](linked-templates.md)는 배포 리소스 유형에 API 버전 **2019-05-10** 이상을 사용해야 합니다.

이전 버전의 PowerShell, CLI 및 REST API는 개수에 0을 지원하지 않습니다.

## <a name="outputs-iteration"></a>출력 반복

다음 예제에서는 다양한 수의 스토리지 계정을 만들고 각 스토리지 계정에 대한 엔드포인트를 반환합니다.

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

이전 템플릿은 다음 값을 가진 배열을 반환합니다.

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

다음 예제에서는 새 스토리지 계정에서 세 가지 속성을 반환합니다.

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

앞의 예제에서는 다음 값을 가진 배열을 반환합니다.

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

- 자습서를 살펴보려면 [자습서: ARM 템플릿을 사용하여 여러 리소스 인스턴스 만들기](template-tutorial-create-multiple-instances.md)를 참조하세요.
- 복사 루프의 다른 용도는 다음을 참조하세요.
  - [ARM 템플릿의 리소스 반복](copy-resources.md)
  - [ARM 템플릿의 속성 반복](copy-properties.md)
  - [ARM 템플릿의 변수 반복](copy-variables.md)
- 템플릿의 섹션에 대해 알아보려면 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
- 템플릿을 배포하는 방법을 자세히 알아보려면 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.