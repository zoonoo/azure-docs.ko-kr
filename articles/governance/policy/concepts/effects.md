---
title: 효과 작동 방식 이해
description: Azure Policy 정의는 준수가 관리되고 보고되는 방법을 결정하는 다양한 효과가 있습니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 67a195932ad1afc3c93a94dfcbda8ab8a47760b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60498818"
---
# <a name="understand-azure-policy-effects"></a>Azure Policy의 영향 파악

Azure Policy의 각 정책 정의는 단일 효과가 있습니다. 해당 효과는 정책 규칙이 일치되는 것으로 평가될 때 어떻게 되는지 결정합니다. 효과는 새 리소스, 업데이트된 리소스 또는 기존 리소스인 경우 서로 다르게 동작합니다.

현재 정책 정의에서 지원되는 6가지 효과가 있습니다.

- 추가
- 감사
- AuditIfNotExists
- 거부
- DeployIfNotExists
- 사용 안 함

## <a name="order-of-evaluation"></a>평가 순서

Azure Resource Manager를 통해 리소스를 만들거나 업데이트하는 요청은 먼저 Policy에서 평가됩니다. Policy는 리소스에 적용한 다음, 각 정의에 대해 리소스를 평가하는 모든 할당 목록을 만듭니다. Policy는 적절한 리소스 공급 기업에 요청을 전달하기 전에 다양한 효과를 처리합니다. 이렇게 하면 리소스가 Policy의 디자인된 거버넌스 컨트롤을 충족하지 않을 때 리소스 공급 기업의 불필요한 처리를 방지합니다.

- **사용 안 함**을 먼저 선택하여 정책 규칙을 평가할지 여부를 확인합니다.
- 그런 다음, **추가**를 평가합니다. 추가는 요청을 변경할 수 있으므로 추가에서 만들어진 변경은 감사를 방지하거나 트리거되는 효과를 거부할 수 있습니다.
- 그런 다음, **거부**가 평가됩니다. 감사 전에 거부를 평가하여 원치 않는 리소스의 이중 로깅이 방지됩니다.
- 그런 다음, 리소스 공급 기업으로 가는 요청 전에 **감사**가 평가됩니다.

리소스 공급 기업이 성공 코드를 반환하면 **AuditIfNotExists** 및 **DeployIfNotExists**가 추가 규정 준수 로깅 또는 작업이 필요한지 확인하기 위해 평가합니다.

## <a name="disabled"></a>사용 안 함

이 효과는 테스트 상황 또는 정책 정의가 효과를 매개 변수화한 경우에 유용합니다. 이러한 유연성을 사용하면 해당 정책의 모든 할당을 사용하지 않도록 설정하는 대신 단일 할당을 사용하지 않도록 설정할 수 있습니다.

## <a name="append"></a>추가

만들기 또는 업데이트 중에 요청된 리소스에 추가 필드를 추가하는 데 추가가 사용됩니다. 일반적인 예로 costCenter와 같은 리소스에 태그를 추가하거나 스토리지 리소스에 대해 허용된 IP를 지정합니다.

### <a name="append-evaluation"></a>추가 평가

추가는 리소스의 생성 또는 업데이트하는 도중 리소스 공급 기업이 요청을 처리하기 전에 평가합니다. 추가는 정책 규칙의 **if** 조건이 충족되는 경우 리소스에 필드를 추가합니다. 추가 효과가 원래 요청의 값을 다른 값으로 재정의하는 경우 거부 효과로서 역할을 하고 해당 요청을 거부합니다. 새 값을 기존 배열에 추가하려면 **[\*]** 버전의 별칭을 사용합니다.

추가 효과를 사용하는 정책 정의가 평가 주기의 일부로 실행되는 경우 이미 존재하는 리소스를 변경하지 않습니다. 대신 비호환으로 **if** 조건을 충족하는 모든 리소스를 표시합니다.

