---
title: Azure Policy 효과 이해
description: Azure Policy 정의는 준수가 관리되고 보고되는 방법을 결정하는 다양한 효과가 있습니다.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a3267d8f2f088c93a8f69f949dc928437a80f455
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856949"
---
# <a name="understanding-policy-effects"></a>Policy 효과 이해

Azure Policy에서 각 정책 정의는 정책 규칙의 **if** 세그먼트가 검사되는 리소스와 일치하도록 평가되는 경우 검사 중 발생하는 작업을 결정하는 단일 효과를 갖습니다. 효과는 새 리소스, 업데이트된 리소스 또는 기존 리소스인 경우 서로 다르게 동작할 수도 있습니다.

현재 정책 정의에서 지원되는 5개의 효과가 있습니다.

- 추가
- 감사
- AuditIfNotExists
- 거부
- DeployIfNotExists

## <a name="order-of-evaluation"></a>평가 순서

Azure Resource Manager를 통해 리소스를 만들거나 업데이트하는 요청이 실행되는 경우 Policy는 적절한 리소스 공급자로 요청을 처리하기 전에 여러 효과를 처리합니다.
이렇게 하면 리소스가 Policy의 디자인된 거버넌스 컨트롤을 충족하지 않을 때 리소스 공급자의 불필요한 처리를 방지합니다. Policy는 범위별로(제외 빼기) 리소스에 적용되는 정책 또는 이니셔티브 할당에 의해 할당된 모든 정책 정의의 목록을 만들고 각 정의에 대해 리소스를 평하도록 준비합니다.

- **추가**가 먼저 평가됩니다. 추가는 요청을 변경할 수 있으므로 추가에서 만들어진 변경은 감사를 방지하거나 트리거되는 효과를 거부할 수 있습니다.
- 그런 다음, **거부**가 평가됩니다. 감사 전에 거부를 평가하여 원치 않는 리소스의 이중 로깅이 방지됩니다.
- 그런 다음, 리소스 공급자로 가는 요청 전에 **감사**가 평가됩니다.

요청이 리소스 공급자에게 제공되고 리소스 공급자가 성공 상태 코드를 반환하면 추가 작업 규정 준수 로깅 또는 작업이 필요한지 확인하는 **AuditIfNotExists** 및 **DeployIfNotExists**가 평가됩니다.

## <a name="append"></a>추가

만들기 또는 업데이트 중에 요청된 리소스에 추가 필드를 추가하는 데 추가가 사용됩니다. costCenter와 같은 리소스에 태그를 추가하거나 저장소 리소스에 대한 허용된 IP를 지정하는 데 유용할 수 있습니다.

### <a name="append-evaluation"></a>추가 평가

언급한 바와 같이 추가는 리소스의 생성 또는 업데이트 중 리소스 공급자에 의해 처리되는 요청 전에 평가합니다. 추가는 정책 규칙의 **if** 조건이 충족되는 경우에 리소스에 필드를 추가합니다. 추가 효과가 원래 요청의 값을 다른 값으로 재정의하는 경우 거부 효과의 역할을 하고 요청을 거부합니다.

추가 효과를 사용하는 정책 정의가 평가 주기의 일부로 실행되는 경우 이미 존재하는 리소스를 변경하지 않습니다. 대신 비호환으로 **if** 조건을 충족하는 모든 리소스를 표시합니다.

### <a name="append-properties"></a>추가 속성

