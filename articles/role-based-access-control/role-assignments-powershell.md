---
title: Azure PowerShell를 사용 하 여 Azure 역할 할당 추가 또는 제거-Azure RBAC
description: Azure PowerShell 및 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 대 한 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
ms.openlocfilehash: 6eadba7d778a632896529f3c7c53886619d96444
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901473"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 역할 할당 추가 또는 제거

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 이 문서에서는 Azure PowerShell를 사용 하 여 역할을 할당 하는 방법을 설명 합니다.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

역할 할당을 추가 하거나 제거 하려면 다음을 수행 해야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))
- Azure Cloud Shell 또는 [Azure PowerShell](/powershell/azure/install-az-ps) [의 PowerShell](../cloud-shell/overview.md)
- PowerShell 명령을 실행 하는 데 사용 하는 계정에는 Microsoft Graph `Directory.Read.All` 권한이 있어야 합니다.

## <a name="steps-to-add-a-role-assignment"></a>역할 할당을 추가하는 단계

Azure RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 추가합니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다. 역할 할당을 추가 하려면 다음 단계를 수행 합니다.

### <a name="step-1-determine-who-needs-access"></a>1 단계: 액세스 해야 하는 사용자 확인

사용자, 그룹, 서비스 주체 또는 관리 id에 역할을 할당할 수 있습니다. 역할 할당을 추가 하려면 개체의 고유 ID를 지정 해야 할 수 있습니다. ID의 형식은 `11111111-1111-1111-1111-111111111111`입니다. Azure Portal 또는 Azure PowerShell를 사용 하 여 ID를 가져올 수 있습니다.

**사용자**

Azure AD 사용자의 경우 *patlong \@ contoso.com* 또는 사용자 개체 ID와 같은 사용자 계정 이름을 가져옵니다. 개체 ID를 가져오려면 [AzADUser](/powershell/module/az.resources/get-azaduser)를 사용할 수 있습니다.

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**그룹**

Azure AD 그룹의 경우 그룹 개체 ID가 필요 합니다. 개체 ID를 가져오려면 [AzADGroup](/powershell/module/az.resources/get-azadgroup)를 사용할 수 있습니다.

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**서비스 주체**

Azure AD 서비스 사용자 (응용 프로그램에서 사용 하는 id)의 경우 서비스 주체 개체 ID가 필요 합니다. 개체 ID를 가져오려면 [AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)를 사용할 수 있습니다. 서비스 주체의 경우 애플리케이션 ID가 **아니라** 개체 ID를 사용합니다.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**관리 ID**

시스템 할당 또는 사용자 할당 관리 id의 경우 개체 ID가 필요 합니다. 개체 ID를 가져오려면 [AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)를 사용할 수 있습니다.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-find-the-appropriate-role"></a>2 단계: 적절 한 역할 찾기

사용 권한은 역할에 함께 그룹화 됩니다. 여러 [Azure 기본 제공 역할](built-in-roles.md) 목록에서 선택 하거나 고유한 사용자 지정 역할을 사용할 수 있습니다. 필요한 최소한의 권한을 사용 하 여 액세스 권한을 부여 하는 것이 가장 좋습니다. 따라서 더 광범위 한 역할을 할당 하지 마십시오.

역할을 나열 하 고 고유한 역할 ID를 얻으려면 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)를 사용 하면 됩니다.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

특정 역할에 대 한 세부 정보를 나열 하는 방법은 다음과 같습니다.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

