---
title: 정책 할당 구조의 세부 정보
description: Azure Policy에서 평가를 위해 리소스에 정책 정의 및 매개 변수를 연결 하는 데 사용 하는 정책 할당 정의에 대해 설명 합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: a01cee2ba803a048e426507b57b96d0833743636
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181380"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy 할당 구조

정책 할당은 정책이 나 이니셔티브 중에 할당 되는 리소스를 정의 하는 Azure Policy에서 사용 됩니다. 정책 할당은 할당 시 해당 리소스 그룹에 대 한 매개 변수 값을 결정할 수 있으므로 준수에 대 한 다른 요구 사항과 동일한 리소스 속성을 처리 하는 정책 정의를 다시 사용할 수 있습니다.

여기에서는 Azure Policy에서 사용하는 스키마를 찾을 수 있습니다. [https://docs.microsoft.com/azure/templates/microsoft.authorization/2019-01-01/policyassignments](/azure/templates/microsoft.authorization/2019-01-01/policyassignments)

JSON을 사용 하 여 정책 할당을 만듭니다. 정책 정의에는 다음 요소가 포함됩니다.

- 표시 이름
- description
- 메타데이터
- 적용 모드
- 정책 정의
- parameters

예를 들어 다음 JSON은 동적 매개 변수를 사용 하는 _DoNotEnforce_ 모드의 정책 할당을 보여 줍니다.

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
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

**DisplayName** 및 **description** 을 사용 하 여 정책 할당을 식별 하 고 특정 리소스 집합과 함께 사용 하기 위한 컨텍스트를 제공 합니다. **displayName**은 최대 길이가 _128_자이고 **description**은 최대 길이가 _512_자입니다.

## <a name="enforcement-mode"></a>적용 모드

**EnforcementMode** 속성은 [Azure 활동 로그](../../../azure-monitor/platform/activity-logs-overview.md)에서 정책 효과를 시작 하거나 항목을 트리거하지 않고 기존 리소스에 대 한 정책 결과를 테스트 하는 기능을 고객에 게 제공 합니다.
이 시나리오를 일반적으로 "What If" 이라고 하며 안전 배포 방법에 맞춥니다.

이 속성의 값은 다음과 같습니다.

|모드 |JSON 값 |type |수동으로 재구성 |활동 로그 항목 |설명 |
|-|-|-|-|-|-|
|Enabled |기본값 |string |예 |예 |정책 효과는 리소스를 만들거나 업데이트 하는 동안 적용 됩니다. |
|사용 안 함 |DoNotEnforce |string |예 |아니요 | 정책 효과는 리소스를 만들거나 업데이트 하는 동안 적용 되지 않습니다. |

**EnforcementMode** 가 정책 또는 이니셔티브 정의에 지정 되지 않은 경우에는 _기본값_ 을 사용 합니다. **EnforcementMode** 가 _DoNotEnforce_로 설정 된 경우에도 [deployifnotexists](./effects.md#deployifnotexists) 정책에 대해 [재구성 작업](../how-to/remediate-resources.md) 을 시작할 수 있습니다.

## <a name="policy-definition-id"></a>정책 정의 ID

이 필드는 정책 정의 또는 이니셔티브 정의의 전체 경로 이름 이어야 합니다.
`policyDefinitionId`는 배열이 아니라 문자열입니다. 여러 정책을 함께 할당 하는 것이 아니라 [이니셔티브](./definition-structure.md#initiatives) 를 사용 하는 것이 좋습니다.

## <a name="parameters"></a>매개 변수

정책 할당의이 세그먼트는 [정책 정의 또는 이니셔티브 정의](./definition-structure.md#parameters)에 정의 된 매개 변수에 대 한 값을 제공 합니다.
이 설계를 통해 여러 리소스를 사용 하 여 정책 또는 이니셔티브 정의를 다시 사용할 수 있지만 다른 비즈니스 값 또는 결과를 확인할 수 있습니다.

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

이 예제에서 정책 정의 `prefix` 에 이전에 정의 된 매개 변수는 및 `suffix`입니다. 이 `prefix` 특정 정책 할당 **은 비-LC**로 `suffix` **설정** 됩니다. 동일한 정책 정의는 다른 부서에 대 한 다른 매개 변수 집합을 사용 하 여 다시 사용할 수 있으므로 유연성을 제공 하면서 정책 정의의 중복 및 복잡성을 줄일 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](../how-to/getting-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스](../how-to/remediate-resources.md)를 수정 하는 방법에 대해 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.