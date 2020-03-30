---
title: 정책 할당 구조에 대한 세부 정보
description: Azure Policy에서 정책 정의 및 매개 변수를 평가를 위해 리소스에 관련하는 데 사용하는 정책 할당 정의를 설명합니다.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265299"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy 할당 구조

정책 할당은 Azure Policy에서 어떤 리소스가 할당되는정책 또는 이니셔티브를 정의하는 데 사용됩니다. 정책 할당은 할당 시 해당 리소스 그룹에 대한 매개 변수 값을 결정할 수 있으므로 규정 준수에 대한 요구사항이 다르므로 동일한 리소스 속성을 처리하는 정책 정의를 다시 사용할 수 있습니다.

JSON을 사용하여 정책 할당을 만듭니다. 정책 할당에는 다음에 대한 요소가 포함되어 있습니다.

- 표시 이름
- description
- metadata
- 적용 모드
- 정책 정의
- 매개 변수

예를 들어 다음 JSON은 동적 매개 변수가 있는 _DoNotEnforce_ 모드에서 정책 할당을 보여 주며 다음과 같은 작업을 수행합니다.

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

모든 Azure 정책 샘플은 [Azure 정책 샘플에 있습니다.](../samples/index.md)

## <a name="display-name-and-description"></a>표시 이름 및 설명

**displayName** 및 **설명을** 사용하여 정책 할당을 식별하고 특정 리소스 집합과 함께 사용할 컨텍스트를 제공합니다. **displayName**은 최대 길이가 _128_자이고 **description**은 최대 길이가 _512_자입니다.

## <a name="enforcement-mode"></a>적용 모드

**enforcementMode** 속성은 고객에게 Azure [Activity 로그에서](../../../azure-monitor/platform/platform-logs-overview.md)정책 효과를 시작하거나 항목을 트리거하지 않고 기존 리소스에 대한 정책 결과를 테스트할 수 있는 기능을 제공합니다. 이 시나리오는 일반적으로 "What If"라고 하며 안전한 배포 관행에 부합합니다. **enforcementMode는** 사용 [안 함](./effects.md#disabled) 효과와 다르며, 그 효과는 리소스 평가가 전혀 발생하지 않도록 합니다.

이 속성에는 다음과 같은 값이 있습니다.

|Mode |JSON 값 |Type |수동으로 수정 |활동 로그 항목 |설명 |
|-|-|-|-|-|-|
|사용 |기본값 |문자열 |yes |yes |정책 효과는 리소스를 만들거나 업데이트하는 동안 적용됩니다. |
|사용 안 함 |DoNotEnforce |문자열 |yes |예 | 리소스를 만들거나 업데이트하는 동안 정책 효과가 적용되지 않습니다. |

**enforcementMode가** 정책 또는 이니셔티브 정의에 지정되지 않은 경우 _기본값_ 값이 사용됩니다. **적용 모드가** _DoNotEnforce로_설정되어 있는 경우에도 [배포IfNotExists](./effects.md#deployifnotexists) 정책에 대한 [업데이트 관리 작업을](../how-to/remediate-resources.md) 시작할 수 있습니다.

## <a name="policy-definition-id"></a>정책 정의 ID

이 필드는 정책 정의 또는 이니셔티브 정의의 전체 경로 이름이어야 합니다.
`policyDefinitionId`는 문자열이 아니라 배열입니다. 여러 정책이 함께 할당되는 경우 [이니셔티브를](./definition-structure.md#initiatives) 대신 사용하는 것이 좋습니다.

## <a name="parameters"></a>매개 변수

정책 할당의 이 세그먼트는 정책 정의 또는 [이니셔티브 정의에](./definition-structure.md#parameters)정의된 매개 변수에 대한 값을 제공합니다.
이 디자인을 사용하면 정책 또는 이니셔티브 정의를 다른 리소스로 재사용할 수 있지만 다른 비즈니스 가치 나 결과를 확인할 수 있습니다.

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

이 예제에서는 정책 정의에 이전에 정의된 `prefix` 매개 `suffix`변수는 및 입니다. 이 특정 정책 `prefix` 할당은 `suffix` **DeptA** 및 **-LC로**설정됩니다. 동일한 정책 정의는 다른 부서에 대해 다른 매개 변수 집합으로 재사용할 수 있으며, 유연성을 제공하면서 정책 정의의 중복과 복잡성을 줄입니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조에](./definition-structure.md)대해 자세히 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 이해합니다.
- [규정 준수 데이터를 얻는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스를 수정하는](../how-to/remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)