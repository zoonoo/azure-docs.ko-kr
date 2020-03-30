---
title: 리소스에 대한 배열 속성에 대한 정책 작성
description: 배열 매개 변수 및 배열 언어 표현식으로 작업하고[*] 별칭을 평가하고 Azure Policy 정의 규칙을 사용하여 요소를 부화하는 방법을 알아봅니다.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280665"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure 리소스의 배열 속성에 대한 정책 작성

Azure Resource Manager 속성은 일반적으로 문자열 및 부울로 정의됩니다. 일대다 관계가 있는 경우 복잡한 속성은 대신 배열로 정의됩니다. Azure Policy에서 배열은 여러 가지 방법으로 사용됩니다.

- 정의 매개 [변수의](../concepts/definition-structure.md#parameters)유형은 여러 옵션을 제공합니다.
- 의 조건을 사용하는 [정책 규칙의](../concepts/definition-structure.md#policy-rule) **일부또는** **not에서**
- 평가할 [ \[ \* \] 별칭을](../concepts/definition-structure.md#understanding-the--alias) 평가하는 정책 규칙의 일부입니다.
  - **없음,** **없음**또는 **모두와** 같은 시나리오
  - **개수가** 포함된 복잡한 시나리오
- 기존 배열을 바꾸거나 추가할 [추가 효과](../concepts/effects.md#append)

이 문서에서는 Azure Policy의 각 사용에 대해 다루며 몇 가지 예제 정의를 제공합니다.

## <a name="parameter-arrays"></a>매개 변수 배열

### <a name="define-a-parameter-array"></a>매개 변수 배열 정의

매개 변수를 배열로 정의하면 두 개 이상의 값이 필요할 때 정책 유연성을 사용할 수 있습니다.
이 정책 정의는 매개 변수에 대한 단일 위치를 **허용허용위치** 및 기본값 _eastus2_:

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

**형식이** _문자열이었기_때문에 정책을 할당할 때 하나의 값만 설정할 수 있습니다. 이 정책이 할당된 경우 범위의 리소스는 단일 Azure 리전 내에서만 허용됩니다. 대부분의 정책 정의는 _eastus2,_ _eastus_및 _westus2_를 허용하는 것과 같은 승인된 옵션 목록을 허용해야 합니다.

여러 옵션을 허용하는 정책 정의를 만들려면 _배열_ **형식을**사용합니다. 다음과 같이 동일한 정책을 다시 작성할 수 있습니다.

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
> 정책 정의가 저장되면 매개 변수의 **형식** 속성을 변경할 수 없습니다.

이 새 매개 변수 정의는 정책 할당 중에 두 개 이상의 값을 사용합니다. array **속성이 허용됨값을** 정의하면 할당 중에 사용할 수 있는 값은 미리 정의된 선택 목록으로 제한됩니다. **허용된값의** 사용은 선택 사항입니다.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>할당 하는 동안 매개 변수 배열에 값을 전달

Azure 포털을 통해 정책을 할당할 때 **형식** _배열의_ 매개 변수가 단일 텍스트 상자로 표시됩니다. 힌트는 "사용; 을 사용하여 값을 분리합니다. (예: 런던; 뉴욕)". _eastus2,_ _eastus_및 _westus2의_ 허용된 위치 값을 매개 변수에 전달하려면 다음 문자열을 사용합니다.

`eastus2;eastus;westus2`

Azure CLI, Azure PowerShell 또는 REST API를 사용할 때 매개 변수 값의 형식이 다릅니다. 값은 매개 변수의 이름도 포함하는 JSON 문자열을 통해 전달됩니다.

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

- Azure CLI: 매개 변수 **매개 변수를** 사용하여 [az 정책 할당 을 명령합니다.](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)
- Azure PowerShell: 매개 변수 정책 매개 **변수를** 사용 하 고 Cmdlet [새-AzPolicy할당](/powershell/module/az.resources/New-Azpolicyassignment)
- REST API: _PUT에서_ request Body의 일부로 작업을 해당 속성의 값으로 **만듭니다.parameters** 속성 [create](/rest/api/resources/policyassignments/create)

## <a name="policy-rules-and-arrays"></a>정책 규칙 및 배열

### <a name="array-conditions"></a>배열 조건


 _배열_**형식의** 매개 변수를 사용할 수 있는 정책 `in` `notIn`규칙 [조건은](../concepts/definition-structure.md#conditions) 및 로 제한됩니다. 조건을 `equals` 예로 들어 다음 정책 정의를 수행합니다.

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

Azure 포털을 통해 이 정책 정의를 만들면 다음과 같은 오류가 발생합니다.

- "유효성 검사 오류로 인해 정책 '{GUID}'를 매개 변수화할 수 없습니다. 정책 매개 변수가 제대로 정의되어 있는지 확인하십시오. 내부 예외 '언어 표현식의 평가 결과 '[parameters('allowed위치')]'는 '배열', 예상 형은 '문자열'입니다.

예상되는 조건 `equals` **유형은** _문자열입니다._ **allowedLocations** 형식 **type** _배열로_정의 되므로 정책 엔진 언어 식을 평가 하 고 오류를 throw 합니다. `in` 및 조건을 `notIn` 사용하면 정책 엔진은 언어 식의 **형식** _배열을_ 기대합니다. 이 오류 메시지를 해결하려면 `in` 로 `notIn`변경하거나 `equals` .

### <a name="evaluating-the--alias"></a>[*] 별칭 평가

이름에 첨부된 ** \[ \* ** 별칭은 **형식이** _배열임을_나타냅니다. 전체 배열의 ** \[ \* ** 값을 평가하는 대신 배열의 각 요소를 논리적 AND 로 개별적으로 평가할 수 있습니다. 항목별 평가에 는 없음, _없음_또는 _Any_ _모든_ 요소가 일치하는 세 가지 표준 시나리오가 있습니다. 복잡한 시나리오의 경우 [count를](../concepts/definition-structure.md#count)사용합니다.

정책 엔진은 **if** 규칙이 true로 평가될 **때만** **효과를** 트리거합니다.
이 사실은 배열의 각 개별 ** \[ \* ** 요소를 평가하는 방식의 맥락에서 이해하는 것이 중요합니다.

아래 시나리오 표에 대한 예제 정책 규칙:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

**ipRules** 배열은 아래 시나리오 표에 대 한 다음과 같습니다.

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

아래의 각 조건 `<field>` 예제에 대해 을 로 바꿉입니다. `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`

다음 결과는 조건과 예제 정책 규칙 및 위의 기존 값 배열의 조합의 결과입니다.

|조건 |결과 | 시나리오 |설명 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |일치하지 없음 |한 배열 요소는 false(127.0.0.1 != 127.0.0.1)로 평가하고 1개(127.0.0.1!= 192.168.1.1)로 평가되므로 **notEquals** 조건은 _false이고_ 효과가 트리거되지 않습니다. |
|`{<field>,"notEquals":"10.0.4.1"}` |정책 효과 |일치하지 없음 |두 배열 요소 모두 true(10.0.4.1 != 127.0.0.1 및 10.0.4.1 != 192.168.1.1)로 평가되므로 **notEquals** 조건이 _true이고_ 효과가 트리거됩니다. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |정책 효과 |하나 이상의 일치 |하나의 배열 요소는 false(127.0.0.1 != 127.0.0.1)로 평가하고 1개(127.0.0.1!= 192.168.1.1)로 평가하므로 **notEquals** 조건은 _false입니다._ 논리 연산자는 _true(false가_**아님)로** 평가하므로 효과가 트리거됩니다. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |하나 이상의 일치 |두 배열 요소 모두 true로 평가됩니다(10.0.4.1 != 127.0.0.1 및 10.0.4.1 != 192.168.1.1) **notEquals** 조건은 _true입니다._ 논리 연산자는 _false(true가_**아님)로** 평가하므로 효과가 트리거되지 않습니다. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |정책 효과 |모든 일치 |하나의 배열 요소는 true(127.0.0.1 =127.0.0.1)로 평가되고 하나는 false(127.0.0.1 == 192.168.1.1)로 평가되므로 **Equals** 조건은 _false입니다._ 논리 연산자는 _true(false가_**아님)로** 평가하므로 효과가 트리거됩니다. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |정책 효과 |모든 일치 |두 배열 요소 모두 false(10.0.4.1 == 127.0.0.1 및 10.0.4.1 == 192.168.1.1)로 평가되므로 **Equals** 조건은 _false입니다._ 논리 연산자는 _true(false가_**아님)로** 평가하므로 효과가 트리거됩니다. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |모든 일치 |한 배열 요소는 true(127.0.0.1 =127.0.1)와 false(127.0.0.1 == 192.168.1.1)로 평가되므로 **Equals** 조건이 _false이고_ 효과가 트리거되지 않습니다. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |모든 일치 |두 배열 요소 모두 false(10.0.4.1 =127.0.0.1 및 10.0.4.1 == 192.168.1.1)로 평가되므로 **Equals** 조건이 _false이고_ 효과가 트리거되지 않습니다. |

## <a name="the-append-effect-and-arrays"></a>부속 효과 및 배열

** \[ \* ** **details.field별칭인지** 여부에 따라 [부속 효과가](../concepts/effects.md#append) 다르게 작용합니다.

- 별칭이 ** \[ \* ** 아닌 경우 부속은 전체 배열을 **값** 속성으로 바꿉습니다.
- 별칭이 ** \[ \* ** 추가되면 기존 배열에 **값** 속성을 추가하거나 새 배열을 만듭니다.

자세한 내용은 추가 [예제를](../concepts/effects.md#append-examples)참조하십시오.

## <a name="next-steps"></a>다음 단계

- Azure 정책 [샘플의 예제를 검토합니다.](../samples/index.md)
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해 검토](../concepts/effects.md).
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 이해합니다.
- [비준수 리소스를 수정하는](remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)
