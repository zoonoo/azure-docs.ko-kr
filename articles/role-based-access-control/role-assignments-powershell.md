---
title: RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 액세스 관리 | Microsoft Docs
description: 사용자, 그룹 및 역할 기반 access control (RBAC) 및 Azure PowerShell을 사용 하 여 응용 프로그램에 대 한 Azure 리소스에 대 한 액세스를 관리 하는 방법에 알아봅니다. 여기에는 액세스 권한을 나열, 부여 및 제거하는 방법이 포함됩니다.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ace70dea5277b4e9d8c8b0e6e0cdc49da0b674e0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432229"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 RBAC 및 Azure PowerShell을 사용하여 사용자, 그룹 및 애플리케이션의 액세스 권한을 관리하는 방법을 설명합니다.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

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
Get-AzRoleDefinition <role name>
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

### <a name="list-a-role-definition-in-json-format"></a>JSON 형식으로 역할 정의 나열

JSON 형식으로 역할 정의 나열 하려면 사용 하 여 [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)합니다.

```azurepowershell
Get-AzRoleDefinition <role name> | ConvertTo-Json
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
Get-AzRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role name>).Actions
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

### <a name="list-role-assignments-at-a-specific-scope"></a>특정 범위의 역할 할당 나열

지정된 구독, 리소스 그룹 또는 리소스에 대한 모든 역할 할당을 확인할 수 있습니다. 예를 들어 리소스 그룹에 대한 모든 활성 할당을 확인하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

지정된 사용자에 할당된 모든 역할을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -SignInName <email, userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

지정된 사용자에게 할당된 역할 및 사용자가 속한 그룹에 할당된 역할을 모두 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -SignInName <email, userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>클래식 서비스 관리자 및 공동 관리자에 대한 역할 할당 나열

클래식 구독 관리자 및 공동 관리자에 대한 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>액세스 권한 부여

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다.

### <a name="search-for-object-ids"></a>개체 ID 검색

역할을 할당하려면 개체(사용자, 그룹 또는 애플리케이션)와 범위 둘 다를 식별해야 합니다.

구독 ID를 모르는 경우 Azure Portal의 **구독** 블레이드에서 확인하거나 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)을 사용할 수 있습니다.

Azure AD 그룹에 대한 개체 ID를 가져오려면 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)을 사용합니다.

```azurepowershell
Get-AzADGroup -SearchString <group name in quotes>
```

Azure AD 서비스 주체 또는 애플리케이션에 대한 개체 ID를 가져오려면 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)을 사용합니다.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service name in quotes>
```

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>구독 범위에서 애플리케이션에 대한 역할 할당 만들기

구독 범위에서 애플리케이션에 액세스 권한을 부여하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다.

```azurepowershell
New-AzRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope /subscriptions/<subscription id>
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

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 대한 역할 할당 만들기

리소스 그룹 범위에서 사용자에 대한 액세스 권한을 부여하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다.

```azurepowershell
New-AzRoleAssignment -SignInName <email, userprincipalname> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>리소스 범위에서 그룹에 대한 역할 할당 만들기

리소스 범위에서 그룹에 대한 액세스 권한을 부여하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다.

```azurepowershell
New-AzRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
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

## <a name="remove-access"></a>액세스 권한 제거

RBAC에서 액세스 권한을 제거하려면 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)를 사용하여 역할 할당을 제거합니다.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="next-steps"></a>다음 단계

- [자습서: RBAC 및 Azure PowerShell을 사용하여 그룹에 Azure 리소스에 대한 액세스 권한 부여](tutorial-role-assignments-group-powershell.md)
- [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)
- [Azure PowerShell을 사용하여 리소스 관리](../azure-resource-manager/manage-resources-powershell.md)
