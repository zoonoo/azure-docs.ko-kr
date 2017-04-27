---
title: "Resource Manager 템플릿 함수 | Microsoft Docs"
description: "Azure Resource Manager 템플릿에서 값을 검색하고 문자열과 숫자로 작업하며 배포 정보를 검색하는 데 사용하는 함수를 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 591749e2a91f8dcc080b5fa697c1f9bf953d836f
ms.lasthandoff: 04/15/2017


---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager 템플릿 함수
이 항목에서는 Azure Resource Manager 템플릿에서 사용할 수 있는 모든 함수에 대해 설명합니다.

템플릿 함수 및 해당 매개 변수는 대/소문자를 구분하지 않습니다. 예를 들어 Resource Manager에서 **variables('var1')**와 **VARIABLES('VAR1')**는 동일합니다. 계산될 때 함수는 대/소문자를 명시적으로 수정하지 않는 한(toUpper 또는 toLower 등) 대/소문자를 보존합니다. 특정 리소스 유형에는 함수가 계산되는 방식에 관계없이 대/소문자 요구 사항이 있을 수 있습니다.

## <a name="numeric-functions"></a>숫자 함수
Resource Manager는 정수 작업을 위한 다음 함수를 제공합니다.

* [추가](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [int](#int)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

### <a name="add"></a>추가
`add(operand1, operand2)`

제공된 두 정수의 합을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- | 
|operand1 |예 |Integer |더할 첫 번째 숫자입니다. |
|operand2 |예 |Integer |더할 두 번째 숫자입니다. |

다음 예제에서는 두 개의 매개 변수를 추가합니다.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "First integer to add"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Second integer to add"
    }
  }
},
...
"outputs": {
  "addResult": {
    "type": "int",
    "value": "[add(parameters('first'), parameters('second'))]"
  }
}
```

<a id="copyindex" />

### <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

반복 루프의 인덱스를 반환합니다. 

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| offset |아니요 |Integer |0부터 시작하는 반복 값에 더할 숫자입니다. |

이 함수는 항상 **copy** 개체에 사용됩니다. **offset** 값을 제공하지 않으면 현재 반복 값이 반환됩니다. 반복 값은 0부터 시작합니다. **copyIndex**를 사용하는 방법의 설명은 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

다음 예제에서는 복사 루프 및 이름에 포함되는 인덱스 값을 보여 줍니다. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

<a id="div" />

### <a name="div"></a>div
`div(operand1, operand2)`

제공된 두 정수의 나누기를 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |Integer |나누어지는 수입니다. |
| operand2 |예 |Integer |나누는 데 사용되는 정수입니다. 0일 수 없습니다. |

다음 예제에서는 다른 매개 변수로 매개 변수 하나를 나눕니다.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer being divided"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer used to divide"
    }
  }
},
...
"outputs": {
  "divResult": {
    "type": "int",
    "value": "[div(parameters('first'), parameters('second'))]"
  }
}
```

<a id="int" />

### <a name="int"></a>int
`int(valueToConvert)`

지정된 값을 정수로 변환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| valueToConvert |예 |문자열 또는 정수 |정수로 변환할 값입니다. |

다음 예제는 사용자가 제공한 매개 변수 값을 정수로 변환합니다.

```json
"parameters": {
    "appId": { "type": "string" }
},
"variables": { 
    "intValue": "[int(parameters('appId'))]"
}
```

<a id="mod" />

### <a name="mod"></a>mod
`mod(operand1, operand2)`

제공된 두 정수를 사용하여 나누기한 나머지를 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |Integer |나누어지는 수입니다. |
| operand2 |예 |Integer |나누는 데 사용되는 정수로, 0일 수 없습니다. |

