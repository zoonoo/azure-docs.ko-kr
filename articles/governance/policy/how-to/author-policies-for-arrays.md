---
title: Azure 리소스의 배열 속성에 대해 작성자 정책
description: 배열 매개 변수를 만들고, 언어 식을 배열에 대 한 규칙을 만들, [*] 별칭을 평가 하는 데 Azure Policy 정의 규칙을 사용 하 여 기존 배열에 요소를 추가 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 38cf6decb8e61768faa9680058f6366e1550ba40
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274726"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure 리소스의 배열 속성에 대해 작성자 정책

Azure Resource Manager 속성은 일반적으로 문자열 및 부울으로 정의 됩니다. 1 대 다 관계가 존재 하는 경우 복합 속성을 배열로 대신 정의 됩니다. Azure Policy에 배열은 여러 가지 방법으로 사용 됩니다.

- 형식의 [definition 매개 변수](../concepts/definition-structure.md#parameters), 여러 옵션을 제공
- 부분을 [정책 규칙](../concepts/definition-structure.md#policy-rule) 조건을 사용 하 여 **에서** 또는 **notIn**
- 평가 하는 정책 규칙의 일부를 [ \[ \* \] 별칭](../concepts/definition-structure.md#understanding-the--alias) 와 같은 특정 시나리오를 평가 하 **None**를 **모든**, 또는  **모든**
- 에 [효과 추가](../concepts/effects.md#append) 바꾸거나 기존 배열에 추가 하려면

이 문서에서는 Azure Policy로 사용 하 여 각를 설명 하 고 몇 가지 예제 정의 제공 합니다.

## <a name="parameter-arrays"></a>매개 변수 배열

### <a name="define-a-parameter-array"></a>매개 변수 배열을 정의 합니다.

둘 이상의 값이 필요한 경우 정책 유연성을 허용 배열 매개 변수를 정의 합니다.
이 정책 정의 매개 변수는 단일 위치를 사용 하면 **allowedLocations** 이며 기본값 _eastus2_:

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

로 **형식** 되었습니다 _문자열_경우 하나의 값을 설정할 수 있습니다만, 정책을 할당 합니다. 이 정책에 할당 된 경우 리소스 범위에서 단일 Azure 지역 내 에서만 허용 됩니다. 대부분의 정책 정의 허용 하는 등의 승인 된 옵션의 목록을 허용 해야 합니다. _eastus2_하십시오 _eastus_, 및 _westus2_합니다.

여러 옵션을 허용 하도록 정책 정의 만들려면 사용 합니다 _배열_ **형식**합니다. 동일한 정책은 다음과 같이 다시 작성할 수 있습니다.

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
> 정책 정의 저장 합니다 **형식** 매개 변수에서 속성을 변경할 수 없습니다.

이 새 매개 변수 정의 정책 할당 중 둘 이상의 값을 사용합니다. 배열 속성을 사용 하 여 **allowedValues** 정의 할당 하는 동안 사용 가능한 값 추가 됩니다. 미리 정의 된 선택 목록으로 제한 합니다. 이용 **allowedValues** 선택 사항입니다.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>매개 변수 배열을 할당 하는 동안 값 전달

Azure portal의 매개 변수를 통해 정책을 할당할 때 **형식** _배열_ 단일 텍스트 상자로 표시 됩니다. 힌트를 "; 사용 라는 값을 구분 합니다. (예: 런던; New York) ". 허용 된 위치 값을 전달할 _eastus2_를 _eastus_, 및 _westus2_ 매개 변수에 다음 문자열을 사용 합니다.

`eastus2;eastus;westus2`

Azure CLI, Azure PowerShell 또는 REST API를 사용 하는 경우 매개 변수 값의 형식과가 다릅니다. 값 매개 변수의 이름을 포함 하는 JSON 문자열을 통해 전달 됩니다.

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

각 SDK를 사용 하 여이 문자열을 사용 하려면 다음 명령을 사용 합니다.

- Azure CLI: 명령 [az 정책 할당 만들기](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) 매개 변수를 사용 하 여 **매개 변수**
- Azure PowerShell: Cmdlet [새로 만들기-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) 매개 변수를 사용 하 여 **PolicyParameter**
- REST API: 에 _배치_ [만듭니다](/rest/api/resources/policyassignments/create) 값으로 요청 본문의 일부로 작업을 **properties.parameters** 속성

## <a name="policy-rules-and-arrays"></a>정책 규칙 및 배열

### <a name="array-conditions"></a>배열 조건

정책 규칙이 [조건을](../concepts/definition-structure.md#conditions) 는 _배열_
**형식** 매개 변수의 사용할 수 있습니다 사용 하 여 제한 됩니다 `in` 및 `notIn`합니다. 조건 사용 하 여 다음 정책 정의 수행할 `equals` 예를 들어:

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

이 오류 메시지와 같은 오류 시키는 Azure 포털을 통해이 정책 정의 만들기 하려고 합니다.

- "'{GUID}' 정책은 없습니다 매개 변수화 할 유효성 검사 오류가 발생 합니다. 정책 매개 변수가 제대로 정의 되어 있는지 확인 하세요. 내부 예외 ' 언어 식 '[parameters('allowedLocations')]'의 결과 형식이 'Array' 평가 필요한 형식은 'String' '. "

예상 **형식** 조건의 `equals` 됩니다 _문자열_합니다. 이후 **allowedLocations** 로 정의 됩니다 **형식** _배열_, 정책 엔진 언어 식을 계산 하 고 오류를 throw 합니다. 사용 하 여는 `in` 및 `notIn` 조건과 정책 엔진은 합니다 **형식** _배열_ 언어 식에서. 이 오류를 해결 하려면 변경할 `equals` 하나로 `in` 또는 `notIn`합니다.

### <a name="evaluating-the--alias"></a>[*] 별칭 평가

있는 별칭 **[\*]** 이름에 연결 된 표시를 **형식** 는 _배열_. 전체 배열 값을 평가 하는 대신 **[\*]** 배열의 각 요소를 평가할 수 있도록 합니다. 세 가지 시나리오에는이 항목 평가 당이 유용 합니다. None, Any, 및 모든

정책 엔진 트리거를 **효과** 에서 **한 다음** 경우에만 **경우** 규칙이 true로 평가 합니다.
이 사실은 컨텍스트에서 서 알아야 **[\*]** 배열의 각 개별 요소를 평가 합니다.

아래 시나리오 테이블에 대 한 예제 정책 규칙:

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

합니다 **ipRules** 배열 아래 시나리오 테이블에는 다음과 같습니다.

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

각 조건은 아래 예에 대 한 대체 `<field>` 사용 하 여 `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`입니다.

다음 결과 예제 정책 규칙 조건 및 위의 기존 값 배열을 결합 합니다.

|조건 |결과 |설명 |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |아무 작업도 수행 |배열 요소 하나를 false로 평가 (127.0.0.1! = 127.0.0.1) true로 개와 (127.0.0.1! 192.168.1.1 =) 이므로 **notEquals** 조건은 _false_ 및 효과 트리거되지 않습니다. |
|`{<field>,"notEquals":"10.0.4.1"}` |정책 적용 |두 배열 요소를 true로 평가 (10.0.4.1! = 127.0.0.1 및 10.0.4.1! = 192.168.1.1) 이므로 **notEquals** 조건은 _true_ 효과 트리거됩니다. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |정책 적용 |True로 계산 되는 배열 요소가 하나 (127.0.0.1 127.0.0.1 = =) 및 false로 (127.0.0.1 192.168.1.1 = =) 이므로 **Equals** 조건은 _false_합니다. True로 계산 되는 논리 연산자 (**되지** _false_) 이므로 효과 트리거됩니다. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |정책 적용 |배열 요소 둘 다 false로 계산 됩니다 (10.0.4.1 = = 127.0.0.1 및 10.0.4.1 192.168.1.1 = =) 이므로 **Equals** 조건은 _false_합니다. True로 계산 되는 논리 연산자 (**되지** _false_) 이므로 효과 트리거됩니다. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |정책 적용 |배열 요소 하나를 false로 평가 (127.0.0.1! = 127.0.0.1) true로 개와 (127.0.0.1! = 192.168.1.1) 이므로 **notEquals** 조건은 _false_합니다. True로 계산 되는 논리 연산자 (**되지** _false_) 이므로 효과 트리거됩니다. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |아무 작업도 수행 |두 배열 요소를 true로 평가 (10.0.4.1! = 127.0.0.1 및 10.0.4.1! = 192.168.1.1) 이므로 **notEquals** 조건은 _true_합니다. False로 계산 되는 논리 연산자 (**되지** _true_) 이므로 효과 트리거되지 않습니다. |
|`{<field>,"Equals":"127.0.0.1"}` |아무 작업도 수행 |True로 계산 되는 배열 요소가 하나 (127.0.0.1 127.0.0.1 = =) 및 false로 (127.0.0.1 192.168.1.1 = =) 이므로 **Equals** 조건은 _false_ 효과 트리거되지 및 합니다. |
|`{<field>,"Equals":"10.0.4.1"}` |아무 작업도 수행 |두 배열 요소를 false로 평가 (10.0.4.1 = = 127.0.0.1 및 10.0.4.1 192.168.1.1 = =) 이므로 **Equals** 조건은 _false_ 및 효과 트리거되지 않습니다. |

## <a name="the-append-effect-and-arrays"></a>추가 효과 및 배열

[효과 추가](../concepts/effects.md#append) 경우에 따라 다르게 동작 합니다 **details.field** 는 **[\*]** 여부 별칭입니다.

- 하지 않은 경우는 **[\*]** 별칭을 추가 사용 하 여 배열 전체를 대체 합니다 **값** 속성
- 경우는 **[\*]** 추가 별칭을 추가 합니다 **값** 속성을 기존 배열 또는 새 배열을 만듭니다

자세한 내용은 참조는 [예제 추가](../concepts/effects.md#append-examples)합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](programmatically-create.md)하는 방법을 이해합니다.
- [비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.