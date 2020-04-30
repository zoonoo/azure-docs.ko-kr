---
title: 템플릿 함수-배열
description: 배열 작업을 위해 Azure Resource Manager 템플릿에서 사용할 함수에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f34ba74847ac394e37e6ef33f859304128daacde
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203814"
---
# <a name="array-functions-for-arm-templates"></a>ARM 템플릿에 대 한 배열 함수

리소스 관리자는 ARM (Azure Resource Manager) 템플릿에서 배열을 사용 하기 위한 여러 함수를 제공 합니다.

* [배열과](#array)
* [사용한](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [비우려면](#empty)
* [기본](#first)
* [교집합](#intersection)
* [최신](#last)
* [length](#length)
* [max](#max)
* [일별](#min)
* [벗어납니다](#range)
* [킵](#skip)
* [노트](#take)
* [부분](#union)

값으로 구분되는 문자열 값의 배열을 가져오려면 [split](template-functions-string.md#split)을 참조하세요.

## <a name="array"></a>array

`array(convertToArray)`

값을 배열로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| convertToArray |예 |int, 문자열, 배열 또는 개체 |배열로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

배열입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json)에서는 여러 다른 형식의 배열 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "efgh"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| intOutput | 배열 | [1] |
| stringOutput | 배열 | ["efgh"] |
| objectOutput | 배열 | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

여러 배열을 결합하고 연결된 배열을 반환하거나 여러 문자열 값을 결합하고 연결된 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 문자열 |연결을 위한 첫 번째 배열 또는 문자열입니다. |
| 추가 인수 |아니요 |배열 또는 문자열 |연결 순서로 나타낸 추가 배열 또는 문자열입니다. |

이 함수는 인수를 개수에 관계없이 사용할 수 있으며 매개 변수에 대한 문자열이나 배열 중 하나를 사용할 수 있습니다. 그러나 매개 변수에 대 한 배열과 문자열을 둘 다 제공할 수는 없습니다. 배열은 다른 배열과만 연결 됩니다.

### <a name="return-value"></a>반환 값

연결된 값의 문자열 또는 배열입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json)에서는 두 개의 배열을 결합하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstArray": {
            "type": "array",
            "defaultValue": [
                "1-1",
                "1-2",
                "1-3"
            ]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": [
                "2-1",
                "2-2",
                "2-3"
            ]
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| return | 배열 | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json)에서는 2개의 문자열 값을 결합하고 연결된 문자열을 반환하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| concatOutput | 문자열 | prefix-5yj4yjf5mbg72 |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

배열에 값이 포함되는지, 개체에 키가 포함되는지 또는 문자열에 하위 문자열이 포함되는지를 확인합니다. 문자열 비교에서는 대/소문자를 구분합니다. 그러나 개체에 키가 포함되어 있는지를 테스트할 때는 비교에서 대/소문자를 구분하지 않습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| container |예 |배열, 개체 또는 문자열 |찾을 값을 포함하는 값입니다. |
| itemToFind |예 |문자열 또는 int |찾을 값입니다. |

### <a name="return-value"></a>반환 값

항목이 있으면 **True**이고, 항목이 없으면 **False**입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json)에서는 여러 다른 형식의 contains를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createarray"></a>createarray

`createArray (arg1, arg2, arg3, ...)`

매개 변수에서 배열을 만듭니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열, 정수, 배열 또는 개체 |배열의 첫 번째 값입니다. |
| 추가 인수 |아니요 |문자열, 정수, 배열 또는 개체 |배열의 추가 값입니다. |

### <a name="return-value"></a>반환 값

배열입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json)에서는 여러 다른 형식의 createArray를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| stringArray | 배열 | ["a", "b", "c"] |
| intArray | 배열 | [1, 2, 3] |
| objectArray | 배열 | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | 배열 | [["one", "two", "three"]] |

## <a name="empty"></a>비어 있음

`empty(itemToTest)`

배열, 개체 또는 문자열이 비어 있는지를 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |예 |배열, 개체 또는 문자열 |비어 있는지 확인할 값입니다. |

### <a name="return-value"></a>반환 값

값이 비어 있으면 **True**를 반환하고 비어 있지 않으면 **False**를 반환합니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json)에서는 배열, 개체 및 문자열이 비어 있는지 여부를 확인합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

배열의 첫 번째 요소 또는 문자열의 첫 번째 문자를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 문자열 |첫 번째 요소 또는 문자를 검색할 값입니다. |

### <a name="return-value"></a>반환 값

배열의 첫 번째 요소 또는 문자열의 첫 번째 문자에 대한 형식(문자열, int, 배열 또는 개체)입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json)에서는 배열 및 문자열에 첫 번째 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayOutput | 문자열 | one |
| stringOutput | 문자열 | O |

## <a name="intersection"></a>교집합

`intersection(arg1, arg2, arg3, ...)`

매개 변수에서 공통 요소를 갖는 단일 배열 또는 개체를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 개체 |공통 요소를 찾는 데 사용할 첫 번째 값입니다. |
| arg2 |예 |배열 또는 개체 |공통 요소를 찾는 데 사용할 두 번째 값입니다. |
| 추가 인수 |아니요 |배열 또는 개체 |공통 요소를 찾는 데 사용할 추가 값입니다. |

### <a name="return-value"></a>반환 값

공통 요소가 있는 배열 또는 개체입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json)에서는 배열 및 개체에 교집합을 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | 배열 | ["two", "three"] |

## <a name="last"></a>last

`last (arg1)`

배열의 마지막 요소 또는 문자열의 마지막 문자를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 문자열 |마지막 요소 또는 문자를 검색할 값입니다. |

### <a name="return-value"></a>반환 값

배열의 마지막 요소 또는 문자열의 마지막 문자에 대한 형식(문자열, int, 배열 또는 개체)입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json)에서는 배열 및 문자열에 최근 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayOutput | 문자열 | three |
| stringOutput | 문자열 | e |

## <a name="length"></a>length

`length(arg1)`

배열의 요소 수, 문자열의 문자 또는 개체의 루트 수준 속성을 반환 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |array, string 또는 object |요소 수를 가져오는 데 사용할 배열, 문자 수를 가져오는 데 사용할 문자열 또는 루트 수준 속성의 수를 가져오는 데 사용할 개체입니다. |

### <a name="return-value"></a>반환 값

int입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json)에서는 배열 및 문자열에 length를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

배열과 함께 이 함수를 사용하면 리소스를 만들 때 반복 횟수를 지정할 수 있습니다. 다음 예제에서 매개 변수 **siteNames** 는 웹 사이트를 만들 때 사용할 이름 배열을 나타냅니다.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

배열과 함께 이 함수를 사용하는 방법의 예제는 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](copy-resources.md)를 참조하세요.

## <a name="max"></a>max

`max(arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최대값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최대값을 가져올 컬렉션입니다. |

### <a name="return-value"></a>반환 값

최대값을 나타내는 int입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json)에서는 배열 및 정소 목록에 최대값을 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="min"></a>min

`min(arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최소값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최소값을 가져올 컬렉션입니다. |

### <a name="return-value"></a>반환 값

최소값을 나타내는 int입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json)에서는 배열 및 정소 목록에 최소값을 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="range"></a>range

`range(startIndex, count)`

시작 정수 및 항목의 수를 포함하는 정수 배열을 만듭니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| startIndex |예 |int |배열에서 첫 번째 정수입니다. StartIndex와 count의 합계는 2147483647 보다 크지 않아야 합니다. |
| count |예 |int |배열에 있는 정수의 수입니다. 1만 까지의 음수가 아닌 정수 여야 합니다. |

### <a name="return-value"></a>반환 값

정수 배열입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json)에서는 범위 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| rangeOutput | 배열 | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

배열에서 지정된 숫자 이후의 모든 요소를 포함하는 배열을 반환하거나 문자열에서 지정된 숫자 이후의 모든 숫자를 포함하는 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |예 |배열 또는 문자열 |건너뛰는 데 사용할 배열 또는 문자열입니다. |
| numberToSkip |예 |int |건너뛸 요소 또는 문자 수입니다. 이 값이 0 이하이면 값의 모든 요소 또는 문자가 반환됩니다. 이 값이 배열 또는 문자열의 길이보다 크면 빈 배열 또는 문자열이 반환됩니다. |

### <a name="return-value"></a>반환 값

배열 또는 문자열입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json)에서는 배열에서 지정된 요소 수 및 문자열에서 지정된 수의 문자를 건너뜁니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayOutput | 배열 | ["three"] |
| stringOutput | 문자열 | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

