---
title: 정책 예외 구조의 세부 정보
description: 이니셔티브 또는 정의 평가에서 리소스를 제외 하기 위해 Azure Policy에서 사용 하는 정책 예외 정의에 대해 설명 합니다.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: b3e6a6c9bc7993161697187b6131994c1973b49d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938594"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy 예외 구조

Azure Policy 예외 (미리 보기) 기능을 사용 하 여 리소스 계층 이나 개별 리소스를 평가 하거나 정의 평가에서 _제외_ 합니다. _제외_ 된 리소스는 전체 규정 준수를 계산 하지만 평가 하거나 임시 포기를 가질 수 없습니다. 자세한 내용은 [Azure Policy 범위 이해](./scope.md)를 참조 하세요. Azure Policy 예외는 [리소스 관리자 모드](./definition-structure.md#resource-manager-modes) 에서만 작동 하며 **리소스 공급자 모드**에서는 작동 하지 않습니다.

> [!IMPORTANT]
> 이 기능은 **미리 보기**기간 동안 무료입니다. 가격 책정에 대 한 자세한 내용은 [Azure Policy 가격 책정](https://azure.microsoft.com/pricing/details/azure-policy/)을 참조 하세요. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

JSON을 사용 하 여 정책 예외를 만듭니다. 정책 예외에는 다음에 대 한 요소가 포함 됩니다.

- 표시 이름
- description
- metadata
- 정책 할당
- 이니셔티브 내의 정책 정의
- 예외 범주
- expiration

> [!NOTE]
> 정책 예외는 리소스 계층에서 자식 개체로 만들어지거나 개별 리소스에 예외가 부여 되었으므로 해당 대상이 예외 정의에 포함 되지 않습니다.

예를 들어 다음 JSON은 라는 이니셔티브 할당에 대 한 리소스 **면제** 범주의 정책 예외를 보여 줍니다 `resourceShouldBeCompliantInit` . 리소스는 이니셔티브 _exempt_ 의 두 정책 정의, `customOrgPolicy` 사용자 지정 정책 정의 (참조 `requiredTags` ) 및 ' 허용 된 위치 ' 기본 제공 정책 정의 (ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , 참조) 에서만 제외 됩니다 `allowedLocations` .

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

정책 예외에 사용 되는 일치 항목으로 관련 이니셔티브의 코드 조각입니다 `policyDefinitionReferenceIds` .

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>표시 이름 및 설명

**DisplayName** 및 **description** 을 사용 하 여 정책 예외를 식별 하 고 특정 리소스와 함께 사용 하기 위한 컨텍스트를 제공 합니다. **displayName**은 최대 길이가 _128_자이고 **description**은 최대 길이가 _512_자입니다.

## <a name="metadata"></a>메타데이터

**Metadata** 속성을 사용 하면 관련 정보를 저장 하는 데 필요한 자식 속성을 만들 수 있습니다. 위의 예제에서 **event.request.requestedby.displayname**, **approvedBy**, **approvedOn**및 **ticketRef** 속성은 예외를 요청 하는 사람, 승인 된 사람 및 시기, 요청에 대 한 내부 추적 티켓을 제공 하는 고객 값을 포함 합니다. 이러한 **메타 데이터** 속성은 예 이지만 필요 하지 않으며 **메타 데이터** 는 이러한 자식 속성으로 제한 되지 않습니다.

## <a name="policy-assignment-id"></a>정책 할당 ID

이 필드는 정책 할당 또는 이니셔티브 할당의 전체 경로 이름 이어야 합니다.
`policyAssignmentId` 는 배열이 아니라 문자열입니다. 이 속성은 부모 리소스 계층 또는 개별 리소스가 _제외_ 되는 할당을 정의 합니다.

## <a name="policy-definition-ids"></a>정책 정의 Id

`policyAssignmentId`이 이니셔티브 할당에 대 한 인 경우 `policyDefinitionReferenceIds` 속성을 사용 하 여 주체 리소스가에 대 한 예외가 있는 이니셔티브의 정책 정의를 지정할 수 있습니다. 하나 이상의 포함 된 정책 정의에서 리소스가 제외 될 수 있으므로이 속성은 _배열_입니다. 값은 필드에 있는 이니셔티브 정의의 값과 일치 해야 합니다 `policyDefinitions.policyDefinitionReferenceId` .

## <a name="required-permissions"></a>필요한 사용 권한

정책 예외 개체를 관리 하는 데 필요한 Azure RBAC 권한은 `Microsoft.Authorization/policyExemptions` 작업 그룹에 있습니다. 기본 제공 역할 [리소스 정책 참가자](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) 및 [보안 관리자](../../../role-based-access-control/built-in-roles.md#security-admin) 는 모두 `read` 및 `write` 사용 권한을 가지 며 [Policy Insights 데이터 기록기 (미리 보기)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) 에는 `read` 권한이 있습니다.

예외에는 예외를 부여 하는 영향 때문에 추가 보안 수단이 있습니다. `Microsoft.Authorization/policyExemptions/write`리소스 계층 이나 개별 리소스에 대 한 작업을 요구 하는 것 외에는 예외 작성자에 게 `exempt/Action` 대상 할당에 대 한 동사가 있어야 합니다.

## <a name="expiration"></a>만료

리소스 계층 또는 개별 리소스가 더 이상 할당에 _제외_ 되지 않는 경우를 설정 하려면 속성을 설정 `expiresOn` 합니다. 이 선택적 속성은 Universal ISO 8601 DateTime 형식 이어야 합니다 `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> 날짜에 도달 하면 정책 예외가 삭제 되지 않습니다 `expiresOn` . 개체는 레코드 유지를 위해 유지 되지만 예외는 더 이상 적용 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.