다음 예제에서는 다른 매개 변수로 매개 변수 하나를 나눈 나머지를 반환합니다.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer being divided"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer used to divide"
    }
  }
},
...
"outputs": {
  "modResult": {
    "type": "int",
    "value": "[mod(parameters('first'), parameters('second'))]"
  }
}
```

<a id="mul" />

### <a name="mul"></a>mul
`mul(operand1, operand2)`

제공된 두 정수의 곱하기를 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |Integer |곱할 첫 번째 숫자입니다. |
| operand2 |예 |Integer |곱할 두 번째 숫자입니다. |

다음 예제에서는 다른 매개 변수로 매개 변수 하나를 곱합니다.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "First integer to multiply"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Second integer to multiply"
    }
  }
},
...
"outputs": {
  "mulResult": {
    "type": "int",
    "value": "[mul(parameters('first'), parameters('second'))]"
  }
}
```

<a id="sub" />

### <a name="sub"></a>sub
`sub(operand1, operand2)`

제공된 두 정수의 빼기를 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |Integer |빼는 피감수입니다. |
| operand2 |예 |Integer |빼는 감수입니다. |

다음 예제에서는 다른 매개 변수에서 매개 변수 하나를 뺍니다.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer subtracted from"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer to subtract"
    }
  }
},
...
"outputs": {
  "subResult": {
    "type": "int",
    "value": "[sub(parameters('first'), parameters('second'))]"
  }
}
```

## <a name="string-functions"></a>문자열 함수
Resource Manager는 문자열 작업을 위한 다음 함수를 제공합니다.

* [base64](#base64)
* [concat](#concat)
* [length](#lengthstring)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skipstring)
* [분할](#split)
* [string](#string)
* [substring](#substring)
* [take](#takestring)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)

<a id="base64" />

### <a name="base64"></a>base64
`base64 (inputString)`

입력 문자열의 base64 표현을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| inputString |예 |문자열 |base64 표현으로 반환할 값입니다. |

다음 예에서는 base64 함수를 사용하는 방법을 보여 줍니다.

```json
"variables": {
  "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
  "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

<a id="concat" />

### <a name="concat---string"></a>concat - 문자열
`concat (string1, string2, string3, ...)`

여러 문자열 값을 결합하고 연결된 문자열을 반환합니다. 

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| string1 |예 |문자열 |연결할 첫 번째 값입니다. |
| 추가 문자열 |아니요 |String |연결할 추가 값(순서대로)입니다. |

이 함수는 인수를 개수에 관계없이 사용할 수 있으며 매개 변수에 대한 문자열이나 배열 중 하나를 사용할 수 있습니다. 배열 연결의 예제는 [concat - 배열](#concatarray)을 참조하세요.

다음 예제에서는 여러 문자열 값을 결합하여 연결된 문자열을 반환하는 방법을 보여 줍니다.

```json
"outputs": {
    "siteUri": {
      "type": "string",
      "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

<a id="lengthstring" />

### <a name="length---string"></a>length - 문자열
`length(string)`

문자열의 문자 수를 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| string |예 |문자열 |문자 수를 구하기 위해 사용할 값입니다. |

배열을 사용하여 length를 사용하는 예제는 [length - 배열](#length)을 참조하세요.

다음 예제는 문자열의 문자 수를 반환합니다. 

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "nameLength": "[length(parameters('appName'))]"
}
```

<a id="padleft" />

### <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

지정된 총 길이에 도달할 때까지 왼쪽에 문자를 추가하여 오른쪽 맞추어진 문자열을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| valueToPad |예 |문자열 또는 정수 |오른쪽으로 맞출 값입니다. |
| totalLength |예 |Integer |반환된 문자열에서 문자의 총수입니다. |
| paddingCharacter |아니요 |단일 문자 |총 길이에 도달할 때까지 왼쪽 여백에 사용되는 문자입니다. 기본값은 공백입니다. |

다음 예제는 문자열이 10자에 도달할 때까지 0 문자를 추가하여 사용자가 제공한 매개 변수 값을 채우는 방법을 보여줍니다. 원래 매개 변수 값이 10자 보다 긴 경우 문자가 더 추가되지 않습니다.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
}
```

<a id="replace" />

### <a name="replace"></a>replace
`replace(originalString, oldCharacter, newCharacter)`

새 문자열을 다른 문자로 대체한 지정된 문자열에서 한 문자의 인스턴스로 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| originalString |예 |String |다른 문자로 대체하는 한 문자의 모든 인스턴스가 있는 값입니다. |
| oldCharacter |예 |String |원래 문자열에서 제거할 문자입니다. |
| newCharacter |예 |String |제거된 문자 대신 추가하는 문자입니다. |

다음 예제는 사용자가 제공한 문자열에서 모든 대시를 제거하는 방법을 보여줍니다.

```json
"parameters": {
    "identifier": { "type": "string" }
},
"variables": { 
    "newidentifier": "[replace(parameters('identifier'),'-','')]"
}
```

<a id="skipstring" />

### <a name="skip---string"></a>skip - 문자열
`skip(originalValue, numberToSkip)`

문자열에 지정된 수 후 모든 문자로 구성된 문자열을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| originalValue |예 |문자열 |건너뛰는 데 사용할 문자열입니다. |
| numberToSkip |예 |Integer |건너뛸 문자 수입니다. 이 값이 0 이하이면 문자열에 있는 문자가 반환됩니다. 문자열의 길이보다 크면 빈 문자열이 반환됩니다. |

배열을 사용하여 skip을 사용하는 예제는 [skip - 배열](#skip)을 참조하세요.

다음 예제에서는 문자열에서 지정된 수의 문자를 건너뜁니다.

```json
"parameters": {
  "first": {
    "type": "string",
    "metadata": {
      "description": "Value to use for skipping"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of characters to skip"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "string",
    "value": "[skip(parameters('first'),parameters('second'))]"
  }
}
```

<a id="split" />

### <a name="split"></a>분할
`split(inputString, delimiterString)`

`split(inputString, delimiterArray)`

지정된 구분 기호로 구분되는 입력 문자열의 부분 문자열을 포함하는 문자열의 배열을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| inputString |예 |문자열 |분할할 문자열입니다. |
| 구분 기호 |예 |문자열 또는 문자열 배열 |문자열 분할에 사용할 구분 기호입니다. |

다음 예제에서는 쉼표를 사용하여 입력 문자열을 분할합니다.

```json
"parameters": {
    "inputString": { "type": "string" }
},
"variables": { 
    "stringPieces": "[split(parameters('inputString'), ',')]"
}
```

다음 예제에서는 쉼표 또는 세미콜론으로 입력 문자열을 분할합니다.

```json
"variables": {
  "stringToSplit": "test1,test2;test3",
  "delimiters": [ ",", ";" ]
},
"resources": [ ],
"outputs": {
  "exampleOutput": {
    "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
    "type": "array"
  }
}
```

<a id="string" />

### <a name="string"></a>string
`string(valueToConvert)`

지정된 값을 문자열로 변환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| valueToConvert |예 | 모두 |문자열로 변환할 값입니다. 개체 및 배열을 비롯하여 모든 값 형식을 변환할 수 있습니다. |

다음 예제는 사용자가 제공한 매개 변수 값을 문자열로 변환합니다.

```json
"parameters": {
  "jsonObject": {
    "type": "object",
    "defaultValue": {
      "valueA": 10,
      "valueB": "Example Text"
    }
  },
  "jsonArray": {
    "type": "array",
    "defaultValue": [ "a", "b", "c" ]
  },
  "jsonInt": {
    "type": "int",
    "defaultValue": 5
  }
},
"variables": { 
  "objectString": "[string(parameters('jsonObject'))]",
  "arrayString": "[string(parameters('jsonArray'))]",
  "intString": "[string(parameters('jsonInt'))]"
}
```

<a id="substring" />

### <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

지정된 문자 위치에서 시작하고 지정한 개수의 문자를 포함하는 부분 문자열을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| stringToParse |예 |String |부분 문자열을 추출할 원래 문자열입니다. |
| startIndex |아니요 |Integer |부분 문자열의 0부터 시작하는 문자 위치입니다. |
| length |아니요 |Integer |부분 문자열에 대한 문자 수입니다. 문자열 내 위치를 참조해야 합니다. |

다음 예제에서는 매개 변수에서 처음 세 문자를 추출합니다.

```json
"parameters": {
    "inputString": { "type": "string" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 3)]"
}
```

다음 예제는 "인덱스 및 길이 매개 변수는 문자열 내 위치를 참조해야 합니다. 인덱스 매개 변수: '0', 길이 매개 변수: '11', 문자열 매개 변수의 길이: '10'." 오류와 함께 실패합니다.

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

<a id="takestring" />

### <a name="take---string"></a>take - 문자열
`take(originalValue, numberToTake)`

문자열의 시작부터 지정된 수의 문자로 문자열을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| originalValue |예 |문자열 |문자를 가져올 값입니다. |
| numberToTake |예 |Integer |가져올 문자 수입니다. 이 값이 0 이하이면 빈 문자열이 반환됩니다. 지정된 문자열의 길이보다 크면 문자열의 모든 문자가 반환됩니다. |

배열을 사용하여 take를 사용하는 예제는 [take - 배열](#take)을 참조하세요.

다음 예제에서는 문자열에서 지정된 수의 문자를 가져옵니다.

```json
"parameters": {
  "first": {
    "type": "string",
    "metadata": {
      "description": "Value to use for taking"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of characters to take"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "string",
    "value": "[take(parameters('first'), parameters('second'))]"
  }
}
```

<a id="tolower" />

### <a name="tolower"></a>toLower
`toLower(stringToChange)`

지정된 문자열을 소문자로 변환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| stringToChange |예 |문자열 |소문자로 변환할 값입니다. |

다음 예제는 사용자가 제공한 매개 변수 값을 소문자로 변환합니다.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "lowerCaseAppName": "[toLower(parameters('appName'))]"
}
```

<a id="toupper" />

### <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

지정된 문자열을 대문자로 변환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| stringToChange |예 |문자열 |대문자로 변환할 값입니다. |

다음 예제는 사용자가 제공한 매개 변수 값을 대문자로 변환합니다.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "upperCaseAppName": "[toUpper(parameters('appName'))]"
}
```

<a id="trim" />

### <a name="trim"></a>trim
`trim (stringToTrim)`

지정된 문자열에서 모든 선행 및 후행 공백 문자를 제거합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| stringToTrim |예 |String |자를 값입니다. |

다음은 사용자가 입력한 매개 변수 값에서 공백 문자를 자르는 예입니다.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "trimAppName": "[trim(parameters('appName'))]"
}
```

<a id="uniquestring" />

### <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

매개 변수로 제공된 값을 기반으로 결정 해시 문자열을 만듭니다. 

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| baseString |예 |String |고유한 문자열을 만들기 위해 해시 함수에서 사용되는 값입니다. |
| 필요에 따라 추가하는 매개 변수 |아니요 |String |고유성 수준을 지정하는 값을 만들기 위해 필요한 만큼 문자열을 추가할 수 있습니다. |

이 함수는 리소스의 고유한 이름을 만들어야 할 때 유용합니다. 결과의 고유성 범위를 제한하는 매개 변수 값을 제공합니다. 구독, 리소스 그룹 또는 배포까지 해당 이름이 고유한지 여부를 지정할 수 있습니다. 

반환된 값은 임의 문자열이 아닌 해시 함수의 결과입니다. 반환된 값은 13자입니다. 전역적으로 고유하지 않습니다. 의미있는 이름을 만들기 위해 해당 값과 명명 규칙의 접두사를 결합할 수도 있습니다. 다음 예제에서는 반환된 값의 형식을 보여 줍니다. 실제 값은 제공된 매개 변수에 따라 달라집니다.

    tcvhiyu5h2o5o

다음 예제에서는 uniqueString를 사용하여 일반적으로 사용하는 수준에 대해 고유한 값을 만드는 방법을 보여 줍니다.

구독에 범위가 지정된 고유함

```json
"[uniqueString(subscription().subscriptionId)]"
```

리소스 그룹에 범위가 지정된 고유함

```json
"[uniqueString(resourceGroup().id)]"
```

리소스 그룹의 배포에 범위가 지정된 고유함

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

다음 예제에서는 리소스 그룹에 따라 저장소 계정에 고유한 이름을 만드는 방법을 보여 줍니다. 리소스 그룹의 내부에서 같은 방식으로 생성된 경우 이름은 고유하지 않습니다.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```


<a id="uri" />

### <a name="uri"></a>uri
`uri (baseUri, relativeUri)`

baseUri와 relativeUri 문자열을 결합하여 절대 URI를 만듭니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| baseUri |예 |String |기본 uri 문자열입니다. |
| relativeUri |예 |문자열 |기본 uri 문자열에 추가할 상대 uri 문자열입니다. |

**baseUri** 매개 변수에 대한 값은 특정 파일을 포함할 수 있지만 URI를 생성하는 경우 기본 경로만 사용됩니다. 예를 들어 `http://contoso.com/resources/azuredeploy.json`을 baseUri 매개 변수로 전달하면 기본 URI는 `http://contoso.com/resources/`가 됩니다.

다음 예제에서는 부모 템플릿의 값을 기반으로 중첩된 템플릿에 대한 링크를 생성하는 방법을 보여 줍니다.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

## <a name="array-functions"></a>배열 함수
Resource Manager는 배열 값 작업을 위한 여러 기능을 제공합니다.

* [concat](#concatarray)
* [length](#length)
* [skip](#skip)
* [take](#take)

값으로 구분되는 문자열 값의 배열을 가져오려면 [split](#split)을 참조하세요.

<a id="concatarray" />

### <a name="concat---array"></a>concat - 배열
`concat (array1, array2, array3, ...)`

여러 배열을 결합하고 연결된 배열을 반환합니다. 

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| array1 |예 |배열 |연결할 첫 번째 배열입니다. |
| 추가 배열 |아니요 |배열 |연결할 추가 배열(순서대로)입니다. |

이 함수는 인수를 개수에 관계없이 사용할 수 있으며 매개 변수에 대한 문자열이나 배열 중 하나를 사용할 수 있습니다. 문자열 값 연결의 예제는 [concat - 문자열](#concat)을 참조하세요.

다음 예제에서는 두 개의 배열을 결합하는 방법을 보여 줍니다.

```json
"parameters": {
    "firstarray": {
      "type": "array"
    }
    "secondarray": {
      "type": "array"
    }
},
"variables": {
    "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]"
}
```

<a id="length" />

### <a name="length---array"></a>length - 배열
`length(array)`

배열 내의 요소 수를 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| array |예 |배열 |요소의 수를 가져오는 데 사용할 배열입니다. |

배열과 함께 이 함수를 사용하면 리소스를 만들 때 반복 횟수를 지정할 수 있습니다. 다음 예제에서 매개 변수 **siteNames** 는 웹 사이트를 만들 때 사용할 이름 배열을 나타냅니다.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

배열과 함께 이 함수를 사용하는 방법의 예제는 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요. 

문자열 값을 사용하여 length를 사용하는 예제는 [length - 문자열](#lengthstring)을 참조하세요.

<a id="skip" />

### <a name="skip---array"></a>skip - 배열
`skip(originalValue, numberToSkip)`

배열에서 지정된 수 후 모든 요소와 함께 배열을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| originalValue |예 |배열 |건너뛰는 데 사용할 배열입니다. |
| numberToSkip |예 |Integer |건너뛸 요소 수입니다. 이 값이 0 이하이면 배열에 있는 모든 요소가 반환됩니다. 배열의 길이보다 크면 빈 배열이 반환됩니다. |

문자열을 사용하여 skip을 사용하는 예제는 [skip - 문자열](#skipstring)을 참조하세요.

다음 예제에서는 배열에서 지정된 수의 요소를 건너뜁니다.

```json
"parameters": {
  "first": {
    "type": "array",
    "metadata": {
      "description": "Values to use for skipping"
    },
    "defaultValue": [ "one", "two", "three" ]
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of elements to skip"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "array",
    "value": "[skip(parameters('first'), parameters('second'))]"
  }
}
```

<a id="take" />

### <a name="take---array"></a>take - 배열
`take(originalValue, numberToTake)`

배열의 시작 부분부터 지정된 수의 요소와 함께 배열을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| originalValue |예 |배열 |요소를 가져올 배열입니다. |
| numberToTake |예 |Integer |가져올 요소 수입니다. 이 값이 0 이하이면 빈 배열이 반환됩니다. 지정된 배열의 길이보다 크면 배열의 모든 요소가 반환됩니다. |

문자열을 사용하여 take를 사용하는 예제는 [take - 문자열](#takestring)을 참조하세요.

다음 예제에서는 배열에서 지정된 수의 요소를 가져옵니다.

```json
"parameters": {
  "first": {
    "type": "array",
    "metadata": {
      "description": "Values to use for taking"
    },
    "defaultValue": [ "one", "two", "three" ]
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of elements to take"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "array",
    "value": "[take(parameters('first'),parameters('second'))]"
  }
}
```

## <a name="deployment-value-functions"></a>배포 값 함수
Resource Manager는 템플릿의 섹션에서 값을 가져오고 배포와 관련된 값을 가져오기 위한 다음 함수를 제공합니다.

* [deployment](#deployment)
* [매개 변수](#parameters)
* [variables](#variables)

리소스, 리소스 그룹 또는 구독에서 값을 가져오려면 [리소스 함수](#resource-functions)를 참조하세요.

<a id="deployment" />

### <a name="deployment"></a>배포
`deployment()`

현재 배포 작업에 대한 정보를 반환합니다.

이 함수는 배포하는 동안 전달되는 개체를 반환합니다. 반환된 개체의 속성은 배포 개체가 링크로 전달되는지 아니면 인라인 개체로 전달되는지에 따라 다릅니다. 

배포 개체가 로컬 파일을 가리키기 위해 Azure PowerShell의 **-TemplateFile** 매개 변수를 사용할 때와 같이 인라인으로 전달되는 경우 개체는 다음 형식으로 반환됩니다.

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

개체가 원격 개체를 가리키기 위해 **-TemplateUri** 매개 변수를 사용할 때와 같이 링크로 전달되는 경우 개체는 다음 형식으로 반환됩니다. 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

다음 예제는 deployment()를 사용하여 부모 템플릿의 URI를 기반으로 하는 다른 템플릿에 연결하는 방법을 보여 줍니다.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

<a id="parameters" />

### <a name="parameters"></a>매개 변수
`parameters (parameterName)`

매개 변수 값을 반환합니다. 템플릿의 매개 변수 섹션에서 지정된 매개 변수 이름을 정의해야 합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| parameterName |예 |String |반환할 매개 변수의 이름입니다. |

다음 예에서는 매개 변수 함수의 간소화된 사용을 보여 줍니다.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

<a id="variables" />

### <a name="variables"></a>variables
`variables (variableName)`

변수의 값을 반환합니다. 템플릿의 변수 섹션에서 지정된 변수 이름을 정의해야 합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| variableName |예 |String |반환할 변수의 이름입니다. |

다음 예제는 변수 값을 사용합니다.

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
],
```

## <a name="resource-functions"></a>리소스 함수
Resource Manager는 리소스 값을 가져오기 위한 다음 함수를 제공합니다.

* [listKeys 및 list{Value}](#listkeys)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)

매개 변수, 변수 또는 현재 배포에서 값을 가져오려면 [배포 값 함수](#deployment-value-functions)를 참조하세요.

<a id="listkeys" />
<a id="list" />

### <a name="listkeys-and-listvalue"></a>listKeys 및 list{Value}
`listKeys (resourceName or resourceIdentifier, apiVersion)`

`list{Value} (resourceName or resourceIdentifier, apiVersion)`

list 작업을 지원하는 모든 리소스 형식에 대한 값을 반환합니다. 가장 일반적인 사용법은 **listKeys**입니다. 

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| resourceName 또는 resourceIdentifier |예 |String |리소스에 대한 고유 식별자. |
| apiVersion |예 |문자열 |리소스 런타임 상태의 API 버전입니다. 일반적으로 **yyyy-mm-dd** 형식입니다. |

**list**로 시작하는 작업은 템플릿에서 함수로 사용됩니다. 사용 가능한 작업은 **listKeys**뿐만 아니라 **list**, **listAdminKeys** 및 **listStatus**와 같은 작업도 포함합니다. list 작업이 있는 리소스 종류를 확인하려면 리소스 공급자에 대한 [REST API 작업](/rest/api/)을 참조하세요.

리소스 공급자에 대한 list 작업을 찾으려면 다음 PowerShell cmdlet을 사용합니다.

```powershell
Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
```

리소스 공급자에 대한 list 작업을 찾으려면 다음 Azure CLI 명령 및 JSON 유틸리티 [jq](http://stedolan.github.io/jq/download/)를 사용하여 list 작업만 필터링합니다.

```azurecli
azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
```

[resourceId 함수](#resourceid) 또는 **{providerNamespace}/{resourceType}/{resourceName}** 형식을 사용하여 resourceId를 지정할 수 있습니다.

다음 예에서는 출력 섹션의 저장소 계정에서 기본 및 보조 키를 반환하는 방법을 보여 줍니다.

```json
"outputs": { 
  "listKeysOutput": { 
    "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
    "type" : "object" 
  } 
}
``` 

listKeys에서 반환된 개체는 다음 형식을 가집니다.

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

<a id="providers" />

### <a name="providers"></a>providers
`providers (providerNamespace, [resourceType])`

리소스 공급자와 지원되는 리소스 유형에 대한 정보를 반환합니다. 리소스 유형을 제공하지 않는 경우 함수는 리소스 공급자에 대한 모든 지원되는 유형을 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| providerNamespace |예 |String |공급자의 네임스페이스입니다. |
| resourceType |아니요 |String |지정된 네임스페이스 내의 리소스 유형입니다. |

각 지원되는 유형이 다음 형식으로 반환됩니다. 배열 순서는 보장되지 않습니다.

```json
{
    "resourceType": "",
    "locations": [ ],
    "apiVersions": [ ]
}
```

다음 예에서는 공급자 함수를 사용하는 방법을 보여 줍니다.

```json
"outputs": {
    "exampleOutput": {
        "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
        "type" : "object"
    }
}
```

<a id="reference" />

### <a name="reference"></a>reference
`reference (resourceName or resourceIdentifier, [apiVersion])`

다른 리소스의 런타임 상태를 나타내는 개체를 반환합니다.

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| resourceName 또는 resourceIdentifier |예 |문자열 |리소스의 이름 또는 고유 식별자입니다. |
| apiVersion |아니요 |문자열 |지정된 리소스의 API 버전입니다. 리소스가 동일한 템플릿 내에서 프로비전되지 않은 경우 이 매개 변수를 포함합니다. 일반적으로 **yyyy-mm-dd** 형식입니다. |

**reference** 함수는 런타임 상태에서 값을 파생하므로 변수 섹션에서 사용할 수 없습니다. 템플릿의 출력 섹션에서 사용할 수 있습니다.

참조 함수를 사용하여 참조되는 리소스가 동일한 템플릿 내에서 프로비전되는 경우 한 리소스가 다른 리소스에 종속되도록 암시적으로 선언합니다. 또한 **dependsOn** 속성도 사용할 필요가 없습니다. 참조 리소스가 배포를 완료할 때까지 함수는 평가되지 않습니다.

다음 예제는 같은 템플릿에 배포되는 저장소 계정을 참조합니다.

```json
"outputs": {
    "NewStorage": {
        "value": "[reference(parameters('storageAccountName'))]",
        "type" : "object"
    }
}
```

다음 예제는 이 템플릿에 배포되지 않지만 동일한 리소스 그룹 내에 배포되는 리소스로 존재하는 저장소 계정을 참조합니다.

```json
"outputs": {
    "ExistingStorage": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
        "type" : "object"
    }
}
```

다음 예제와 같이 반환된 개체에서 특정 값을 검색할 수 있습니다(예: Blob 끝점 URI).

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    }
}
```

다음 예제에서는 다른 리소스 그룹에서 저장소 계정을 참조합니다.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    }
}
```

**reference** 함수에서 반환된 개체의 속성은 리소스 유형에 따라 다릅니다. 리소스 유형에 대한 속성 이름 및 값을 보려면 **outputs** 섹션에서 개체를 반환하는 간단한 템플릿을 만듭니다. 해당 유형의 기존 리소스가 있는 경우 템플릿은 새로운 리소스를 배포하지 않고 개체를 반환합니다. 해당 유형의 기존 리소스가 없는 경우 템플릿은 이 유형만 배포하고 개체를 반환합니다. 그런 다음, 배포 중에 값을 동적으로 검색하는 데 필요한 다른 템플릿에 해당 속성을 추가합니다. 

<a id="resourcegroup" />

### <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

현재 리소스 그룹을 나타내는 개체를 반환합니다. 

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

다음 예에서는 리소스 그룹 위치를 사용하여 웹 사이트에 대한 위치를 할당합니다.

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

<a id="resourceid" />

### <a name="resourceid"></a>resourceId
`resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

리소스의 고유 식별자를 반환합니다. 

| 매개 변수 | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| subscriptionId |아니요 |문자열(GUID 형식) |기본값은 현재 구독입니다. 다른 구독에서 리소스를 검색해야 하는 경우 이 값을 지정합니다. |
| resourceGroupName |아니요 |String |기본값은 현재 리소스 그룹입니다. 다른 리소스 그룹에서 리소스를 검색해야 하는 경우 이 값을 지정합니다. |
| resourceType |예 |String |리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다. |
| resourceName1 |예 |String |리소스의 이름입니다. |
| resourceName2 |아니요 |문자열 |리소스가 중첩된 경우 다음 리소스 이름 세그먼트입니다. |

리소스 이름이 모호하거나 동일한 템플릿 내에서 프로비전되지 않은 경우 이 함수를 사용합니다. 식별자는 다음 형식으로 반환됩니다.

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

다음 예에서는 웹 사이트 및 데이터베이스에 대한 리소스 ID를 검색하는 방법을 보여 줍니다. 웹 사이트는 **myWebsitesGroup** 이라는 리소스 그룹에 있고 데이터베이스는 이 템플릿에 대한 현재 리소스 그룹에 있습니다.

```json
[resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

대체 리소스 그룹의 저장소 계정 또는 가상 네트워크를 사용할 경우 이 함수를 사용해야 합니다. 저장소 계정 또는 가상 네트워크를 여러 리소스 그룹에서 사용할 수 있으므로, 단일 리소스 그룹을 삭제할 때 해당 저장소 계정 또는 가상 네트워크를 삭제하지 않습니다. 다음 예에서는 외부 리소스 그룹의 리소스를 쉽게 사용할 수 있는 방법을 보여 줍니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

<a id="subscription" />

### <a name="subscription"></a>subscription
`subscription()`

구독에 대한 세부 정보를 다음 형식으로 반환합니다.

```json
{
    "id": "/subscriptions/#####",
    "subscriptionId": "#####",
    "tenantId": "#####"
}
```

다음 예에서는 출력 섹션에서 호출되는 구독 함수를 보여 줍니다. 

```json
"outputs": { 
  "exampleOutput": { 
      "value": "[subscription()]", 
      "type" : "object" 
  } 
} 
```

## <a name="next-steps"></a>다음 단계
* Azure Resource Manager 템플릿의 섹션에 대한 설명은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)
* 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)
* 만든 템플릿을 배포하는 방법을 보려면 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)