자세한 내용은 [Azure 역할 정의 나열](role-definitions-list.md#azure-powershell)을 참조 하세요.
 
### <a name="step-3-identify-the-needed-scope"></a>3 단계: 필요한 범위 확인

Azure는 리소스, [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups), 구독 및 [관리 그룹](../governance/management-groups/overview.md)의 네 가지 범위 수준을 제공 합니다. 필요한 최소 권한을 사용 하 여 액세스 권한을 부여 하는 것이 가장 좋습니다. 따라서 더 광범위 한 범위에서 역할을 할당 하지 마십시오. 범위에 대한 자세한 내용은 [범위 이해](scope-overview.md)를 참조하세요.

**리소스 범위**

리소스 범위의 경우 리소스의 리소스 ID가 필요 합니다. Azure Portal 리소스의 속성을 살펴보면 리소스 ID를 찾을 수 있습니다. 리소스 ID의 형식은 다음과 같습니다.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**리소스 그룹 범위**

리소스 그룹 범위에는 리소스 그룹의 이름이 필요 합니다. Azure Portal의 **리소스 그룹** 페이지에서 이름을 찾거나 [AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)를 사용할 수 있습니다.

```azurepowershell
Get-AzResourceGroup
```

**구독 범위** 

구독 범위에는 구독 ID가 필요 합니다. Azure Portal의 **구독** 페이지에서 ID를 찾거나 [AzSubscription](/powershell/module/az.accounts/get-azsubscription)를 사용할 수 있습니다.

```azurepowershell
Get-AzSubscription
```

**관리 그룹 범위** 

관리 그룹 범위에는 관리 그룹 이름이 필요 합니다. Azure Portal의 **관리 그룹** 페이지에서 이름을 찾거나 [AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)를 사용할 수 있습니다.

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-add-role-assignment"></a>4 단계: 역할 할당 추가

역할 할당을 추가 하려면 [AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 명령을 사용 합니다. 범위에 따라 명령은 일반적으로 다음 형식 중 하나를 갖습니다.

**리소스 범위**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**리소스 그룹 범위**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**구독 범위** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**관리 그룹 범위** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="add-role-assignment-examples"></a>역할 할당 예제 추가

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>저장소 계정 리소스 범위에서 모든 blob 컨테이너에 대 한 역할 할당 추가

*Storage12345* 라는 저장소 계정에 대 한 리소스 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 사용자에 게 [저장소 Blob 데이터 참가자](built-in-roles.md#storage-blob-data-contributor) 역할을 할당 합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>특정 blob 컨테이너 리소스 범위에 대 한 역할 할당 추가

Blob 컨테이너 *-01* 이라는 blob 컨테이너에 대 한 리소스 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 사용자에 게 [저장소 Blob 데이터 참가자](built-in-roles.md#storage-blob-data-contributor) 역할을 할당 합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>특정 가상 네트워크 리소스 범위에서 그룹에 대 한 역할 할당 추가

*Pharma-sales-projectforcast* 이라는 가상 네트워크에 대 한 리소스 범위에서 ID가 Aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa 인 *pharma-sales-projectforcast Sales Admins* 그룹에 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="add-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 대 한 역할 할당 추가

*Pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 *patlong \@ Contoso.com* user에 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

또는 매개 변수를 사용 하 여 정규화 된 리소스 그룹을 지정할 수 있습니다 `-Scope` .

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>리소스 그룹 범위에서 고유한 역할 ID를 사용 하 여 사용자에 대 한 역할 할당 추가

역할 이름이 변경 될 수 있는 몇 가지 경우가 있습니다. 예를 들면 다음과 같습니다.

- 사용자 고유의 사용자 지정 역할을 사용 하 고 있으며 이름을 변경 하기로 결정 했습니다.
- 이름에 **(미리 보기)** 가 있는 미리 보기 역할을 사용 하 고 있습니다. 역할을 해제 하면 역할의 이름이 바뀝니다.

역할의 이름이 바뀐 경우에도 역할 ID는 변경 되지 않습니다. 스크립트 또는 자동화를 사용 하 여 역할 할당을 만드는 경우 역할 이름 대신 고유한 역할 ID를 사용 하는 것이 좋습니다. 따라서 역할의 이름을 바꾸면 스크립트가 작동할 가능성이 높습니다.

다음 예에서는 *pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 *patlong \@ Contoso.com* 사용자에 게 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>리소스 그룹 범위에서 응용 프로그램에 대 한 역할 할당 추가

*Pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 서비스 사용자 개체 ID가 77777777-7777-7777-7777-777777777777 인 응용 프로그램에 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>구독 범위에서 사용자에 대 한 역할 할당 추가

구독 범위에서 *\@ example.com* 사용자에 게 [읽기 권한자](built-in-roles.md#reader) 역할을 할당 합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>관리 그룹 범위에서 사용자에 대 한 역할 할당 추가

관리 그룹 범위에서 *alain \@ example.com* 사용자에 게 [청구 읽기 권한자](built-in-roles.md#billing-reader) 역할을 할당 합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

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

Azure RBAC에서 액세스 권한을 제거 하려면 [AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)를 사용 하 여 역할 할당을 제거 합니다.

다음 예에서는 *pharma-sales-projectforcast sales* 리소스 그룹의 *patlong \@ Contoso.com* 사용자에서 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할 할당을 제거 합니다.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

구독 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann Mack 팀* 그룹에서 [판독기](built-in-roles.md#reader) 역할을 제거 합니다.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

관리 그룹 범위의 *alain \@ example.com* 사용자에서 [청구 읽기 권한자](built-in-roles.md#billing-reader) 역할을 제거 합니다.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

"제공 된 정보가 역할 할당에 매핑되지 않습니다." 라는 오류 메시지가 표시 되 면 `-Scope` 또는 매개 변수도 지정 해야 합니다 `-ResourceGroupName` . 자세한 내용은 [AZURE RBAC 문제 해결](troubleshooting.md#role-assignments-with-identity-not-found)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell를 사용 하 여 Azure 역할 할당 나열](role-assignments-list-powershell.md)
- [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 그룹 액세스 권한 부여](tutorial-role-assignments-group-powershell.md)
- [Azure PowerShell을 사용하여 리소스 관리](../azure-resource-manager/management/manage-resources-powershell.md)
