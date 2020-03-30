---
title: RBAC 및 Azure PowerShell을 통해 역할 할당 추가 또는 제거
description: RBAC(Azure 역할 기반 액세스 제어) 및 Azure PowerShell을 사용하여 사용자, 그룹, 서비스 주체 또는 관리되는 ID에 대한 Azure 리소스에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283213"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Azure RBAC 및 Azure PowerShell을 사용하여 역할 할당 추가 또는 제거

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]이 문서에서는 Azure PowerShell을 사용하여 역할을 할당하는 방법을 설명합니다.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

역할 할당을 추가하거나 제거하려면 다음이 있어야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))
- Azure 클라우드 셸 또는 [Azure PowerShell의](/powershell/azure/install-az-ps) [PowerShell](/azure/cloud-shell/overview)

## <a name="get-object-ids"></a>개체 아이디 받기

역할 할당을 추가하거나 제거하려면 개체의 고유 ID를 지정해야 할 수 있습니다. ID에는 형식이 `11111111-1111-1111-1111-111111111111`있습니다. Azure 포털 또는 Azure PowerShell을 사용하여 ID를 얻을 수 있습니다.

### <a name="user"></a>사용자

Azure AD 사용자의 개체 ID를 얻으려면 [Get-AzADUser](/powershell/module/az.resources/get-azaduser)를 사용할 수 있습니다.

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>그룹

Azure AD 그룹에 대한 개체 ID를 얻으려면 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)을 사용할 수 있습니다.

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>애플리케이션

Azure AD 서비스 주체(응용 프로그램에서 사용하는 ID)에 대한 개체 ID를 얻으려면 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)을 사용할 수 있습니다. 서비스 주체의 경우 응용 프로그램 ID가 **아닌** 개체 ID를 사용합니다.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>역할 할당 추가

RBAC에서 액세스 권한을 부여하려면 역할 할당을 추가합니다.

### <a name="user-at-a-resource-group-scope"></a>리소스 그룹 범위의 사용자

리소스 그룹 범위에서 사용자에 대한 역할 할당을 추가하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>고유한 역할 ID 사용

예를 들어 역할 이름이 변경될 수 있는 경우가 몇 번 있습니다.

- 사용자 지정 역할을 사용하고 있으며 이름을 변경하기로 결정합니다.
- 이름에 **미리 보기(미리 보기)가** 있는 미리 보기 역할을 사용하고 있습니다. 역할이 해제되면 역할 이름이 바뀝니다.

> [!IMPORTANT]
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

역할이름이 변경되더라도 역할 ID는 변경되지 않습니다. 스크립트 또는 자동화를 사용하여 역할 할당을 만드는 경우 역할 이름 대신 고유한 역할 ID를 사용하는 것이 좋습니다. 따라서 역할의 이름이 바뀌면 스크립트가 작동할 가능성이 높습니다.

역할 이름 대신 고유한 역할 ID를 사용하여 역할 할당을 추가하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

다음 예제에서는 *제약 판매* 리소스 그룹 범위에서 *\@alain example.com* 사용자에 게 가상 컴퓨터 [기여자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다. 고유한 역할 ID를 얻으려면 [Get-AzRoleDefinition를](/powershell/module/az.resources/get-azroledefinition) 사용하거나 [Azure 리소스에 대한 기본 제공 역할을](built-in-roles.md)볼 수 있습니다.

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>리소스 범위에서 그룹화

리소스 범위에서 그룹에 대한 역할 할당을 추가하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다. 그룹의 개체 ID를 얻는 방법에 대한 자세한 내용은 [개체 ID 받기를](#get-object-ids)참조하십시오.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>구독 범위의 응용 프로그램

구독 범위에서 응용 프로그램에 대한 역할 할당을 추가하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다. 응용 프로그램의 개체 ID를 얻는 방법에 대한 자세한 내용은 [개체 ID 받기](#get-object-ids)를 참조하십시오.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>관리 그룹 범위의 사용자

관리 그룹 범위에서 사용자에 대한 역할 할당을 추가하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다. 관리 그룹 ID를 얻으려면 Azure 포털의 **관리 그룹** 블레이드에서 찾거나 [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)을 사용할 수 있습니다.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>역할 할당 제거

RBAC에서 액세스 권한을 제거하려면 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)를 사용하여 역할 할당을 제거합니다.

다음 예제에서는 *제약 판매* 리소스 그룹의 *alain\@example.com* 사용자에서 가상 컴퓨터 *기여자* 역할 할당을 제거합니다.

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

다음 예제에서는 구독 범위에서 object_id> <<role_name> 역할을 제거합니다.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

다음 예제에서는 관리 그룹 범위에서 <role_name> 역할을 <object_id> 제거합니다.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

"제공된 정보가 역할 할당에 매핑되지 않습니다"라는 `-Scope` `-ResourceGroupName` 오류 메시지가 나타납니다. 자세한 내용은 [Azure 리소스에 대한 RBAC 문제를 참조하십시오.](troubleshooting.md#role-assignments-with-unknown-security-principal)

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 Azure PowerShell을 사용하여 역할 할당 목록](role-assignments-list-powershell.md)
- [자습서: RBAC 및 Azure PowerShell을 사용 하 여 Azure 리소스에 대 한 그룹 액세스 권한 부여](tutorial-role-assignments-group-powershell.md)
- [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)
- [Azure PowerShell을 사용하여 리소스 관리](../azure-resource-manager/management/manage-resources-powershell.md)
