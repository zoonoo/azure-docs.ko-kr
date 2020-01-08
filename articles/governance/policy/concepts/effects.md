---
title: 효과 작동 방식 이해
description: Azure Policy 정의에는 규정 준수를 관리 하 고 보고 하는 방법을 결정 하는 다양 한 효과가 있습니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: fec2f966260d997b45be50554e0f41d5fd0491aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436357"
---
# <a name="understand-azure-policy-effects"></a>Azure Policy의 영향 파악

Azure Policy의 각 정책 정의는 단일 효과가 있습니다. 해당 효과는 정책 규칙이 일치되는 것으로 평가될 때 어떻게 되는지 결정합니다. 효과는 새 리소스, 업데이트된 리소스 또는 기존 리소스인 경우 서로 다르게 동작합니다.

이러한 영향은 현재 정책 정의에서 지원 됩니다.

- [Append](#append)
- [감사](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [거부](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [사용 안 함](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (미리 보기)
- [EnforceRegoPolicy](#enforceregopolicy) (미리 보기)
- [수정](#modify)

## <a name="order-of-evaluation"></a>평가 순서

Azure Resource Manager를 통해 리소스를 만들거나 업데이트하도록 요구하는 요청은 먼저 Azure Policy에서 평가됩니다. Azure Policy 리소스에 적용 되는 모든 할당 목록을 만든 다음 각 정의에 대해 리소스를 평가 합니다. Azure Policy은 적절 한 리소스 공급자에 게 요청을 전달 하기 전에 몇 가지 효과를 처리 합니다. 이렇게 하면 리소스가 Azure Policy의 디자인 된 거 버 넌 스 컨트롤을 충족 하지 않는 경우 리소스 공급자에의 한 불필요 한 처리를 방지 합니다.

- **사용 안 함**을 먼저 선택하여 정책 규칙을 평가할지 여부를 확인합니다.
- 그런 다음 **추가** 및 **수정** 이 평가 됩니다. 에서 요청을 변경할 수 있기 때문에 변경 사항이 있으면 감사 또는 거부 효과가 발생 하지 않을 수 있습니다.
- 그런 다음, **거부**가 평가됩니다. 감사 전에 거부를 평가하여 원치 않는 리소스의 이중 로깅이 방지됩니다.
- 그런 다음, 리소스 공급 기업으로 가는 요청 전에 **감사**가 평가됩니다.

리소스 공급 기업이 성공 코드를 반환하면 **AuditIfNotExists** 및 **DeployIfNotExists**가 추가 규정 준수 로깅 또는 작업이 필요한지 확인하기 위해 평가합니다.

현재 **EnforceOPAConstraint** 또는 **EnforceRegoPolicy** 효과를 평가 하는 순서는 없습니다.

## <a name="disabled"></a>사용 안 함

이 효과는 테스트 상황 또는 정책 정의가 효과를 매개 변수화한 경우에 유용합니다. 이러한 유연성을 사용하면 해당 정책의 모든 할당을 사용하지 않도록 설정하는 대신 단일 할당을 사용하지 않도록 설정할 수 있습니다.

사용할 수 없는 효과에 대 한 대안은 정책 할당에 설정 된 **enforcementMode** 입니다.
**EnforcementMode** 를 _사용 하지 않도록 설정_하면 리소스는 계속 평가 됩니다. 활동 로그와 같은 로깅 및 정책 효과가 발생 하지 않습니다. 자세한 내용은 [정책 할당-적용 모드](./assignment-structure.md#enforcement-mode)를 참조 하세요.

## <a name="append"></a>추가

만들기 또는 업데이트 중에 요청된 리소스에 추가 필드를 추가하는 데 추가가 사용됩니다. 일반적인 예는 저장소 리소스에 대해 허용 되는 Ip를 지정 하는 것입니다.

> [!IMPORTANT]
> 추가는 비 태그 속성과 함께 사용 하기 위한 것입니다. 추가는 만들기 또는 업데이트 요청 중에 리소스에 태그를 추가할 수 있지만 대신 태그에 [수정](#modify) 효과를 사용 하는 것이 좋습니다.

### <a name="append-evaluation"></a>추가 평가

추가는 리소스의 생성 또는 업데이트하는 도중 리소스 공급 기업이 요청을 처리하기 전에 평가합니다. 추가는 정책 규칙의 **if** 조건이 충족되는 경우 리소스에 필드를 추가합니다. 추가 효과가 원래 요청의 값을 다른 값으로 재정의하는 경우 거부 효과로서 역할을 하고 해당 요청을 거부합니다. 새 값을 기존 배열에 추가하려면 **[\*]** 버전의 별칭을 사용합니다.

추가 효과를 사용하는 정책 정의가 평가 주기의 일부로 실행되는 경우 이미 존재하는 리소스를 변경하지 않습니다. 대신 비호환으로 **if** 조건을 충족하는 모든 리소스를 표시합니다.

### <a name="append-properties"></a>추가 속성

추가 효과는 필수인 **details** 배열만을 갖습니다. **details**는 배열이므로 단일 **필드/값** 쌍 또는 여러 개를 사용할 수 있습니다. 허용 가능한 필드 목록은 [정의 구조](definition-structure.md#fields)를 참조하세요.

### <a name="append-examples"></a>추가 예제

예 1: 저장소 계정에 IP 규칙을 설정 하기 위해 배열 **값** 이 포함 된 비 **[\*]** [별칭](definition-structure.md#aliases) 을 사용 하는 단일 **필드/값** 쌍입니다. **[\*]** 별칭이 아닌 별칭이 배열이면 **value**를 전체 배열로 추가합니다. 배열이 이미 있으면 충돌로 인해 거부 이벤트가 발생합니다.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

예 2: 저장소 계정에 IP 규칙을 설정 하기 위해 배열 **값** 이 있는 **[\*]** [별칭](definition-structure.md#aliases) 을 사용 하는 단일 **필드/값** 쌍입니다. **[\*]** 별칭을 사용하면 효과(effect)에서 **value**를 이전부터 잠재적으로 존재하는 배열에 추가합니다. 배열이 아직 없으면 생성 됩니다.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>수정

Modify는 만들거나 업데이트 하는 동안 리소스에 대 한 태그를 추가, 업데이트 또는 제거 하는 데 사용 됩니다. 일반적인 예는 costCenter와 같은 리소스에 대 한 태그를 업데이트 하는 것입니다. 대상 리소스가 리소스 그룹이 아닌 경우 수정 정책에는 항상 _인덱싱된_ 로 설정 된 `mode` 있어야 합니다. 기존 비준수 리소스는 [재구성 작업](../how-to/remediate-resources.md)을 통해 수정할 수 있습니다. 단일 수정 규칙에는 원하는 수의 작업이 있을 수 있습니다.

> [!IMPORTANT]
> Modify는 현재 태그에만 사용할 수 있습니다. 태그를 관리 하는 경우 추가 작업 형식 및 기존 리소스를 수정 하는 기능을 제공 하는 대신 수정을 사용 하는 것이 좋습니다. 그러나 관리 id를 만들 수 없는 경우에는 추가를 권장 합니다.

### <a name="modify-evaluation"></a>평가 수정

수정은 리소스를 만들거나 업데이트 하는 동안 리소스 공급자에서 요청을 처리 하기 전에 평가 됩니다. Modify 정책 규칙의 **if** 조건이 충족 될 때 리소스에 대 한 태그를 추가 하거나 업데이트 합니다.

수정 효과를 사용 하는 정책 정의가 평가 주기의 일부로 실행 되는 경우 이미 존재 하는 리소스를 변경 하지 않습니다. 대신 비호환으로 **if** 조건을 충족하는 모든 리소스를 표시합니다.

### <a name="modify-properties"></a>속성 수정

수정 효과의 **details** 속성에는 재구성에 필요한 사용 권한을 정의 하는 모든 하위 속성 및 태그 값을 추가, 업데이트 또는 제거 하는 데 사용 되는 **작업이** 포함 됩니다.

- **roleDefinitionIds** [required]
  - 이 속성은 구독에서 액세스할 수 있는 역할 기반 액세스 제어 역할 ID와 일치하는 문자열 배열을 포함해야 합니다. 자세한 내용은 [수정 - 정책 정의 구성](../how-to/remediate-resources.md#configure-policy-definition)을 참조하세요.
  - 정의 된 역할에는 [참가자](../../../role-based-access-control/built-in-roles.md#contributor) 역할에 부여 된 모든 작업이 포함 되어야 합니다.
- **작업** [필수]
  - 일치 하는 리소스에서 완료할 모든 태그 작업의 배열입니다.
  - 속성
    - **작업** [필수]
      - 일치 하는 리소스에 대해 수행할 작업을 정의 합니다. 옵션은 _Addorreplace_, _Add_, _Remove_입니다. _추가는 추가_ [효과와 유사](#append) 하 게 동작 합니다.
    - **필드** [필수]
      - 추가, 대체 또는 제거할 태그입니다. 태그 이름은 다른 [필드](./definition-structure.md#fields)에 대 한 동일한 명명 규칙을 따라야 합니다.
    - **값** (선택 사항)
      - 태그를 설정할 값입니다.
      - **작업이** _addorreplace_ 또는 _Add_인 경우이 속성은 필수입니다.

### <a name="modify-operations"></a>작업 수정

**작업** 속성 배열을 사용 하면 단일 정책 정의에서 여러 가지 방법으로 여러 태그를 변경할 수 있습니다. 각 작업은 **작업**, **필드**및 **값** 속성으로 구성 됩니다. 작업은 수정 작업이 태그에 수행할 작업을 결정 하 고, 필드는 변경 되는 태그를 결정 하며, 값은 해당 태그에 대 한 새 설정을 정의 합니다. 아래 예제에서는 다음과 같은 태그를 변경 합니다.

- 다른 값이 이미 있는 경우에도 `environment` 태그를 "Test"로 설정 합니다.
- `TempResource`태그를 제거 합니다.
- 정책 할당에 구성 된 정책 매개 변수 _DeptName_ `Dept` 태그를 설정 합니다.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

**Operation** 속성에는 다음과 같은 옵션이 있습니다.

|작업 |Description |
|-|-|
|addOrReplace |태그가 다른 값으로 이미 존재 하는 경우에도 정의 된 태그 및 값을 리소스에 추가 합니다. |
|추가 |리소스에 정의 된 태그 및 값을 추가 합니다. |
|제거 |리소스에서 정의 된 태그를 제거 합니다. |

### <a name="modify-examples"></a>예제 수정

예제 1: `environment` 태그를 추가 하 고 기존 `environment` 태그를 "Test"로 바꿉니다.

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

예 2: `env` 태그를 제거 하 고 `environment` 태그를 추가 하거나 기존 `environment` 태그를 매개 변수가 있는 값으로 바꿉니다.

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>거부

거부는 정책 정의를 통해 정의된 표준과 일치하지 않고 요청에 실패하는 리소스 요청을 방지하는 데 사용됩니다.

### <a name="deny-evaluation"></a>거부 평가

일치된 리소스를 만들거나 업데이트할 때 거부는 리소스 공급 기업에 전송되기 전에 먼저 요청을 방지합니다. 요청은 `403 (Forbidden)`으로 반환됩니다. 포털에서 금지됨은 정책 할당에서 차단된 배포에 대한 상태로 확인할 수 있습니다.

기존 리소스의 평가 중 거부 정책 정의와 일치하는 리소스는 비준수로 표시됩니다.

### <a name="deny-properties"></a>거부 속성

거부 효과에는 정책 정의의 **then** 조건에 사용하기 위한 추가 속성이 없습니다.

### <a name="deny-example"></a>거부 예제

예제: 거부 효과 사용

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>감사

감사는 비준수 리소스를 평가하는 경우 활동 로그에서 경고 이벤트를 만드는 데 사용되지만 요청을 중단하지는 않습니다.

### <a name="audit-evaluation"></a>감사 평가

Audit는 리소스를 만들거나 업데이트 하는 동안 Azure Policy에서 확인 한 마지막 효과입니다. 그런 다음 리소스를 리소스 공급자에 게 보냅니다. Azure Policy 감사는 리소스 요청 및 평가 주기와 동일하게 작동합니다. Azure Policy는 활동 로그에 `Microsoft.Authorization/policies/audit/action` 작업을 추가 하 고 리소스를 비규격으로 표시 합니다.

### <a name="audit-properties"></a>감사 속성

감사 효과에는 정책 정의의 **then** 조건에 사용하기 위한 추가 속성이 없습니다.

### <a name="audit-example"></a>감사 예제

예제: 감사 효과 사용

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists는 **if** 조건과 일치하는 리소스에서 감사를 활성화하지만 **then** 조건의 **details**에서 지정된 구성 요소를 포함하지 않습니다.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 평가

리소스 공급 기업이 리소스 만들기 또는 업데이트 요청을 처리하고 성공 상태 코드를 반환한 후 AuditIfNotExists가 실행됩니다. 관련된 리소스가 없거나 **ExistenceCondition**에서 정의된 리소스가 true로 평가되지 않는 경우 감사가 발생합니다. Azure Policy는 감사 효과와 동일한 방식으로 활동 로그에 `Microsoft.Authorization/policies/audit/action` 작업을 추가 합니다. 트리거되는 경우 **if** 조건을 충족하는 리소스는 비호환으로 표시되는 리소스입니다.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 속성

AuditIfNotExists 효과의 **details** 속성에는 일치하는 관련된 리소스를 정의하는 모든 하위 속성이 있습니다.

- **Type**[필수]
  - 일치하는 관련된 리소스의 형식을 지정합니다.
  - **Details. type** 이 if condition 리소스 아래의 리소스 유형인 **경우** 정책은 평가 된 리소스의 범위 내에서이 **형식의** 리소스를 쿼리 합니다. 그렇지 않으면 평가 된 리소스와 동일한 리소스 그룹 내에서 정책 쿼리가 실행 됩니다.
- **Name**(옵션)
  - 일치하는 리소스의 정확한 이름을 지정하고 정책에서 지정된 형식의 모든 리소스 대신 하나의 특정 리소스를 인출하도록 합니다.
  - **If. field. 형식** 및 **. details. 형식** 에 대 한 조건 값이 일치 하는 경우에는 **이름이** _필요_ 하며 `[field('name')]`이어야 합니다. 그러나 [감사](#audit) 효과를 대신 고려해 야 합니다.
- **ResourceGroupName**(옵션)
  - 다른 리소스 그룹에서 오는 관련된 리소스의 일치를 허용합니다.
  - **type**이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용되지 않습니다.
  - 기본값은 리소스의 리소스 그룹인 **if** 조건입니다.
- **ExistenceScope**(옵션)
  - 허용되는 값은 _Subscription_ 및 _ResourceGroup_입니다.
  - 일치하는 관련된 리소스를 인출할 범위를 설정합니다.
  - **type**이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용되지 않습니다.
  - _ResourceGroup_의 경우 **ResourceGroupName**에서 지정된 **if** 조건 리소스의 리소스 그룹 또는 리소스 그룹으로 제한합니다.
  - _Subscription_의 경우 관련된 리소스에 대한 전체 구독을 쿼리합니다.
  - 기본값은 _ResourceGroup_입니다.
- **ExistenceCondition**(옵션)
  - 지정되지 않은 경우 **type**의 모든 관련 리소스는 효과를 충족하고 감사를 트리거하지 않습니다.
  - **if** 조건에 대해 정책 규칙과 동일한 언어를 사용하지만 관련된 각 리소스에 대해 개별적으로 평가됩니다.
  - 일치하는 관련 리소스가 true로 평가되는 경우 효과가 충족되고 감사를 트리거하지 않습니다.
  - [field()]를 사용하여 **if** 조건의 값과 같은지 확인할 수 있습니다.
  - 예를 들어 부모 리소스(**if** 조건에서)가 일치하는 관련된 리소스와 동일한 리소스 위치에 있는지 확인하는 데 사용될 수 있습니다.

### <a name="auditifnotexists-example"></a>AuditIfNotExists 예제

예제: 맬웨어 방지 확장이 있는지 확인하도록 Virtual Machines를 평가한 다음, 누락되는 경우 감사합니다.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

AuditIfNotExists와 마찬가지로, DeployIfNotExists 정책 정의는 조건이 충족 될 때 템플릿 배포를 실행 합니다.

> [!NOTE]
> **deployIfNotExists**에서는 [중첩 템플릿](../../../azure-resource-manager/templates/linked-templates.md#nested-template)은 지원되지만 [연결된 템플릿](../../../azure-resource-manager/templates/linked-templates.md#linked-template)은 현재 지원되지 않습니다.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 평가

리소스 공급 기업이 리소스 만들기 또는 업데이트 요청을 처리하고 성공 상태 코드를 반환한 후 DeployIfNotExists가 실행됩니다. 관련 리소스가 없거나 **ExistenceCondition**에서 정의된 리소스가 true로 평가되지 않는 경우 템플릿 배포가 발생합니다.

평가 주기 중 리소스와 일치하는 DeployIfNotExists 효과가 포함된 정책 정의는 비준수로 표시되지만 해당 리소스에서 작업이 수행되지 않습니다.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 속성

DeployIfNotExists 효과의 **details** 속성에는 일치 시킬 관련 리소스를 정의 하는 모든 하위 속성 및 실행할 템플릿 배포가 있습니다.

- **Type**[필수]
  - 일치하는 관련된 리소스의 형식을 지정합니다.
  - **if** 조건 리소스 아래의 리소스를 인출하려는 시도로 시작한 다음, **if** 리소스 조건와 동일한 리소스 그룹 내에서 쿼리합니다.
- **Name**(옵션)
  - 일치하는 리소스의 정확한 이름을 지정하고 정책에서 지정된 형식의 모든 리소스 대신 하나의 특정 리소스를 인출하도록 합니다.
  - **If. field. 형식** 및 **. details. 형식** 에 대 한 조건 값이 일치 하는 경우에는 **이름이** _필요_ 하며 `[field('name')]`이어야 합니다.
- **ResourceGroupName**(옵션)
  - 다른 리소스 그룹에서 오는 관련된 리소스의 일치를 허용합니다.
  - **type**이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용되지 않습니다.
  - 기본값은 리소스의 리소스 그룹인 **if** 조건입니다.
  - 템플릿 배포가 실행되는 경우 이 값의 리소스 그룹에 배포됩니다.
- **ExistenceScope**(옵션)
  - 허용되는 값은 _Subscription_ 및 _ResourceGroup_입니다.
  - 일치하는 관련된 리소스를 인출할 범위를 설정합니다.
  - **type**이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용되지 않습니다.
  - _ResourceGroup_의 경우 **ResourceGroupName**에서 지정된 **if** 조건 리소스의 리소스 그룹 또는 리소스 그룹으로 제한합니다.
  - _Subscription_의 경우 관련된 리소스에 대한 전체 구독을 쿼리합니다.
  - 기본값은 _ResourceGroup_입니다.
- **ExistenceCondition**(옵션)
  - 지정되지 않은 경우 **type**의 모든 관련 리소스는 효과를 충족하고 배포를 트리거하지 않습니다.
  - **if** 조건에 대해 정책 규칙과 동일한 언어를 사용하지만 관련된 각 리소스에 대해 개별적으로 평가됩니다.
  - 일치하는 모든 관련 리소스가 true로 평가되는 경우 효과가 충족되고 배포를 트리거하지 않습니다.
  - [field()]를 사용하여 **if** 조건의 값과 같은지 확인할 수 있습니다.
  - 예를 들어 부모 리소스(**if** 조건에서)가 일치하는 관련된 리소스와 동일한 리소스 위치에 있는지 확인하는 데 사용될 수 있습니다.
- **roleDefinitionIds** [required]
  - 이 속성은 구독에서 액세스할 수 있는 역할 기반 액세스 제어 역할 ID와 일치하는 문자열 배열을 포함해야 합니다. 자세한 내용은 [수정 - 정책 정의 구성](../how-to/remediate-resources.md#configure-policy-definition)을 참조하세요.
- **DeploymentScope**(선택 사항)
  - 허용되는 값은 _Subscription_ 및 _ResourceGroup_입니다.
  - 트리거될 배포 유형을 설정합니다. _Subscription_은 [구독 수준의 배포](../../../azure-resource-manager/deploy-to-subscription.md)를 나타내며, _ResourceGroup_은 리소스 그룹에 대한 배포를 나타냅니다.
  - 구독 수준 배포를 사용하는 경우 _location_ 속성을 _Deployment_에 지정해야 합니다.
  - 기본값은 _ResourceGroup_입니다.
- **Deployment**[필수]
  - 이 속성은 `Microsoft.Resources/deployments` PUT API로 전달되므로 전체 템플릿 배포를 포함해야 합니다. 자세한 내용은 [배포 REST API](/rest/api/resources/deployments)를 참조하세요.

  > [!NOTE]
  > **배포** 속성 내부의 모든 함수는 정책이 아닌 템플릿의 구성 요소로 평가됩니다. 예외는 정책에서 템플릿으로 값을 전달하는 **parameters** 속성입니다. 템플릿 매개 변수 이름 아래에 있는 이 섹션의 **값**은 이 값의 전달을 수행하는 데 사용됩니다(DeployIfNotExists 예제의 _fullDbName_ 참조).

### <a name="deployifnotexists-example"></a>DeployIfNotExists 예제

예제: transparentDataEncryption이 활성화되었는지 확인하도록 SQL Server 데이터베이스를 평가합니다. 그렇지 않은 경우 활성화하는 배포가 실행됩니다.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

이 효과는 `Microsoft.Kubernetes.Data`의 정책 정의 *모드* 와 함께 사용 됩니다. Azure에서 자체 관리 되는 Kubernetes 클러스터에 대해 opa ( [정책 에이전트](https://www.openpolicyagent.org/) )를 열기 위해 [Opa 제약 조건 프레임 워크](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) 를 사용 하 여 정의한 게이트 키퍼 v3 허용 제어 규칙을 전달 하는 데 사용 됩니다.

> [!NOTE]
> [AKS 엔진에 대 한 Azure Policy](aks-engine.md) 는 공개 미리 보기로 제공 되며 기본 제공 정책 정의만 지원 합니다.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint 평가

개방 된 정책 에이전트 허용 컨트롤러는 클러스터의 모든 새 요청을 실시간으로 평가 합니다.
5 분 마다 클러스터의 전체 검색이 완료 되 고 결과가 Azure Policy 보고 됩니다.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint 속성

EnforceOPAConstraint 효과의 **details** 속성에는 게이트 키퍼 v3 허용 제어 규칙을 설명 하는 하위 속성이 있습니다.

- **constraintTemplate** [필수]
  - 새 제약 조건을 정의 하는 CRD (제약 조건 템플릿 CustomResourceDefinition)입니다. 템플릿은 Azure Policy의 **값** 을 통해 전달 되는 rego 논리, 제약 조건 스키마 및 제약 조건 매개 변수를 정의 합니다.
- **제약 조건** [필수]
  - 제약 조건 템플릿의 CRD 구현입니다. **값** 을 통해 전달 되는 매개 변수를 `{{ .Values.<valuename> }}`으로 사용 합니다. 아래 예제에서는 `{{ .Values.cpuLimit }}` 하 고 `{{ .Values.memoryLimit }}`합니다.
- **값** [선택 사항]
  - 제약 조건에 전달할 매개 변수 및 값을 정의 합니다. 각 값은 제약 조건 템플릿 CRD에 있어야 합니다.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy 예제

예: AKS 엔진에서 컨테이너 CPU 및 메모리 리소스 제한을 설정 하는 게이트 키퍼 v3 허용 제어 규칙

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

이 효과는 `Microsoft.ContainerService.Data`의 정책 정의 *모드* 와 함께 사용 됩니다. [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) 로 정의 된 게이트 키퍼 v2 허용 제어 규칙을 전달 하 여 [Azure Kubernetes Service](../../../aks/intro-kubernetes.md)에서 [정책 에이전트](https://www.openpolicyagent.org/) (opa)를 여는 데 사용 됩니다.

> [!NOTE]
> [AKS에 대 한 Azure Policy](rego-for-aks.md) 는 제한 된 미리 보기로 제공 되며 기본 제공 정책 정의만 지원 합니다.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy 평가

개방 된 정책 에이전트 허용 컨트롤러는 클러스터의 모든 새 요청을 실시간으로 평가 합니다.
5 분 마다 클러스터의 전체 검색이 완료 되 고 결과가 Azure Policy 보고 됩니다.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy 속성

EnforceRegoPolicy 효과의 **details** 속성에는 게이트 키퍼 허용 제어 규칙을 설명 하는 하위 속성이 있습니다.

- **policyId** [required]
  - Rego 허용 제어 규칙에 매개 변수로 전달 되는 고유 이름입니다.
- **정책** [필수]
  - Rego 허용 제어 규칙의 URI를 지정 합니다.
- **policyParameters** [선택 사항]
  - Rego 정책에 전달할 매개 변수 및 값을 정의 합니다.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy 예제

예: AKS에서 지정 된 컨테이너 이미지만 허용 하는 게이트 키퍼 허용 제어 규칙

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>레이어링 정책

리소스는 여러 할당에서 영향을 받을 수 있습니다. 이러한 할당은 동일한 범위 또는 서로 다른 범위에 있을 수 있습니다. 이러한 각 할당은 정의된 다른 효과를 가질 수 있습니다. 각 정책에 대한 조건 및 효과는 독립적으로 평가됩니다. 예:

- 정책 1
  - 리소스 위치를 'westus'로 제한
  - 구독 A에 할당됨
  - 거부 효과
- 정책 2
  - 리소스 위치를 'eastus'로 제한
  - 구독 A의 리소스 그룹 B에 할당됨
  - 감사 효과
  
이 설정은 다음 결과로 나타납니다.

- 'eastus'에 이미 있는 리소스 그룹 B의 모든 리소스는 정책 2를 준수하지만 정책 1을 준수하지 않음
- 'eastus'에 있지 않은 리소스 그룹 B의 모든 리소스는 정책 2를 준수하지 않고 'westus'에 있지 않은 경우 정책 1도 준수하지 않음
- 'westus'에 있지 않은 구독 A의 모든 새 리소스는 정책 1에서 거부됨
- 'westus'에 있는 구독 A 및 리소스 그룹 B의 모든 새 리소스는 정책 2에서 생성되고 정책 2를 준수하지 않음

정책 1 및 정책 2 모두에 거부의 효과가 있는 경우 상황은 다음으로 변경됩니다.

- 'eastus'에 있지 않은 리소스 그룹 B의 모든 리소스는 정책 2를 준수하지 않음
- 'westus'에 있지 않은 리소스 그룹 B의 모든 리소스는 정책 1을 준수하지 않음
- 'westus'에 있지 않은 구독 A의 모든 새 리소스는 정책 1에서 거부됨
- 구독 A의 리소스 그룹 B에 있는 모든 새 리소스는 거부됨

각 할당은 개별적으로 평가됩니다. 따라서 리소스가 범위의 차이로 인한 간격을 통해 떨어질 기회는 없습니다. 레이어링 정책 또는 정책 겹침의 최종 결과는 **누적된 가장 제한적**인 것으로 간주됩니다. 예를 들어 정책 1 및 정책 2 모두에 거부 효과가 있으면 리소스는 겹치고 충돌하는 정책에 의해 차단됩니다. 리소스를 여전히 대상 범위에서 만들어야 하는 경우 각 할당에 대한 제외를 검토하여 적절한 정책이 적절한 범위에 적용되고 있는지 유효성을 검사합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토 합니다.
- [Azure Policy 정의 구조](definition-structure.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스](../how-to/remediate-resources.md)를 수정 하는 방법에 대해 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
