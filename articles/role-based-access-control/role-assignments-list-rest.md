---
title: REST API를 사용하여 Azure 역할 할당 나열 - Azure RBAC
description: REST API 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 액세스할 수 있는 사용자, 그룹, 서비스 주체 또는 관리 ID를 확인하는 방법을 알아봅니다.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.topic: how-to
ms.date: 02/27/2021
ms.author: rolyon
ms.openlocfilehash: d7f110ac29fa863ba07ea03cac92d0f03e444e56
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109787422"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>REST API를 사용하여 Azure 역할 할당 나열하기

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] 이 문서에서는 REST API를 사용하여 역할 할당을 나열하는 방법을 설명합니다.

> [!NOTE]
> 조직에서 [Azure Lighthouse](../lighthouse/overview.md)를 사용하는 서비스 공급자에 대해 아웃소싱된 관리 기능을 사용하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="list-role-assignments"></a>역할 할당 나열

Azure RBAC에서 역할 할당을 나열하면 액세스 권한이 나열됩니다. 역할 할당을 나열하려면 [역할 할당 - 나열](/rest/api/authorization/roleassignments/list) REST API 중 하나를 사용합니다. 결과를 구체화하려면 범위와 선택적 필터를 지정합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 역할 할당을 나열하려는 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | 유형 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 리소스 |

    이전 예제에서 microsoft.web은 App Service 인스턴스를 참조하는 리소스 공급자입니다. 마찬가지로 다른 리소스 공급자를 사용하여 범위를 지정할 수 있습니다. 자세한 내용은 [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md) 및 지원되는 [Azure 리소스 공급자 작업](resource-provider-operations.md)을 참조하세요.  
     
1. *{filter}* 를 역할 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | `$filter=atScope()` | 하위 범위의 역할 할당은 제외하고 지정된 범위에 대한 역할 할당만 나열합니다. |
    > | `$filter=assignedTo('{objectId}')` | 지정된 사용자 또는 서비스 주체에 대한 역할 할당을 나열합니다.<br/>사용자가 역할 할당이 있는 그룹의 멤버인 경우 해당 역할 할당도 나열됩니다. 해당 필터는 그룹에 대해 전이적입니다. 즉, 사용자가 그룹의 멤버이고 해당 그룹이 역할 할당이 있는 다른 그룹의 멤버인 경우에도 해당 역할 할당이 나열됩니다.<br/>해당 필터는 사용자 또는 서비스 주체의 개체 ID만 허용합니다. 그룹의 개체 ID를 전달할 수는 없습니다. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 지정된 사용자 또는 서비스 주체에 대해 지정된 범위에서 역할 할당을 나열합니다. |
    > | `$filter=principalId+eq+'{objectId}'` | 지정된 사용자, 그룹 또는 서비스 주체에 대한 역할 할당을 나열합니다. |

다음 요청은 구독 범위에서 지정된 사용자에 대한 모든 역할 할당을 나열합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

다음은 출력 예제입니다.

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

- [REST API를 사용하여 Azure 역할 할당](role-assignments-rest.md)
- [Azure REST API 참조](/rest/api/azure/)