### <a name="append-properties"></a>추가 속성

추가 효과는 필수인 **details** 배열만을 갖습니다. **details**는 배열이므로 단일 **필드/값** 쌍 또는 여러 개를 사용할 수 있습니다. 허용 가능한 필드 목록은 [정의 구조](definition-structure.md#fields)를 참조하세요.

### <a name="append-examples"></a>추가 예제

예제 1: 태그를 추가하는 단일 **필드/값** 쌍입니다.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

예 2: 태그 세트를 추가하는 여러 **필드/값** 쌍입니다.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

예 3: 스토리지 계정에 IP 규칙을 설정하는 **value** 배열이 포함된 **[\*]**
[별칭](definition-structure.md#aliases)이 아닌 별칭을 사용하는 단일 **field/value** 쌍입니다. **[\*]** 별칭이 아닌 별칭이 배열이면 **value**를 전체 배열로 추가합니다. 배열이 이미 있으면 충돌로 인해 거부 이벤트가 발생합니다.

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

예제 4: 스토리지 계정에 IP 규칙을 설정하는 **value** 배열이 포함된 **[\*]** [별칭](definition-structure.md#aliases)을 사용하는 단일 **field/value** 쌍입니다. **[\*]** 별칭을 사용하면 효과(effect)에서 **value**를 이전부터 잠재적으로 존재하는 배열에 추가합니다. 배열이 아직 없으면 만들어집니다.

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

## <a name="deny"></a>거부

거부는 정책 정의를 통해 정의된 표준과 일치하지 않고 요청에 실패하는 리소스 요청을 방지하는 데 사용됩니다.

### <a name="deny-evaluation"></a>거부 평가

일치된 리소스를 만들거나 업데이트할 때 거부는 리소스 공급 기업에 전송되기 전에 먼저 요청을 방지합니다. 요청은 `403 (Forbidden)`으로 반환됩니다. 포털에서 금지됨은 정책 할당에서 차단된 배포에 대한 상태로 확인할 수 있습니다.

기존 리소스의 평가 중 거부 정책 정의와 일치하는 리소스는 비준수로 표시됩니다.

### <a name="deny-properties"></a>거부 속성

거부 효과에는 정책 정의의 **then** 조건에 사용하기 위한 추가 속성이 없습니다.

### <a name="deny-example"></a>거부 예제

예제: 거부 효과 사용.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>감사

감사는 비준수 리소스를 평가하는 경우 활동 로그에서 경고 이벤트를 만드는 데 사용되지만 요청을 중단하지는 않습니다.

### <a name="audit-evaluation"></a>감사 평가

감사는 리소스의 만들기 또는 업데이트하는 중에 Policy에서 확인된 마지막 효과입니다. 그런 다음, Policy에서는 리소스 공급 기업에 리소스를 전송합니다. 감사는 리소스 요청 및 평가 주기와 동일하게 작동합니다. Policy는 `Microsoft.Authorization/policies/audit/action` 작업을 활동 로그에 추가하고 리소스를 비준수로 표시합니다.

### <a name="audit-properties"></a>감사 속성

감사 효과에는 정책 정의의 **then** 조건에 사용하기 위한 추가 속성이 없습니다.

### <a name="audit-example"></a>감사 예제

예제: 감사 효과 사용.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists는 **if** 조건과 일치하는 리소스에서 감사를 활성화하지만 **then** 조건의 **details**에서 지정된 구성 요소를 포함하지 않습니다.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 평가

리소스 공급 기업이 리소스 만들기 또는 업데이트 요청을 처리하고 성공 상태 코드를 반환한 후 AuditIfNotExists가 실행됩니다. 관련된 리소스가 없거나 **ExistenceCondition**에서 정의된 리소스가 true로 평가되지 않는 경우 감사가 발생합니다. Policy에서는 감사 효과와 동일한 방식으로 `Microsoft.Authorization/policies/audit/action` 작업을 활동 로그에 추가합니다. 트리거되는 경우 **if** 조건을 충족하는 리소스는 비호환으로 표시되는 리소스입니다.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 속성

AuditIfNotExists 효과의 **details** 속성에는 일치하는 관련된 리소스를 정의하는 모든 하위 속성이 있습니다.

- **Type**[필수]
  - 일치하는 관련된 리소스의 형식을 지정합니다.
  - 하는 경우 **details.type** 아래에 있는 리소스 종류는 **경우** 조건 리소스의 리소스에 대 한 정책 쿼리 **형식** 계산된 리소스의 범위 내에서. 계산된 리소스와 동일한 리소스 그룹 내에서 고, 그렇지 정책 쿼리 합니다.
- **Name**(옵션)
  - 일치하는 리소스의 정확한 이름을 지정하고 정책에서 지정된 형식의 모든 리소스 대신 하나의 특정 리소스를 인출하도록 합니다.
  - 조건에 대 한 값 하는 경우 **if.field.type** 하 고 **then.details.type** 다음 일치 **이름** 됩니다 _필요_ 여야합니다`[field('name')]`. 그러나는 [감사](#audit) 효과 대신 고려해 야 합니다.
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

AuditIfNotExists와 마찬가지로 DeployIfNotExists는 조건이 충족하는 경우 템플릿 배포를 실행합니다.

> [!NOTE]
> **deployIfNotExists**에서는 [중첩 템플릿](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template)은 지원되지만 [연결된 템플릿](../../../azure-resource-manager/resource-group-linked-templates.md)은 현재 지원되지 않습니다.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 평가

리소스 공급 기업이 리소스 만들기 또는 업데이트 요청을 처리하고 성공 상태 코드를 반환한 후 DeployIfNotExists가 실행됩니다. 관련 리소스가 없거나 **ExistenceCondition**에서 정의된 리소스가 true로 평가되지 않는 경우 템플릿 배포가 발생합니다.

평가 주기 중 리소스와 일치하는 DeployIfNotExists 효과가 포함된 정책 정의는 비준수로 표시되지만 해당 리소스에서 작업이 수행되지 않습니다.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 속성

DeployIfNotExists 효과의 **details** 속성에는 일치하는 관련된 리소스를 정의하는 모든 하위 속성 및 실행할 템플릿 배포가 있습니다.

- **Type**[필수]
  - 일치하는 관련된 리소스의 형식을 지정합니다.
  - **if** 조건 리소스 아래의 리소스를 인출하려는 시도로 시작한 다음, **if** 리소스 조건와 동일한 리소스 그룹 내에서 쿼리합니다.
- **Name**(옵션)
  - 일치하는 리소스의 정확한 이름을 지정하고 정책에서 지정된 형식의 모든 리소스 대신 하나의 특정 리소스를 인출하도록 합니다.
  - 조건에 대 한 값 하는 경우 **if.field.type** 하 고 **then.details.type** 다음 일치 **이름** 됩니다 _필요_ 여야합니다`[field('name')]`.
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

예제: transparentDataEncryption이 활성화되었는지 확인하기 위해 SQL Server 데이터베이스를 평가합니다. 그렇지 않은 경우 활성화하는 배포가 실행됩니다.

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
            "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
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

## <a name="layering-policies"></a>레이어링 정책

리소스는 여러 할당에서 영향을 받을 수 있습니다. 이러한 할당은 동일한 범위 또는 서로 다른 범위에 있을 수 있습니다. 이러한 각 할당은 정의된 다른 효과를 가질 수 있습니다. 각 정책에 대한 조건 및 효과는 독립적으로 평가됩니다. 예를 들면 다음과 같습니다.

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

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Policy 정의 구조](definition-structure.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](../how-to/programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는 방법](../how-to/getting-compliance-data.md)을 알아봅니다.
- [비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
