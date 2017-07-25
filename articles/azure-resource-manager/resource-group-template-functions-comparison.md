---
title: "Azure Resource Manager 템플릿 함수 - 비교 | Microsoft Docs"
description: "Azure Resource Manager 템플릿에서 값을 비교하는 데 사용할 수 있는 함수에 대해 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: d993bd5cae5fa2f0a6eda999c95b10bd1c268be2
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿용 비교 함수

Resource Manager는 템플릿에서 비교를 수행하기 위한 몇 가지 함수를 제공합니다.

* [equals](#equals)
* [less](#less)
* [lessOrEquals](#lessorequals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)

<a id="equals" />

## <a name="equals"></a>equals
`equals(arg1, arg2)`

두 값이 서로 일치하는지 여부를 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int, 문자열, 배열 또는 개체 |같은지 확인할 첫 번째 값입니다. |
| arg2 |예 |int, 문자열, 배열 또는 개체 |같은지 확인할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

값이 같으면 **True**를 반환하고 같지 않으면 **False**를 반환합니다.

### <a name="remarks"></a>설명

equals 함수는 종종 `condition` 요소와 함께 리소스 배포 여부를 테스트하는 데 사용됩니다.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>예제

이 예제 템플릿에서는 다른 형식의 값이 같은지 확인합니다. 모든 기본값은 True를 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

기본값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | 형식 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

<a id="less" />

## <a name="less"></a>less
`less(arg1, arg2)`

첫 번째 값이 두 번째 값보다 작은지 여부를 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int 또는 문자열 |less 비교에 사용할 첫 번째 값입니다. |
| arg2 |예 |int 또는 문자열 |less 비교에 사용할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 작으면 **True**를 반환하고 작지 않으면 **False**를 반환합니다.

### <a name="example"></a>예제

이 예제 템플릿에서는 한 값이 다른 값보다 작은지 여부를 확인합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

기본값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | 형식 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

<a id="lessorequals" />

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

첫 번째 값이 두 번째 값보다 작거나 같은지 여부를 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int 또는 문자열 |less 또는 equals 비교에 사용할 첫 번째 값입니다. |
| arg2 |예 |int 또는 문자열 |less 또는 equals 비교에 사용할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 작거나 같으면 **True**를 반환하고 크면 **False**를 반환합니다.

### <a name="example"></a>예제

이 예제 템플릿에서는 한 값이 다른 값보다 작거나 같은지 여부를 확인합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

기본값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | 형식 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

<a id="greater" />

## <a name="greater"></a>greater
`greater(arg1, arg2)`

첫 번째 값이 두 번째 값보다 큰지 여부를 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int 또는 문자열 |greater 비교에 사용할 첫 번째 값입니다. |
| arg2 |예 |int 또는 문자열 |greater 비교에 사용할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 크면 **True**를 반환하고 크지 않으면 **False**를 반환합니다.

### <a name="example"></a>예제

이 예제 템플릿에서는 한 값이 다른 값보다 큰지 여부를 확인합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

기본값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | 형식 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

<a id="greaterorequals" />

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

첫 번째 값이 두 번째 값보다 크거나 같은지 여부를 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int 또는 문자열 |greater 또는 equal 비교에 사용할 첫 번째 값입니다. |
| arg2 |예 |int 또는 문자열 |greater 또는 equal 비교에 사용할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 크거나 같으면 **True**를 반환하고 작으면 **False**를 반환합니다.

### <a name="example"></a>예제

이 예제 템플릿에서는 한 값이 다른 값보다 크거나 같은지 여부를 확인합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

기본값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | 형식 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |


## <a name="next-steps"></a>다음 단계
* Azure Resource Manager 템플릿의 섹션에 대한 설명은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.
* 만든 템플릿을 배포하는 방법을 보려면 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.


