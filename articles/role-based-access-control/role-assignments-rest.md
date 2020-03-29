---
title: RBAC 및 REST API를 사용 하 고 역할 할당 추가 또는 제거
description: AZURE 역할 기반 액세스 제어(RBAC) 및 REST API를 사용하여 사용자, 그룹, 서비스 주체 또는 관리되는 ID에 대한 Azure 리소스에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063005"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Azure RBAC 및 REST API를 사용하여 역할 할당 추가 또는 제거

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]이 문서에서는 REST API를 사용하여 역할을 할당하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

역할 할당을 추가하거나 제거하려면 다음이 있어야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))

## <a name="add-a-role-assignment"></a>역할 할당 추가

RBAC에서 액세스 권한을 부여하려면 역할 할당을 추가합니다. 역할 할당을 추가하려면 역할 할당 - REST API 만들기및 보안 주체, 역할 정의 및 범위를 [지정합니다.](/rest/api/authorization/roleassignments/create) 이 API를 호출하려면 `Microsoft.Authorization/roleAssignments/write` 작업에 액세스할 수 있어야 합니다. 기본 제공 역할의 경우 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)에게만 이러한 작업의 권한이 부여됩니다.

1. 할당하려는 역할 정의에 대한 식별자를 가져오려면 [역할 정의 - 나열](/rest/api/authorization/roledefinitions/list) REST API를 사용하거나 [기본 제공 역할](built-in-roles.md)을 참조하세요.

1. GUID 도구를 사용하여 역할 할당 식별자에 사용할 고유 식별자를 생성합니다. 식별자의 형식은 `00000000-0000-0000-0000-000000000000`입니다.

1. 다음 요청 및 본문으로 시작합니다.

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
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
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 리소스 |

    이전 예제에서 microsoft.web은 앱 서비스 인스턴스를 참조하는 리소스 공급자입니다. 마찬가지로 다른 리소스 공급자를 사용하고 범위를 지정할 수 있습니다. 자세한 내용은 [Azure 리소스 공급자 및 유형 및](../azure-resource-manager/management/resource-providers-and-types.md) 지원되는 Azure 리소스 관리자 리소스 공급자 [작업을](resource-provider-operations.md)참조하십시오.  

1. *{roleAssignmentName}* 을 역할 할당의 GUID 식별자로 바꿉니다.

1. 요청 본문 내에서 *{scope}를* 역할 할당의 범위로 바꿉습니다.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 리소스 |

1. *{roleDefinitionId}* 를 역할 정의 식별자로 바꿉니다.

1. *{principalId}* 를 역할이 할당될 사용자, 그룹 또는 서비스 주체의 개체 식별자로 바꿉니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거

RBAC에서 액세스 권한을 제거하려면 역할 할당을 제거해야 합니다. 역할 할당을 제거하려면 [역할 할당 - 삭제](/rest/api/authorization/roleassignments/delete) REST API를 사용합니다. 이 API를 호출하려면 `Microsoft.Authorization/roleAssignments/delete` 작업에 액세스할 수 있어야 합니다. 기본 제공 역할의 경우 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)에게만 이러한 작업의 권한이 부여됩니다.

1. 역할 할당 식별자(GUID)를 가져옵니다. 이 식별자는 역할 할당을 처음 만들 때 반환되거나 역할 할당을 나열하여 가져올 수 있습니다.

1. 다음 요청으로 시작합니다.

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 제거할 역할 할당에 대한 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 리소스 |

1. *{roleAssignmentName}* 을 역할 할당의 GUID 식별자로 바꿉니다.

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 REST API를 사용하여 역할 할당 목록](role-assignments-list-rest.md)
- [리소스 관리자 템플릿과 리소스 관리자 REST API로 리소스 배포](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API 참조](/rest/api/azure/)
- [REST API를 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기](custom-roles-rest.md)
