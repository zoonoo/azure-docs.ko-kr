---
title: 정책 할당 구조 세부 정보
description: Azure Policy에서 평가를 위해 정책 정의 및 매개 변수를 리소스에 연결하는 데 사용하는 정책 할당 정의를 설명합니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 1b29becc3324c0d174db2102c4ae7ac15206e567
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567959"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy 할당 구조

정책 할당은 Azure Policy에서 정책이나 이니셔티브에 할당되는 리소스를 정의하는 데 사용됩니다. 정책 할당은 정책을 할당할 때 해당 리소스 그룹에 대한 매개 변수 값을 결정할 수 있으므로 규정 준수 요구 사항이 다른, 동일한 리소스 속성을 처리하는 정책 정의를 다시 사용할 수 있습니다.

JSON을 사용하여 정책 할당을 만듭니다. 정책 할당에는 다음 요소가 포함됩니다.

- 표시 이름
- description
- metadata
- 적용 모드
- 제외된 범위
- 정책 정의
- 비준수 메시지
- 매개 변수

예를 들어 다음 JSON은 동적 매개 변수를 사용하는 _DoNotEnforce_ 모드의 정책 할당을 보여 줍니다.

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

모든 Azure Policy 샘플은 [Azure Policy 샘플](../samples/index.md)에 있습니다.

## <a name="display-name-and-description"></a>표시 이름 및 설명

**displayName** 및 **description** 을 사용하여 정책 할당을 식별하고 특정 리소스 집합과 함께 사용하기 위한 컨텍스트를 제공합니다. **displayName** 은 최대 길이가 _128_ 자이고 **description** 은 최대 길이가 _512_ 자입니다.

## <a name="metadata"></a>메타데이터

선택적 `metadata` 속성은 정책 할당에 대한 정보를 저장합니다. 고객은 `metadata`에서 조직에 유용한 모든 속성 및 값을 정의할 수 있습니다. 그러나 Azure Policy에서 사용되는 몇 가지 _공통_ 속성이 있습니다. 각 `metadata` 속성의 제한은 1024자입니다.

### <a name="common-metadata-properties"></a>공통 메타데이터 속성

