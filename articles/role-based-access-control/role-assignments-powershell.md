---
title: RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 액세스 관리 | Microsoft Docs
description: RBAC (역할 기반 액세스 제어) 및 Azure PowerShell를 사용 하 여 사용자, 그룹 및 응용 프로그램에 대 한 Azure 리소스에 대 한 액세스를 관리 하는 방법을 알아봅니다. 여기에는 액세스 권한을 나열, 부여 및 제거하는 방법이 포함됩니다.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4eaf59200295a25498d3c8b84196e73a703b055d
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995243"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 RBAC 및 Azure PowerShell을 사용하여 사용자, 그룹 및 애플리케이션의 액세스 권한을 관리하는 방법을 설명합니다.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

액세스를 관리하려면 다음 중 하나가 필요합니다.

* [Azure Cloud Shell의 PowerShell](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>역할 나열

### <a name="list-all-available-roles"></a>사용 가능한 모든 역할 나열

할당에 사용할 수 있는 RBAC 역할을 나열하고 액세스 권한을 부여하는 작업을 검사하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용합니다.

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>특정 역할 나열

특정 역할을 나열하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용합니다.

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>역할 정의 나열

### <a name="list-a-role-definition-in-json-format"></a>JSON 형식의 역할 정의를 나열 합니다.

JSON 형식으로 역할 정의를 나열 하려면 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)를 사용 합니다.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>역할의 작업 나열

특정 역할에 대한 작업을 나열하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용합니다.

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="list-access"></a>액세스 권한 나열

RBAC에서 역할 할당을 나열하면 액세스 권한이 나열됩니다.

### <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>리소스 그룹 범위에서 역할 할당 나열

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

### <a name="list-role-assignments-at-a-subscription-scope"></a>구독 범위에서 역할 할당 나열

구독 범위에서 모든 역할 할당을 나열 하려면 [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용 합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 찾거나 [AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)를 사용할 수 있습니다.

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>관리 그룹 범위에서 역할 할당 나열

관리 그룹 범위에서 모든 역할 할당을 나열 하려면 [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용 합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 찾거나 [AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)를 사용할 수 있습니다.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>클래식 서비스 관리자 및 공동 관리자에 대한 역할 할당 나열

클래식 구독 관리자 및 공동 관리자에 대한 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>액세스 허용

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다.

### <a name="search-for-object-ids"></a>개체 ID 검색

역할을 할당하려면 개체(사용자, 그룹 또는 애플리케이션)와 범위 둘 다를 식별해야 합니다.

구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 찾거나 [AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)를 사용할 수 있습니다.

Azure AD 사용자의 개체 ID를 가져오려면 [AzADUser](/powershell/module/az.resources/get-azaduser)을 사용 합니다.

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

Azure AD 그룹의 개체 ID를 가져오려면 [AzADGroup](/powershell/module/az.resources/get-azadgroup)을 사용 합니다.

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

Azure AD 서비스 주체 또는 애플리케이션에 대한 개체 ID를 가져오려면 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)을 사용합니다.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 대한 역할 할당 만들기

리소스 그룹 범위에서 사용자에 게 액세스 권한을 부여 하려면 [AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)을 사용 합니다.

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

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>고유한 역할 ID를 사용 하 여 역할 할당 만들기

역할 이름이 변경 될 수 있는 몇 가지 경우가 있습니다. 예를 들면 다음과 같습니다.

- 사용자 고유의 사용자 지정 역할을 사용 하 고 있으며 이름을 변경 하기로 결정 했습니다.
- 이름에 **(미리 보기)** 가 있는 미리 보기 역할을 사용 하 고 있습니다. 역할을 해제 하면 역할의 이름이 바뀝니다.

> [!IMPORTANT]
> 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

역할의 이름이 바뀐 경우에도 역할 ID는 변경 되지 않습니다. 스크립트 또는 자동화를 사용 하 여 역할 할당을 만드는 경우 역할 이름 대신 고유한 역할 ID를 사용 하는 것이 좋습니다. 따라서 역할의 이름을 바꾸면 스크립트가 작동할 가능성이 높습니다.

역할 이름 대신 고유한 역할 ID를 사용 하 여 역할 할당을 만들려면 [AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용 합니다.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹 범위에서 사용자에 *alain@example.com* 게 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다. 고유한 역할 ID를 얻기 위해 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 를 사용 하거나 [Azure 리소스에 대 한 기본 제공 역할](built-in-roles.md)을 확인할 수 있습니다.

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

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>리소스 범위에서 그룹에 대한 역할 할당 만들기

리소스 범위에서 그룹에 대 한 액세스 권한을 부여 하려면 [AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용 합니다.

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

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>구독 범위에서 애플리케이션에 대한 역할 할당 만들기

구독 범위에서 응용 프로그램에 대 한 액세스 권한을 부여 하려면 [AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용 합니다.

```azurepowershell
New-AzRoleAssignment -ObjectId <application_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
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

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>관리 그룹 범위에서 사용자에 대 한 역할 할당 만들기

관리 그룹 범위에서 사용자에 게 액세스 권한을 부여 하려면 [AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)을 사용 합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 찾거나 [AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)를 사용할 수 있습니다.

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

## <a name="remove-access"></a>액세스 권한 제거

RBAC에서 액세스 권한을 제거하려면 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)를 사용하여 역할 할당을 제거합니다.

다음 예에서는 *pharma-sales-projectforcast sales* 리소스 그룹의 *alain\@example.com* 사용자에서 *가상 컴퓨터 참가자* 역할 할당을 제거 합니다.

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

다음 예에서는 구독 범위에서 < object_id >에서 < role_name > 역할을 제거 합니다.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

다음 예에서는 관리 그룹 범위의 < object_id >에서 < role_name > 역할을 제거 합니다.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

오류 메시지가 표시 되는 경우: "제공 된 정보가 역할 할당에 매핑되지 않습니다." 라는 메시지가 `-Scope` `-ResourceGroupName` 표시 되는지 확인 합니다. 자세한 내용은 [Azure 리소스에 대 한 RBAC 문제 해결](troubleshooting.md#role-assignments-without-a-security-principal)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [자습서: RBAC 및 Azure PowerShell을 사용하여 그룹에 Azure 리소스에 대한 액세스 권한 부여](tutorial-role-assignments-group-powershell.md)
- [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)
- [Azure PowerShell을 사용하여 리소스 관리](../azure-resource-manager/manage-resources-powershell.md)
