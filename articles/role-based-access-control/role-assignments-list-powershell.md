---
title: Azure RBAC 및 Azure PowerShell를 사용 하 여 역할 할당 나열
description: Azure RBAC (역할 기반 액세스 제어) 및 Azure PowerShell를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 id가 액세스할 수 있는 리소스를 확인 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: c30f1246dccbe14445b0f7db8584e37c0f4be6ab
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710413"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Azure RBAC 및 Azure PowerShell를 사용 하 여 역할 할당 나열

이 문서 [!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Azure PowerShell를 사용 하 여 역할 할당을 나열 하는 방법을 설명 합니다.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>전제 조건

- Azure Cloud Shell 또는 [Azure PowerShell](/powershell/azure/install-az-ps) [의 PowerShell](/azure/cloud-shell/overview)

## <a name="list-role-assignments-for-the-current-subscription"></a>현재 구독에 대 한 역할 할당 나열

현재 구독의 모든 역할 할당 목록을 가져오는 가장 쉬운 방법은 (루트 및 관리 그룹에서 상속 된 역할 할당 포함) 매개 변수 없이 [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 를 사용 하는 것입니다.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>구독의 역할 할당 나열

구독 범위에서 모든 역할 할당을 나열 하려면 [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용 합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 찾거나 [AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)를 사용할 수 있습니다.

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

지정된 사용자에 할당된 모든 역할을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

지정된 사용자에게 할당된 역할 및 사용자가 속한 그룹에 할당된 역할을 모두 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>리소스 그룹에 대한 역할 할당 목록

리소스 그룹 범위에서 모든 역할 할당을 나열 하려면 [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용 합니다.

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>관리 그룹의 역할 할당 나열

관리 그룹 범위에서 모든 역할 할당을 나열 하려면 [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용 합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 찾거나 [AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)를 사용할 수 있습니다.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>클래식 서비스 관리자 및 공동 관리자에 대한 역할 할당 나열

클래식 구독 관리자 및 공동 관리자에 대한 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 Azure PowerShell를 사용 하 여 역할 할당 추가 또는 제거](role-assignments-powershell.md)
