---
title: 템플릿 함수-numeric
description: Azure Resource Manager 템플릿에서 숫자 작업을 수행하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 00b44d971a487a0bbec27f3fc2d0746cedd6f874
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677919"
---
# <a name="numeric-functions-for-arm-templates"></a>ARM 템플릿에 대 한 숫자 함수

리소스 관리자는 ARM (Azure Resource Manager) 템플릿에서 정수를 사용 하기 위한 다음 함수를 제공 합니다.

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

제공된 두 정수의 합을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
|operand1 |예 |int |더할 첫 번째 숫자입니다. |
|operand2 |예 |int |더할 두 번째 숫자입니다. |

### <a name="return-value"></a>반환 값

매개 변수의 합계를 포함하는 정수입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json)에서는 두 개의 매개 변수를 추가합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

반복 루프의 인덱스를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| loopName | 예 | 문자열 | 반복을 가져오기 위한 루프의 이름입니다. |
| offset |아니요 |int |0부터 시작하는 반복 값에 더할 숫자입니다. |

### <a name="remarks"></a>설명

이 함수는 항상 **copy** 개체에 사용됩니다. **offset** 값을 제공하지 않으면 현재 반복 값이 반환됩니다. 반복 값은 0부터 시작합니다.

**loopName** 속성을 사용하면 copyIndex에서 리소스 반복 또는 속성 반복을 참조하는지 여부를 지정할 수 있습니다. **loopName**에 값을 제공하지 않으면 현재 리소스 종류 반복이 사용됩니다. 속성에서 반복하는 경우 **loopName**의 값을 제공합니다.

Copy 사용에 대 한 자세한 내용은 다음을 참조 하세요.

* [ARM 템플릿의 리소스 반복](copy-resources.md)
* [ARM 템플릿의 속성 반복](copy-properties.md)
* [ARM 템플릿의 변수 반복](copy-variables.md)
* [ARM 템플릿의 출력 반복](copy-outputs.md)

### <a name="example"></a>예제

다음 예제에서는 복사 루프 및 이름에 포함되는 인덱스 값을 보여 줍니다.

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
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
    "outputs": {}
}
```

### <a name="return-value"></a>반환 값

반복의 현재 인덱스를 나타내는 정수입니다.

## <a name="div"></a>div

`div(operand1, operand2)`

제공된 두 정수의 나누기를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |int |나누어지는 수입니다. |
| operand2 |예 |int |나누는 데 사용되는 정수입니다. 0 일 수 없습니다. |

### <a name="return-value"></a>반환 값

나누기를 나타내는 정수입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json)에서는 다른 매개 변수로 매개 변수 하나를 나눕니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>float

`float(arg1)`

값을 부동 소수점 숫자로 변환합니다. 논리 앱과 같은 애플리케이션에 사용자 지정 매개 변수를 전달할 때만 이 함수를 사용합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열 또는 int |부동 소수점 숫자로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

부동 소수점 수입니다.

### <a name="example"></a>예제

다음 예제에서는 float를 사용해서 매개 변수를 논리 앱에 전달하는 방법을 보여 줍니다.

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

## <a name="int"></a>int

`int(valueToConvert)`

지정된 값을 정수로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| valueToConvert |예 |문자열 또는 int |정수로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

변환된 값의 정수입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json)은 사용자가 제공한 매개 변수 값을 정수로 변환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": {
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최대값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최대값을 가져올 컬렉션입니다. |

### <a name="return-value"></a>반환 값

컬렉션의 최대값을 나타내는 정수입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json)에서는 배열 및 정소 목록에 최대값을 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>분

`min (arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최소값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최소값을 가져올 컬렉션입니다. |

### <a name="return-value"></a>반환 값

컬렉션의 최소값을 나타내는 정수입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json)에서는 배열 및 정소 목록에 최소값을 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

제공된 두 정수를 사용하여 나누기한 나머지를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |int |나누어지는 수입니다. |
| operand2 |예 |int |나누는 데 사용 되는 숫자는 0 일 수 없습니다. |

### <a name="return-value"></a>반환 값

나머지를 나타내는 정수입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json)에서는 다른 매개 변수로 매개 변수 하나를 나눈 나머지를 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

제공된 두 정수의 곱하기를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |int |곱할 첫 번째 숫자입니다. |
| operand2 |예 |int |곱할 두 번째 숫자입니다. |

### <a name="return-value"></a>반환 값

곱하기를 나타내는 정수입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json)에서는 다른 매개 변수로 매개 변수 하나를 곱합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

제공된 두 정수의 빼기를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |int |빼는 피감수입니다. |
| operand2 |예 |int |빼는 감수입니다. |

### <a name="return-value"></a>반환 값

빼기를 나타내는 정수입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json)에서는 다른 매개 변수에서 매개 변수를 뺍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| subResult | Int | 4 |

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager 템플릿의 섹션에 대 한 설명은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
* 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](copy-resources.md)를 참조하세요.