- `assignedBy`(문자열): 할당을 만든 보안 주체의 이름입니다.
- `createdBy`(문자열): 할당을 만든 보안 주체의 GUID 입니다.
- `createdOn`(문자열): 할당 생성 시간의 유니버설 ISO 8601날짜/시간 형식입니다.
- `parameterScopes`(개체): 키는 [strongType](./definition-structure.md#strongtype)으로 구성된 매개 변수 이름과 일치하며 값은 Portal에서 사용되는 리소스 그룹을 정의하여, _strongType_ 일치를 통해 사용 가능한 리소스 목록을 제공하는 키-값 쌍의 컬렉션입니다. 범위가 할당 범위와 다른 경우 Portal에서 이 값을 설정합니다. 설정된 경우 Portal에서 정책 할당을 편집하면 매개 변수의 범위가 자동으로 이 값으로 설정됩니다. 그러나 범위는 값으로 잠기지 않으며 다른 범위로 변경할 수 있습니다.

  다음 `parameterScopes` 예제는 이름이 **backupPolicyId** 인 _strongType_ 매개 변수에 대한 것으로, Portal에서 할당이 편집될 때 리소스 선택에 대한 범위를 설정합니다.

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy`(문자열): 해당하는 경우 할당을 업데이트한 보안 주체의 이름입니다.
- `updatedOn`(문자열): 해당하는 경우 할당 업데이트 시간의 유니버설 ISO 8601날짜/시간 형식입니다.

## <a name="enforcement-mode"></a>적용 모드

**enforcementMode** 속성은 [Azure 활동 로그](../../../azure-monitor/essentials/platform-logs-overview.md)에서 정책 효과를 시작하거나 항목을 트리거하지 않고 기존 리소스에 대한 정책 결과를 테스트하는 기능을 고객에게 제공합니다. 해당 시나리오를 일반적으로 “What If”라고 하며 안전 배포 방법에 부합합니다. **enforcementMode** 는 [사용 안 함](./effects.md#disabled) 효과와 다릅니다. 해당 효과는 리소스 평가가 전혀 일어나지 않도록 합니다.

해당 속성 값은 다음과 같습니다.

|모드 |JSON 값 |유형 |수동으로 수정 |활동 로그 항목 |Description |
|-|-|-|-|-|-|
|사용 |기본값 |문자열 |예 |예 |리소스를 만들거나 업데이트하는 동안 정책 효과가 적용됩니다. |
|사용 안 함 |DoNotEnforce |문자열 |예 |예 | 리소스를 만들거나 업데이트하는 동안 정책 효과가 적용되지 않습니다. |

정책 또는 이니셔티브 정의에 **enforcementMode** 가 지정되지 않은 경우에는 ‘기본값’을 사용합니다. **enforcementMode** 가 _DoNotEnforce_ 로 설정된 경우에도 [deployIfNotExists](./effects.md#deployifnotexists) 정책에 대해 [수정 작업](../how-to/remediate-resources.md)을 시작할 수 있습니다.

## <a name="excluded-scopes"></a>제외된 범위

할당 **범위** 는 모든 자식 리소스 컨테이너 및 자식 리소스를 포함합니다. 자식 리소스 컨테이너 또는 자식 리소스에 정의가 적용되지 않아야 하는 경우에는 **notScopes** 를 설정하여 계산에서 ‘제외’할 수 있습니다. 해당 속성은 하나 이상의 리소스 컨테이너 또는 리소스를 평가에서 제외할 수 있도록 하는 배열입니다. **notScopes** 는 초기 할당을 만든 후 추가하거나 업데이트할 수 있습니다.

> [!NOTE]
> ‘제외’된 리소스는 ‘예외’ 리소스와 다릅니다.  자세한 내용은 [Azure Policy의 범위 이해](./scope.md)를 참조하세요.

## <a name="policy-definition-id"></a>정책 정의 ID

해당 필드는 정책 정의 또는 이니셔티브 정의의 전체 경로 이름이어야 합니다.
`policyDefinitionId`는 문자열이며 배열이 아닙니다. 여러 정책을 함께 할당하는 경우 대신 [이니셔티브](./initiative-definition-structure.md)를 사용하는 것이 좋습니다.

## <a name="non-compliance-messages"></a>비준수 메시지

리소스가 정책 또는 이니셔티브 정의를 준수하지 않는 이유를 설명하는 사용자 지정 메시지를 설정하려면 할당 정의에서 `nonComplianceMessages`를 설정합니다. 해당 노드는 `message` 항목의 배열입니다. 해당 사용자 지정 메시지는 비준수에 대한 기본 오류 메시지에 추가되며 선택 사항입니다.

> [!IMPORTANT]
> 비준수에 대한 사용자 지정 메시지는 [리소스 관리자 모드](./definition-structure.md#resource-manager-modes) 정의를 사용하는 정의 또는 이니셔티브에서만 지원됩니다.

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

이니셔티브에 대한 할당인 경우 이니셔티브의 각 정책 정의에 대해 서로 다른 메시지를 구성할 수 있습니다. 메시지는 이니셔티브 정의에 구성된 `policyDefinitionReferenceId` 값을 사용합니다. 자세한 내용은 [정책 정의 속성](./initiative-definition-structure.md#policy-definition-properties)을 참조하세요.

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>매개 변수

이 정책 할당 세그먼트는 [정책 정의 또는 이니셔티브 정의](./definition-structure.md#parameters)에 정의된 매개 변수에 대한 값을 제공합니다. 해당 설계를 통해 여러 리소스에 정책 또는 이니셔티브 정의를 다시 사용할 수 있지만, 다른 비즈니스 가치 또는 결과를 확인해야 합니다.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

해당 예제에서 이전에 정책 정의에 정의된 매개 변수는 `prefix` 및 `suffix`입니다. 이 특정 정책 할당은 `prefix`를 **DeptA** 로 설정하고 `suffix`를 **-LC** 로 설정합니다. 동일한 정책 정의를 다른 부서에 다른 매개 변수 집합과 다시 사용할 수 있으므로 정책 정의의 중복 및 복잡성을 줄이면서 유연성을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
