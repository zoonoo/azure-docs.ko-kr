---
title: Azure 역할 할당 제거 - Azure RBAC
description: Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 사용자, 그룹, 서비스 주체 또는 관리 ID의 Azure 리소스에 대한 액세스를 제거하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100561507"
---
# <a name="remove-azure-role-assignments"></a>Azure 역할 할당 제거

[Azure RBAC(Azure 역할 기반 액세스 제어)](../../articles/role-based-access-control/overview.md)는 Azure 리소스에 대한 액세스를 관리하는 데 사용하는 권한 부여 시스템입니다. Azure 리소스에서 액세스 권한을 제거하려면 역할 할당을 제거합니다. 이 문서에서는 Azure Portal, Azure PowerShell, Azure CLI 및 REST API를 사용하여 역할 할당을 제거하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

역할 할당을 제거하려면 다음이 필요합니다.

- [사용자 액세스 관리자](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) 또는 [소유자](../../articles/role-based-access-control/built-in-roles.md#owner) 등의 `Microsoft.Authorization/roleAssignments/delete` 권한

## <a name="azure-portal"></a>Azure portal

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 제거하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

1. 역할 할당 목록에서, 제거할 역할 할당이 있는 보안 주체 옆에 확인란을 추가합니다.

   ![제거하도록 선택된 역할 할당](./media/role-assignments-remove/rg-role-assignments-select.png)

1. **제거** 를 클릭합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-remove/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예** 를 클릭합니다.

    상속된 역할 할당을 제거할 수 없다는 메시지가 표시되면 자식 범위에서 역할 할당을 제거하려고 시도하는 것입니다. 역할이 할당된 범위에서 액세스 제어(IAM)를 열고 다시 시도해야 합니다. 올바른 범위에서 액세스 제어(IAM)를 여는 빠른 방법은 **범위** 열을 확인하고 **(상속됨)** 옆에 있는 링크를 클릭하는 것입니다.

   ![상속된 역할 할당에 대한 역할 할당 메시지 제거](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell에서 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)를 사용하여 역할 할당을 제거합니다.

다음 예제에서는 *pharma-sales* 리소스 그룹의 *patlong\@contoso.com* 사용자로부터 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할 할당을 제거합니다.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

구독 범위에서 ID가 22222222-2222-2222-2222-222222222222인 *Ann Mack Team* 그룹에서 [Reader](built-in-roles.md#reader) 역할을 제거합니다.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

관리 그룹 범위에 있는 *alain\@example.com* 사용자로부터 [Billing Reader](built-in-roles.md#billing-reader) 역할을 제거합니다.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

"제공된 정보가 역할 할당에 매핑되지 않습니다."라는 오류 메시지가 표시되면 `-Scope` 또는 `-ResourceGroupName` 매개 변수도 지정해야 합니다. 자세한 내용은 [Azure RBAC 문제 해결](troubleshooting.md#role-assignments-with-identity-not-found)을 참조하세요.

## <a name="azure-cli"></a>Azure CLI

Azure CLI에서 [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete)를 사용하여 역할 할당을 제거합니다.

다음 예제에서는 [pharma-sales](built-in-roles.md#virtual-machine-contributor) 리소스 그룹의 *patlong\@contoso.com* 사용자로부터 *Virtual Machine Contributor* 역할 할당을 제거합니다.

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

구독 범위에서 ID가 22222222-2222-2222-2222-222222222222인 [Ann Mack Team](built-in-roles.md#reader) 그룹에서 *Reader* 역할을 제거합니다.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

관리 그룹 범위에 있는 [alain](built-in-roles.md#billing-reader)example.com *사용자로부터 \@Billing Reader* 역할을 제거합니다.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>REST API

REST API에서 [역할 할당 - 삭제](/rest/api/authorization/roleassignments/delete)를 사용하여 역할 할당을 제거합니다.

1. 역할 할당 식별자(GUID)를 가져옵니다. 이 식별자는 역할 할당을 처음 만들 때 반환되거나 역할 할당을 나열하여 가져올 수 있습니다.

1. 다음 요청으로 시작합니다.

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 제거할 역할 할당에 대한 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | 유형 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 리소스 |

1. *{roleAssignmentId}* 를 역할 할당의 GUID 식별자로 바꿉니다.

다음 요청은 구독 범위에서 지정된 역할 할당을 제거합니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

다음은 출력 예제입니다.

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>ARM 템플릿

ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 역할 할당을 제거하는 방법은 없습니다. 역할 할당을 제거하려면 Azure Portal, Azure PowerShell, Azure CLI 또는 REST API와 같은 다른 도구를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 Azure 역할 할당을 나열](role-assignments-list-portal.md)합니다.
- [Azure PowerShell을 사용하여 Azure 역할 할당 나열](role-assignments-list-powershell.md)
- [Azure RBAC 문제 해결](troubleshooting.md)