추가 효과는 필수인 **details** 배열만을 갖습니다. **details**는 배열이므로 단일 **필드/값** 쌍 또는 여러 개를 사용할 수 있습니다. 허용 가능한 필드 목록은 [정책 정의](policy-definition.md#fields)를 참조하세요.

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

예제 2: 태그 집합을 추가하는 여러 **필드/값** 쌍입니다.

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

예제 3: 저장소 계정에 IP 규칙을 설정하는 배열 **값**이 있는 [별칭](policy-definition.md#aliases)을 사용하는 단일 **필드/값** 쌍입니다.

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

## <a name="deny"></a>거부

정책 정의를 통해 원하는 표준과 일치하지 않고 요청에 실패하는 리소스 요청을 방지하는 데 거부가 사용됩니다.

### <a name="deny-evaluation"></a>거부 평가

리소스를 만들거나 업데이트할 때 거부는 리소스 공급자에 전송되기 전에 요청을 방지합니다. 요청은 403(금지됨)으로 반환됩니다. 포털에서 금지됨은 정책 할당으로 인해 차단된 배포에 대한 상태로 볼 수 있습니다.

평가 주기 중 리소스와 일치하는 거부 효과가 있는 정책 정의는 비호환으로 표시되지만 해당 리소스에서 작업이 수행되지 않습니다.

### <a name="deny-properties"></a>거부 속성

거부 효과에는 정책 정의의 **then** 조건에서 사용하기 위한 추가 속성이 없습니다.

### <a name="deny-example"></a>거부 예제

예제: 거부 효과 사용

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>감사

비호환 리소스가 평가되는 경우 활동 로그에서 경고 이벤트를 만드는 데 감사 효과가 사용되지만 요청을 중단하지 않습니다.

### <a name="audit-evaluation"></a>감사 평가

감사 효과는 리소스가 리소스 공급자에게 전송되기 전에 리소스의 생성 또는 업데이트 중 실행되는 마지막입니다. 감사는 리소스 요청 및 평가 주기에 동일하게 작동하고, 활동 로그에 대한 `Microsoft.Authorization/policies/audit/action` 작업을 실행합니다. 두 경우 모두에서 리소스는 비호환으로 표시됩니다.

### <a name="audit-properties"></a>감사 속성

감사 효과에는 정책 정의의 **then** 조건에서 사용하기 위한 추가 속성이 없습니다.

### <a name="audit-example"></a>감사 예제

예제: 감사 효과 사용

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists는 **then** 조건과 일치하는 리소스에서 감사를 활성화하지만 **then** 조건의 **details**에 정의된 구성 요소를 포함하지 않습니다.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 평가

AuditIfNotExists는 리소스 공급자가 리소스에 대한 만들기 또는 업데이트 요청을 처리하고 성공 상태 코드를 반환한 후 실행됩니다. 관련된 리소스가 없거나 **ExistenceCondition**에서 정의된 리소스가 true로 평가하지 않는 경우 효과가 트리거됩니다. 효과가 트리거되는 경우 활동 로그에 대한 `Microsoft.Authorization/policies/audit/action` 작업이 감사 효과와 동일한 방식으로 실행됩니다. 트리거되는 경우 **if** 조건을 충족하는 리소스는 비호환으로 표시되는 리소스입니다.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 속성

AuditIfNotExists 효과의 **details** 속성에는 일치하는 관련된 리소스를 정의하는 모든 하위 속성이 있습니다.

- **Type**[필수]
  - 일치하는 관련된 리소스의 형식을 지정합니다.
  - **if** 조건 리소스 아래의 리소스를 인출하려는 시도로 시작한 다음, **if** 리소스 조건와 동일한 리소스 그룹 내에서 쿼리합니다.
- **Name**(옵션)
  - 일치하는 리소스의 정확한 이름을 지정하고 정책에서 지정된 형식의 모든 리소스 대신 하나의 특정 리소스를 인출하도록 합니다.
- **ResourceGroupName**(옵션)
  - 다른 리소스 그룹에서 오는 관련된 리소스의 일치를 허용합니다.
  - **type**이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용하지 않습니다.
  - 기본값은 리소스의 리소스 그룹인 **if** 조건입니다.
- **ExistenceScope**(옵션)
  - 허용되는 값은 _Subscription_ 및 _ResourceGroup_입니다.
  - 일치하는 관련된 리소스를 인출할 범위를 설정합니다.
  - **type**이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용하지 않습니다.
  - _ResourceGroup_의 경우 **ResourceGroupName**에서 지정된 **if** 조건 리소스의 리소스 그룹 또는 리소스 그룹으로 제한합니다.
  - _Subscription_의 경우 관련된 리소스에 대한 전체 구독을 쿼리합니다.
  - 기본값은 _ResourceGroup_입니다.
- **ExistenceCondition**(옵션)
  - 지정되지 않은 경우 **type**의 관련된 리소스는 효과를 충족하고 감사를 트리거하지 않습니다.
  - **if** 조건에 대해 정책 규칙과 동일한 언어를 사용하지만 관련된 각 리소스에 대해 개별적으로 평가됩니다.
  - 일치하는 관련된 리소스가 true로 평가하는 경우 효과가 만족되고 감사를 트리거하지 않습니다.
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

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 평가

DeployIfNotExists는 리소스 공급자가 리소스에 대한 만들기 또는 업데이트 요청을 처리하고 성공 상태 코드를 반환한 후 실행됩니다. 관련된 리소스가 없거나 **ExistenceCondition**에서 정의된 리소스가 true로 평가하지 않는 경우 효과가 트리거됩니다. 효과가 트리거되면 템플릿 배포가 실행됩니다.

평가 주기 중 리소스와 일치하는 DeployIfNotExists 효과가 있는 정책 정의는 비호환으로 표시되지만 해당 리소스에서 작업이 수행되지 않습니다.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 속성

DeployIfNotExists 효과의 **details** 속성에는 일치하는 관련된 리소스를 정의하는 모든 하위 속성 및 실행할 템플릿 배포가 있습니다.

- **Type**[필수]
  - 일치하는 관련된 리소스의 형식을 지정합니다.
  - **if** 조건 리소스 아래의 리소스를 인출하려는 시도로 시작한 다음, **if** 리소스 조건와 동일한 리소스 그룹 내에서 쿼리합니다.
- **Name**(옵션)
  - 일치하는 리소스의 정확한 이름을 지정하고 정책에서 지정된 형식의 모든 리소스 대신 하나의 특정 리소스를 인출하도록 합니다.
- **ResourceGroupName**(옵션)
  - 다른 리소스 그룹에서 오는 관련된 리소스의 일치를 허용합니다.
  - **type**이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용하지 않습니다.
  - 기본값은 리소스의 리소스 그룹인 **if** 조건입니다.
  - 템플릿 배포가 실행되는 경우 이 값의 리소스 그룹에 배포됩니다.
- **ExistenceScope**(옵션)
  - 허용되는 값은 _Subscription_ 및 _ResourceGroup_입니다.
  - 일치하는 관련된 리소스를 인출할 범위를 설정합니다.
  - **type**이 **if** 조건 리소스의 아래에 있는 리소스인 경우 적용하지 않습니다.
  - _ResourceGroup_의 경우 **ResourceGroupName**에서 지정된 **if** 조건 리소스의 리소스 그룹 또는 리소스 그룹으로 제한합니다.
  - _Subscription_의 경우 관련된 리소스에 대한 전체 구독을 쿼리합니다.
  - 기본값은 _ResourceGroup_입니다.
- **ExistenceCondition**(옵션)
  - 지정되지 않은 경우 **type**의 관련된 리소스는 효과를 충족하고 배포를 트리거하지 않습니다.
  - **if** 조건에 대해 정책 규칙과 동일한 언어를 사용하지만 관련된 각 리소스에 대해 개별적으로 평가됩니다.
  - 일치하는 관련된 리소스가 true로 평가하는 경우 효과가 만족되고 배포를 트리거하지 않습니다.
  - [field()]를 사용하여 **if** 조건의 값과 같은지 확인할 수 있습니다.
  - 예를 들어 부모 리소스(**if** 조건에서)가 일치하는 관련된 리소스와 동일한 리소스 위치에 있는지 확인하는 데 사용될 수 있습니다.
- **Deployment**[필수]
  - 이 속성은 `Microsoft.Resources/deployments` PUT API로 전달되므로 모든 템플릿 배포를 포함해야 합니다. 자세한 내용은 [배포 REST API](/rest/api/resources/deployments)를 참조하세요.

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
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

리소스는 여러 할당에 의해 영향을 받을 수 있습니다. 이러한 할당은 동일한 범위(특정 리소스, 리소스 그룹, 구독 또는 관리 그룹) 또는 서로 다른 범위에 있을 수 있습니다. 이러한 각 할당은 정의된 다른 효과를 가질 수 있습니다. 그럼에도 불구하고 각 정책(직접 또는 이니셔티브의 일부로 할당됨)에 대한 조건 및 효과는 독립적으로 평가됩니다. 예를 들어 정책 1에 구독 A에 대한 리소스 위치가 거부 효과가 있는 'westus'에서만 만들어지도록 제한하는 조건 및 리소스 그룹 B(구독 A에 있음)의 리소스 위치가 감사 효과가 모두 할당된 'eastus'에서만 만들어지도록 제한하는 정책 2가 있는 경우 도출되는 결과는 다음과 같습니다.

- 'eastus'의 리소스 그룹 B에 이미 있는 리소스는 정책 2를 준수하지만 정책 1에 비호환으로 표시됩니다.
- 'eastus'가 아닌 리소스 그룹 B에 이미 있는 리소스는 정책 2에 비호환으로 표시되고, 'westus'가 아닌 경우 정책 1에도 비호환으로 표시됩니다.
- 'westus'가 아닌 구독 A의 모든 새 리소스는 정책 1에 의해 거부됩니다.
- 'westus'에 있는 구독 A/리소스 그룹 B의 모든 새 리소스는 정책 2에서 비호환으로 표시되지만 만들어집니다(정책 1 및 정책 2에 대한 호환은 감사되고 거부되지 않음).

정책 1 및 정책 2 모두에 거부의 효과가 있는 경우 상황은 다음으로 변경됩니다.

- 'eastus'가 아닌 리소스 그룹 B에 이미 있는 리소스는 정책 2에 비호환으로 표시됩니다.
- 'westus'가 아닌 리소스 그룹 B에 이미 있는 리소스는 정책 1에 비호환으로 표시됩니다.
- 'westus'가 아닌 구독 A의 모든 새 리소스는 정책 1에 의해 거부됩니다.
- 구독 A/리소스 그룹 B의 모든 새 리소스는 거부됩니다(해당 위치는 정책 1 및 정책 2 모두를 충족할 수 없으므로).

각 할당은 개별적으로 평가되므로 리소스가 범위의 차이로 인한 간격을 통해 떨어질 기회가 없습니다. 따라서 레이어링 정책 또는 정책 겹침의 네트 결과는 **누적된 가장 제한적**인 것으로 간주됩니다. 즉, 만들려는 리소스는 정책 1 및 정책 2 모두에 거부 효과가 있는 경우 위의 예제와 같이 정책 겹침 및 충돌로 인해 차단될 수 있습니다. 리소스를 여전히 대상 범위에 만들어야 하는 경우 각 할당의 제외를 검토하여 적절한 정책이 적절한 범위에 적용되도록 합니다.

## <a name="next-steps"></a>다음 단계

이제 정책 정의 효과에 대해 이해했으므로 정책 샘플을 검토합니다.

- [Azure Policy 샘플](json-samples.md)에서 더 많은 예제를 검토합니다.
