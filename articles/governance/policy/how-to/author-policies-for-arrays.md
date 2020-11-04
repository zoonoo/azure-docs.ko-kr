---
title: 리소스의 배열 속성에 대한 작성자 정책
description: 배열 매개 변수 및 배열 언어 식을 사용하고, [*] 별칭을 평가하고, Azure Policy 정의 규칙을 사용하여 요소를 추가하는 방법을 알아봅니다.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 92339a6da4fd2061d66935cc8d04428c69822862
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323226"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure 리소스의 배열 속성에 대한 작성자 정책

Azure Resource Manager 속성은 일반적으로 문자열 및 부울로 정의됩니다. 일 대 다 관계가 있는 경우 복합 속성이 배열로 대신 정의됩니다. Azure Policy에서는 배열이 여러 가지 방법으로 사용됩니다.

- 여러 옵션을 제공하기 위한 [정의 매개 변수](../concepts/definition-structure.md#parameters) 유형
- **in** 또는 **notIn** 조건을 사용하는 [정책 규칙](../concepts/definition-structure.md#policy-rule)의 일부
- [\[\*\] 별칭](../concepts/definition-structure.md#understanding-the--alias)을 평가하는 정책 규칙의 일부:
  - **없음** , **임의** 또는 **모두** 같은 시나리오
  - **count** 를 사용하는 복잡한 시나리오
- 기존 배열을 바꾸거나 기존 배열에 추가하는 [추가 효과](../concepts/effects.md#append)

이 문서에서는 Azure Policy의 각 사용법을 설명하고 몇 가지 예제 정의를 제공합니다.

## <a name="parameter-arrays"></a>매개 변수 배열

### <a name="define-a-parameter-array"></a>매개 변수 배열 정의

매개 변수를 배열로 정의하면 둘 이상의 값이 필요한 경우 유연한 정책이 가능합니다.
이 정책 정의를 사용하면 매개 변수 **allowedLocations** 에 단일 위치를 사용할 수 있으며 기본값은 _eastus2_ 입니다.

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

**type** 이 _string_ 이었으므로 정책을 할당할 때 하나의 값만 설정할 수 있습니다. 이 정책이 할당되면 범위의 리소스가 단일 Azure 지역 내에서만 허용됩니다. 대부분의 정책 정의는 _eastus2_ , _eastus_ 및 _westus2_ 허용 같은 승인된 옵션 목록을 허용해야 합니다.

여러 옵션을 허용하는 정책 정의를 만들려면 _array_ **type** 을 사용합니다. 동일한 정책을 다음과 같이 다시 작성할 수 있습니다.

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> 정책 정의를 저장한 후에는 매개 변수의 **type** 속성을 변경할 수 없습니다.

이 새 매개 변수 정의는 정책 할당 중에 둘 이상의 값을 사용합니다. **allowedValues** 라는 배열 속성을 정의하면 할당 중에 사용 가능한 값이 미리 정의된 옵션 목록으로 추가 제한됩니다. **allowedValues** 사용은 선택 사항입니다.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>할당 중에 매개 변수 배열에 값 전달

Azure Portal을 통해 정책을 할당하는 경우 **type** _array_ 의 매개 변수가 단일 텍스트 상자로 표시됩니다. 힌트에는 "값을 구분하려면 ;을 사용하세요. (예: 런던;뉴욕)"라고 표시됩니다. _eastus2_ , _eastus_ 및 _westus2_ 의 허용되는 위치 값을 매개 변수에 전달하려면 다음 문자열을 사용합니다.

`eastus2;eastus;westus2`

Azure CLI, Azure PowerShell 또는 REST API를 사용하는 경우 매개 변수 값의 형식이 다릅니다. 값은 매개 변수의 이름도 포함하는 JSON 문자열을 통해 전달됩니다.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

각 SDK에서 이 문자열을 사용하려면 다음 명령을 사용합니다.

- Azure CLI: **params** 매개 변수가 지정된 [az policy assignment create](/cli/azure/policy/assignment#az-policy-assignment-create) 명령
- Azure PowerShell: **PolicyParameter** 매개 변수가 지정된 [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) cmdlet
- REST API: 요청 본문에 **properties.parameters** 속성 값으로 포함된 _PUT_ [create](/rest/api/resources/policyassignments/create) 작업

## <a name="array-conditions"></a>배열 조건

매개 변수의 _array_
**type** 을 사용할 수 있는 정책 규칙 [조건](../concepts/definition-structure.md#conditions)은 `in` 및 `notIn`으로 제한됩니다. `equals` 조건을 사용하는 다음 정책 정의를 예로 들어 보겠습니다.

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Azure Portal을 통해 이 정책 정의를 만들려고 하면 다음 오류 메시지와 같은 오류가 발생합니다.

- "유효성 검사 오류로 인해 '{GUID}' 정책을 매개 변수화할 수 없습니다. 정책 매개 변수가 제대로 정의되어 있는지 확인하세요. '언어 식 '[parameters('allowedLocations')]'의 평가 결과가 'Array' 유형입니다. 올바른 유형은 'String'입니다.'라는 내부 예외가 발생했습니다."

`equals` 조건의 올바른 **type** 은 _string_ 입니다. **allowedLocations** 가 **type** _array_ 로 정의되었으므로 정책 엔진이 언어 식을 평가하고 오류를 throw합니다. `in` 및 `notIn` 조건을 사용하면 정책 엔진이 언어 식에서 **type** _array_ 를 예상합니다. 이 오류 메시지를 해결하려면 `equals`를 `in` 또는 `notIn`으로 변경하세요.

## <a name="referencing-array-resource-properties"></a>배열 리소스 속성 참조

많은 사용 사례에서 계산 된 리소스의 배열 속성 작업을 수행 해야 합니다. 일부 시나리오에서는 전체 배열을 참조 해야 합니다 (예: 길이 확인). 다른 경우에는 각 개별 배열 멤버에 조건을 적용 해야 합니다. 예를 들어 모든 방화벽 규칙이 인터넷에서 액세스를 차단 하는지 확인 합니다. 리소스 속성을 참조할 수 Azure Policy 다양 한 방법 이해 및 배열 속성을 참조할 때 이러한 참조가 동작 하는 방식은 이러한 시나리오를 포함 하는 조건을 작성 하기 위한 키입니다.

### <a name="referencing-resource-properties"></a>리소스 속성 참조
리소스 속성은 [별칭](../concepts/definition-structure.md#aliases) 을 사용 하 여 Azure Policy에서 참조할 수 있으며, Azure Policy 내에서 리소스 속성의 값을 참조 하는 두 가지 방법이 있습니다.

- [필드](../concepts/definition-structure.md#fields) 조건을 사용 하 여 선택한 **모든** 리소스 속성이 조건을 충족 하는지 여부를 확인 합니다. 예제:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- `field()`함수를 사용 하 여 속성 값에 액세스 합니다. 예제:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

필드 조건에는 암시적 "모두" 동작이 포함 됩니다. 별칭이 값의 컬렉션을 나타내는 경우 모든 개별 값이 조건을 충족 하는지 여부를 확인 합니다. `field()`함수는 별칭이 나타내는 값을 있는 그대로 반환 합니다 .이 값은 다른 템플릿 함수에 의해 조작 될 수 있습니다.

### <a name="referencing-array-fields"></a>배열 필드 참조

배열 리소스 속성은 일반적으로 두 가지 별칭 형식으로 표시 됩니다. 하나에 연결 된 ' normal ' 별칭 및 [배열 별칭](../concepts/definition-structure.md#understanding-the--alias) `[*]` :

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>배열 참조

첫 번째 별칭은 요청 콘텐츠의 속성 값인 단일 값을 나타냅니다 `stringArray` . 해당 속성의 값은 배열 이므로 정책 조건에서 그다지 유용 하지 않습니다. 다음은 그 예입니다.

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

이 조건은 전체 `stringArray` 배열을 단일 문자열 값과 비교 합니다. 을 비롯 하 여 대부분의 조건은 문자열 `equals` 값만 허용 하므로 배열을 문자열과 비교할 때 많은 사용이 없습니다. 배열 속성을 참조 하는 주요 시나리오는 존재 여부를 확인 하는 경우에 유용 합니다.

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

함수를 사용 하면 `field()` 반환 되는 값은 요청 콘텐츠의 배열이 며,이 배열은 배열 인수를 허용 하는 [지원 되는 템플릿 함수](../concepts/definition-structure.md#policy-functions) 에 사용할 수 있습니다. 예를 들어 다음 조건은의 길이가 0 보다 큰지 여부를 확인 합니다 `stringArray` .

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>배열 멤버 컬렉션 참조

구문을 사용 하는 별칭은 배열 속성을 선택 하는 것과는 `[*]` 다른 **배열 속성에서 선택한 속성 값의 컬렉션** 을 나타냅니다. 의 경우의 `Microsoft.Test/resourceType/stringArray[*]` 모든 멤버가 포함 된 컬렉션을 반환 합니다 `stringArray` . 앞서 언급 했 듯이, `field` 조건은 선택한 모든 리소스 속성이 조건을 충족 하는지 확인 하므로의 **모든** 멤버가 `stringArray` ' "값" '과 같은 경우에만 다음 조건이 충족 됩니다.

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

배열에 개체가 포함 된 경우 `[*]` 별칭을 사용 하 여 각 배열 멤버의 특정 속성 값을 선택할 수 있습니다. 예제:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

이 조건은의 모든 속성 값이 `property` 와 같으면 true입니다 `objectArray` `"value"` .

함수를 사용 하 여 `field()` 배열 별칭을 참조 하는 경우 반환 되는 값은 선택한 모든 값의 배열입니다. 이 동작은 함수의 일반적인 사용 사례 `field()` , 리소스 속성 값에 템플릿 함수를 적용 하는 기능이 매우 제한적 임을 의미 합니다. 이 경우에 사용할 수 있는 유일한 템플릿 함수는 배열 인수를 허용 하는 함수입니다. 예를 들어를 사용 하 여 배열의 길이를 가져올 수 `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` 있습니다. 그러나 각 배열 멤버에 템플릿 함수를 적용 하 고 원하는 값과 비교 하는 것과 같은 복잡 한 시나리오는 식을 사용 하는 경우에만 가능 `count` 합니다. 자세한 내용은 [Count 식](#count-expressions)을 참조 하세요.

요약 하자면, 다음 예제 리소스 내용과 다양 한 별칭에서 반환 된 선택 된 값을 참조 하세요.

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

예제 리소스 콘텐츠에서 필드 조건을 사용 하는 경우 결과는 다음과 같습니다.

| Alias | 선택한 값 |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | 빈 값 컬렉션입니다. |
| `Microsoft.Test/resourceType/missingArray[*].property` | 빈 값 컬렉션입니다. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

`field()`예제 리소스 내용에서 함수를 사용 하는 경우 결과는 다음과 같습니다.

| 식 | 반환 값 |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

## <a name="count-expressions"></a>개수 식

[개수](../concepts/definition-structure.md#count) 식은 조건을 충족 하는 배열 멤버 수를 계산 하 고 해당 개수를 대상 값과 비교 합니다. `Count` 는 조건과 비교 하 여 배열을 보다 직관적이 고 다양 하 게 평가할 수 `field` 있습니다. 구문은 다음과 같습니다.

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

' Where ' 조건 없이 사용 하는 경우은 `count` 단순히 배열의 길이를 반환 합니다. 이전 섹션의 예제 리소스 콘텐츠를 사용 하 여 다음 식의는 `count` `true` `stringArray` 세 개의 멤버를 포함 하므로로 계산 됩니다.

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

이 동작은 중첩 된 배열 에서도 작동 합니다. 예를 들어 배열 `count` `true` 에 4 개의 배열 멤버가 있으므로 다음 식은로 계산 됩니다 `nestedArray` .

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

의 거듭제곱 `count` 은 `where` 조건에 있습니다. 지정 Azure Policy 된 경우 배열 멤버를 열거 하 고 각 조건에 대해 평가 하 여 계산 된 배열 멤버 수를 계산 `true` 합니다. 특히, 조건 평가의 각 반복에서 `where` Azure Policy는 단일 배열 멤버 * **i** _를 선택 하 고 * `where` i가 array_ *의 유일한 멤버인 _것 처럼 *_i_*_ * i _ 조건에 대해 리소스 콘텐츠를 평가 합니다. 각 반복에서 배열 멤버를 하나만 사용할 수 있는 경우 각 개별 배열 멤버에 복잡 한 조건을 적용 하는 방법이 제공 됩니다.

예제:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
식을 계산 하기 위해 `count` Azure Policy는 `where` 의 각 멤버에 대해 한 번씩 조건을 3 번 평가 하 여 `stringArray` 계산 된 횟수를 계산 `true` 합니다. 조건에서 `where` 배열 멤버를 참조 하는 경우 `Microsoft.Test/resourceType/stringArray[*]` 의 모든 멤버를 선택 하는 대신 항상 `stringArray` 단일 배열 멤버를 선택 합니다.

| 반복 | 선택한 `Microsoft.Test/resourceType/stringArray[*]` 값 | `where` 평가 결과 |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

따라서는을 `count` 반환 `1` 합니다.

더 복잡 한 식은 다음과 같습니다.
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| 반복 | 선택한 값 | `where` 평가 결과 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

따라서는을 `count` 반환 `1` 합니다.

`where`식이 **전체** 요청 내용에 대해 계산 되 고 (현재 열거 되는 배열 멤버만 변경 됨), `where` 조건이 배열 외부의 필드를 참조할 수도 있음을 의미 합니다.
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| 반복 | 선택한 값 | `where` 평가 결과 |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

중첩 된 개수 식도 사용할 수 있습니다.
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| 외부 루프 반복 | 선택한 값 | 내부 루프 반복 | 선택한 값 |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

### <a name="the-field-function-inside-where-conditions"></a>`field()`조건 내의 함수 `where`

`field()`조건 내에서 함수가 동작 하는 방식은 `where` 다음과 같은 개념을 기반으로 합니다.
1. 배열 별칭은 모든 배열 멤버에서 선택한 값의 컬렉션으로 확인 됩니다.
1. `field()` 배열 별칭을 참조 하는 함수는 선택한 값이 포함 된 배열을 반환 합니다.
1. 조건 내에서 계산 된 배열 별칭을 참조 하면 `where` 현재 반복에서 계산 되는 배열 멤버에서 단일 값이 선택 된 컬렉션이 반환 됩니다.

이 동작은 계산 된 배열 멤버를 조건 내에서 함수로 참조할 때 `field()` `where` **단일 멤버를 포함 하는 배열을 반환** 함을 의미 합니다. 이는 직관적이 지 않을 수 있지만 배열 별칭이 항상 선택 된 속성의 컬렉션을 반환 한다는 것과 일치 합니다. 예를 들면 다음과 같습니다.

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| 반복 | 식 값 | `where` 평가 결과 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

따라서 함수를 사용 하 여 계산 된 배열 별칭의 값에 액세스 해야 하는 경우이 작업을 수행 하는 `field()` 방법은 템플릿 함수를 사용 하 여 래핑하는 것입니다 `first()` .

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| 반복 | 식 값 | `where` 평가 결과 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

유용한 예제는 [Count 예](../concepts/definition-structure.md#count-examples)를 참조 하세요.

## <a name="modifying-arrays"></a>배열 수정

만들거나 업데이트 하는 동안 리소스에 대 한 alter 속성을 [추가](../concepts/effects.md#append) 및 [수정](../concepts/effects.md#modify) 합니다. 배열 속성으로 작업할 때 이러한 효과의 동작은 작업이 별칭을 수정 하려고 하는지 여부에 따라 달라 집니다  **\[\*\]** .

> [!NOTE]
> 별칭에 효과를 사용 하 `modify` 는 것은 현재 **미리 보기** 상태입니다.

|Alias |효과 | 결과 |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure Policy는 효과 세부 정보에 지정 된 전체 배열을 누락 된 경우 추가 합니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` with `add` 작업 | Azure Policy는 효과 세부 정보에 지정 된 전체 배열을 누락 된 경우 추가 합니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` with `addOrReplace` 작업 | Azure Policy 기존 배열을 누락 하거나 바꾸면 효과 세부 정보에 지정 된 전체 배열을 추가 합니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy 효과 세부 정보에 지정 된 배열 멤버를 추가 합니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` with `add` 작업 | Azure Policy 효과 세부 정보에 지정 된 배열 멤버를 추가 합니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` with `addOrReplace` 작업 | Azure Policy 기존 배열 멤버를 모두 제거 하 고 효과 세부 정보에 지정 된 배열 멤버를 추가 합니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy는 `action` 각 배열 멤버의 속성에 값을 추가 합니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` with `add` 작업 | Azure Policy는 `action` 각 배열 멤버의 속성에 값을 추가 합니다. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` with `addOrReplace` 작업 | Azure Policy는 `action` 각 배열 멤버의 기존 속성을 추가 하거나 바꿉니다. |

자세한 내용은 [추가 예제](../concepts/effects.md#append-examples)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](programmatically-create.md)하는 방법을 이해합니다.
- [규정 비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