배열 시작부터 지정된 수의 요소를 포함하는 배열 또는 문자열 시작부터 지정된 수의 문자를 포함하는 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |예 |배열 또는 문자열 |요소를 가져올 배열 또는 문자열입니다. |
| numberToTake |예 |int |수락할 요소 또는 문자의 수입니다. 이 값이 0 이하이면 빈 배열 또는 문자열이 반환됩니다. 지정된 배열 또는 문자열의 길이보다 크면 배열 또는 문자열의 모든 요소가 반환됩니다. |

### <a name="return-value"></a>반환 값

배열 또는 문자열입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json)에서는 배열에서 지정된 수의 요소 및 문자열의 문자를 가져옵니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| arrayOutput | 배열 | ["one", "two"] |
| stringOutput | 문자열 | On |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

매개 변수의 모든 요소를 포함하는 단일 배열 또는 개체를 반환합니다. 중복된 값 또는 키는 한 번만 포함됩니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | 유형 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 개체 |요소를 조인하는 데 사용할 첫 번째 값입니다. |
| arg2 |예 |배열 또는 개체 |요소를 조인하는 데 사용할 두 번째 값입니다. |
| 추가 인수 |아니요 |배열 또는 개체 |요소를 조인하는 데 사용할 추가 값입니다. |

### <a name="return-value"></a>반환 값

배열 또는 개체입니다.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json)에서는 배열 및 개체에 union을 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | 배열 | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager 템플릿의 섹션에 대 한 설명은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
