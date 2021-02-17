---
title: REST API를 사용 하 여 Azure 역할 할당-Azure RBAC
description: REST API 및 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 id에 대 한 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: d012173adb5e238282e107b832ed9c6895237e48
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556067"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>REST API를 사용 하 여 Azure 역할 할당

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 이 문서에서는 REST API를 사용 하 여 역할을 할당 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Azure 역할 할당

역할을 할당 하려면 [역할 할당-REST API 만들기](/rest/api/authorization/roleassignments/create) 를 사용 하 고 보안 주체, 역할 정의 및 범위를 지정 합니다. 이 API를 호출하려면 `Microsoft.Authorization/roleAssignments/write` 작업에 액세스할 수 있어야 합니다. 기본 제공 역할의 경우 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)에게만 이러한 작업의 권한이 부여됩니다.

1. 할당하려는 역할 정의에 대한 식별자를 가져오려면 [역할 정의 - 나열](/rest/api/authorization/roledefinitions/list) REST API를 사용하거나 [기본 제공 역할](built-in-roles.md)을 참조하세요.

1. GUID 도구를 사용하여 역할 할당 식별자에 사용할 고유 식별자를 생성합니다. 식별자의 형식은 `00000000-0000-0000-0000-000000000000`입니다.

1. 다음 요청 및 본문으로 시작합니다.

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. URI 내에서 *{scope}* 를 역할 할당에 대한 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 리소스 |

    이전 예제에서 microsoft. web은 App Service 인스턴스를 참조 하는 리소스 공급자입니다. 마찬가지로 다른 리소스 공급자를 사용 하 여 범위를 지정할 수 있습니다. 자세한 내용은 [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md) 및 지원 되는 [azure 리소스 공급자 작업](resource-provider-operations.md)을 참조 하세요.  

1. *{RoleAssignmentId}* 를 역할 할당의 GUID 식별자로 바꿉니다.

1. 요청 본문 내에서 *{scope}* 를 역할 할당의 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 리소스 |

1. *{roleDefinitionId}* 를 역할 정의 식별자로 바꿉니다.

1. *{principalId}* 를 역할이 할당될 사용자, 그룹 또는 서비스 주체의 개체 식별자로 바꿉니다.

다음 요청 및 본문은 구독 범위에서 사용자에 게 [백업 읽기 권한자](built-in-roles.md#backup-reader) 역할을 할당 합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

다음은 출력 예제입니다.

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="next-steps"></a>다음 단계

- [REST API를 사용 하 여 Azure 역할 할당 나열](role-assignments-list-rest.md)
- [리소스 관리자 템플릿 및 리소스 관리자를 사용 하 여 리소스 배포 REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API 참조](/rest/api/azure/)
- [REST API를 사용 하 여 Azure 사용자 지정 역할 만들기 또는 업데이트](custom-roles-rest.md)
