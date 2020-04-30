---
title: 리소스의 배열 속성에 대 한 작성자 정책
description: 배열 매개 변수 및 배열 언어 식에 대 한 작업을 수행 하 고, [*] 별칭을 평가 하 고 Azure Policy 정의 규칙을 사용 하 여 요소를 추가 하는 방법을 알아봅니다.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280665"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure 리소스의 배열 속성에 대 한 작성자 정책

Azure Resource Manager 속성은 일반적으로 문자열 및 부울로 정의 됩니다. 일 대 다 관계가 있는 경우 대신 복합 속성이 배열로 정의 됩니다. Azure Policy 배열은 여러 가지 방법으로 사용 됩니다.

- 여러 옵션을 제공 하는 [정의 매개 변수의](../concepts/definition-structure.md#parameters)형식입니다.
- 또는 **Notin** **의 조건을** 사용 하는 [정책 규칙](../concepts/definition-structure.md#policy-rule) 의 일부
- [ \[ \* 평가할 \] 별칭](../concepts/definition-structure.md#understanding-the--alias) 을 평가 하는 정책 규칙의 일부는 다음과 같습니다.
  - **없음**, **Any**또는 **All** 등의 시나리오
  - **개수** 를 포함 하는 복잡 한 시나리오
- 기존 배열에 바꾸기 또는 기존 배열에 추가에 대 한 추가 [효과](../concepts/effects.md#append)

이 문서에서는 Azure Policy의 각 사용에 대해 설명 하 고 몇 가지 예제 정의를 제공 합니다.

## <a name="parameter-arrays"></a>매개 변수 배열

### <a name="define-a-parameter-array"></a>매개 변수 배열 정의

매개 변수를 배열로 정의 하면 둘 이상의 값이 필요한 경우 정책 유연성이 가능 합니다.
이 정책 정의는 매개 변수 **allowedlocations** 에 대 한 단일 위치 및 기본값 _eastus2_를 허용 합니다.

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

**Type** 은 _문자열_이므로 정책을 할당할 때 하나의 값만 설정할 수 있습니다. 이 정책이 할당 된 경우 범위의 리소스는 단일 Azure 지역 내 에서만 허용 됩니다. 대부분의 정책 정의는 승인 된 옵션 목록 (예: _eastus2_, _에서는 eastus_및 _westus2_허용)을 허용 해야 합니다.

여러 옵션을 허용 하는 정책 정의를 만들려면 _배열_ **유형을**사용 합니다. 동일한 정책을 다음과 같이 다시 작성할 수 있습니다.

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
> 정책 정의를 저장 한 후에는 매개 변수의 **type** 속성을 변경할 수 없습니다.

이 새 매개 변수 정의는 정책 할당 중에 둘 이상의 값을 사용 합니다. 배열 속성 **Allowedvalues** 가 정의 된 상태에서 할당 중에 사용할 수 있는 값은 미리 정의 된 선택 목록으로 추가로 제한 됩니다. **Allowedvalues** 의 사용은 선택 사항입니다.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>할당 하는 동안 매개 변수 배열에 값을 전달 합니다.

Azure Portal를 통해 정책을 할당할 때 _배열_ **형식의** 매개 변수는 단일 텍스트 상자로 표시 됩니다. 힌트는 "Use; 값을 구분 하려면입니다. (예: 런던;) 뉴욕) ". _Eastus2_, _에서는 eastus_및 _westus2_ 의 허용 되는 위치 값을 매개 변수에 전달 하려면 다음 문자열을 사용 합니다.

`eastus2;eastus;westus2`

매개 변수 값의 형식은 Azure CLI, Azure PowerShell 또는 REST API를 사용할 때 다릅니다. 값은 매개 변수의 이름도 포함 하는 JSON 문자열을 통해 전달 됩니다.

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

각 SDK에서이 문자열을 사용 하려면 다음 명령을 사용 합니다.

- Azure CLI: 명령 [az policy 대입문](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) with parameter **params**
- Azure PowerShell: Cmdlet [AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) **PolicyParameter** 매개 변수
- REST API: _PUT_ [만들기](/rest/api/resources/policyassignments/create) 작업에서 요청 본문의 일부로 서 **속성. parameters** 속성 값으로 설정 합니다.

## <a name="policy-rules-and-arrays"></a>정책 규칙 및 배열

### <a name="array-conditions"></a>배열 조건

매개 변수의 _배열_
**형식이** 와 함께 사용 될 수 있는 정책 규칙 `in` [조건은](../concepts/definition-structure.md#conditions) 및 `notIn`로 제한 됩니다. 조건 `equals` 으로 다음 정책 정의를 예로 들어 보겠습니다.

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

Azure Portal를 통해이 정책 정의를 만들려고 하면 다음 오류 메시지와 같은 오류가 발생 합니다.

- "유효성 검사 오류로 인해 ' {GUID} ' 정책을 매개 변수화 할 수 없습니다. 정책 매개 변수가 제대로 정의 되어 있는지 확인 하세요. 언어 식 ' [parameters (' allowedLocations ')] '의 내부 예외 평가 결과는 ' Array ' 형식입니다. 필요한 형식은 ' String '입니다. '. "

필요한 `equals` 조건 **유형은** _문자열_입니다. **Allowedlocations** **형식** _배열로_정의 되므로 정책 엔진은 언어 식을 평가 하 고 오류를 throw 합니다. `in` 및 `notIn` 조건에서 정책 엔진은 언어 식에 **형식** _배열이_ 필요 합니다. 이 오류 메시지를 해결 하려면 `equals` `in` 또는 `notIn`로 변경 합니다.

### <a name="evaluating-the--alias"></a>[*] 별칭 평가

이름에 ** \[ \* ** 연결 된 별칭은 **형식이** _배열_임을 의미 합니다. 전체 배열의 값을 계산 하는 대신,를 ** \[ \* ** 사용 하 여 배열의 각 요소를 개별적으로 계산 하 고 둘 사이에서 계산할 수 있습니다. 다음 세 가지 표준 시나리오가 있습니다. 이러한 시나리오는 항목 평가 별로 유용 합니다. _None_, _Any_또는 _All_ 요소와 일치 합니다. 복잡 한 시나리오의 경우 [count](../concepts/definition-structure.md#count)를 사용 합니다.

정책 엔진은 **if** 규칙이 true로 평가 되는 **경우에만** 의 **효과** 를 트리거합니다.
이 사실은 배열의 개별 요소를 평가 하는 방식을 ** \[ \* ** 이해 하는 것이 중요 합니다.

아래 시나리오 테이블의 정책 규칙 예:

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

**IpRules** 배열은 아래 시나리오 테이블에 대해 다음과 같이 나타납니다.

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

아래 각 조건 예의 경우를 `<field>` 로 `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`바꿉니다.

다음 결과는 조건 및 예제 정책 규칙의 조합 및 위의 기존 값 배열에 대 한 결과입니다.

|조건 |결과 | 시나리오 |설명 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |일치 항목 없음 |한 배열 요소는 false (127.0.0.1! = 127.0.0.1)로 계산 되 고 1은 true (127.0.0.1! = 192.168.1.1)로 계산 되기 때문에 **참고** 요소는 _false_ 이며 효과가 트리거되지 않습니다. |
|`{<field>,"notEquals":"10.0.4.1"}` |정책 효과 |일치 항목 없음 |두 배열 _요소는 모두_ true (10.0.4.1! = 127.0.0.1 and 10.0.4.1! = 192.168.1.1)로 평가 되므로 **참고** |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |정책 효과 |하나 이상의 일치 항목 |한 배열 요소는 false (127.0.0.1! = 127.0.0.1)로 계산 되 고 1은 true (127.0.0.1! = 192.168.1.1)로 계산 되므로 **참고** 요소는 _false_입니다. 논리 연산자는 _false_가**아닌** true로 평가 되므로 효과가 트리거됩니다. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |하나 이상의 일치 항목 |두 배열 _요소는 모두_true (10.0.4.1! = 127.0.0.1 and 10.0.4.1! = 192.168.1.1)로 평가 되므로 **참고** 로, 논리 연산자는 false (**not** _true_)로 계산 되므로 결과가 트리거되지 않습니다. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |정책 효과 |모두 일치 하지 않음 |한 배열 요소는 true (127.0.0.1 = = 127.0.0.1)로 계산 되 고 하나는 false (127.0.0.1 = = 192.168.1.1)로 계산 되므로 **Equals** 조건은 _false_입니다. 논리 연산자는 _false_가**아닌** true로 평가 되므로 효과가 트리거됩니다. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |정책 효과 |모두 일치 하지 않음 |두 배열 요소는 모두 false (10.0.4.1 = = 127.0.0.1 and 10.0.4.1 = = 192.168.1.1)로 평가 되므로 **Equals** 조건은 _false_입니다. 논리 연산자는 _false_가**아닌** true로 평가 되므로 효과가 트리거됩니다. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |모든 일치 |한 배열 요소는 true (127.0.0.1 = = 127.0.0.1)로 계산 되 고 하나는 false (127.0.0.1 = = 192.168.1.1)로 계산 되므로 **Equals** 조건은 _false_ 이며 효과가 트리거되지 않습니다. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |모든 일치 |두 배열 요소는 모두 false (10.0.4.1 = = 127.0.0.1 and 10.0.4.1 = = 192.168.1.1)로 평가 되므로 **Equals** 조건은 _false_ 이며 효과가 트리거되지 않습니다. |

## <a name="the-append-effect-and-arrays"></a>추가 효과 및 배열

[추가 효과](../concepts/effects.md#append) 는 **details** 가 ** \[ \* ** 별칭 인지 여부에 따라 다르게 동작 합니다.

- ** \[ \* ** 별칭이 아니면 append는 전체 배열을 **value** 속성으로 바꿉니다.
- 별칭인 경우 append는 기존 배열에 value 속성을 추가 하거나 새 배열을 만듭니다. **value** ** \[ \* **

자세한 내용은 [추가 예제](../concepts/effects.md#append-examples)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토 합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 알아봅니다.
- [비준수 리소스](remediate-resources.md)를 수정 하는 방법에 대해 알아봅니다.
- [Azure 관리 그룹을 사용](../../management-groups/overview.md)하 여 리소스를 구성 하는 관리 그룹을 검토 합니다.
