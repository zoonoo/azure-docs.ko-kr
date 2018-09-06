---
title: 자습서 - RBAC 및 Azure PowerShell을 사용하여 그룹에 액세스 권한 부여 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어)를 사용하여 그룹에 Azure PowerShell을 사용하여 구독의 모든 항목을 살펴보고 리소스 그룹의 모든 항목을 관리할 수 있는 액세스 권한을 부여할 수 있습니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 8bb06493683dabb92dfe75f371f96db14a7951b3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301006"
---
# <a name="tutorial-grant-access-for-a-group-using-rbac-and-azure-powershell"></a>자습서: RBAC 및 Azure PowerShell을 사용하여 그룹에 액세스 권한 부여

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 이 자습서에서는 Azure PowerShell을 사용하여 그룹에 구독의 모든 항목을 살펴보고 리소스 그룹의 모든 항목을 관리할 수 있는 액세스 권한을 부여합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 다양한 범위에서 그룹에 액세스 권한 부여
> * 액세스 권한 나열
> * 액세스 권한 제거

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Azure Active Directory에서 그룹을 만드는(또는 기존 그룹을 사용하는) 권한
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>역할 할당

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다. 이 자습서에서는 다음과 같은 두 가지 역할 할당을 수행할 것입니다.

| 보안 주체 | 역할 정의 | 범위 |
| --- | --- | --- |
| 그룹<br>(RBAC 자습서 그룹) | [판독기](built-in-roles.md#reader) | 구독 |
| 그룹<br>(RBAC 자습서 그룹)| [기여자](built-in-roles.md#contributor) | 리소스 그룹<br>(rbac-tutorial-resource-group) |

   ![그룹에 대한 역할 할당](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>그룹 만들기

역할을 할당하려면 사용자, 그룹 또는 서비스 주체가 필요합니다. 아직 그룹이 없는 경우 새로 만들면 됩니다.

- Azure Cloud Shell에서 [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) 명령을 사용하여 새 그룹을 만듭니다.

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

그룹을 만들 권한이 없는 경우 [자습서: RBAC 및 Azure PowerShell을 사용하여 사용자에게 액세스 권한 부여](tutorial-role-assignments-user-powershell.md)를 시도해 보세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 사용하여 리소스 그룹 범위에서 역할을 할당하는 방법을 보여줍니다.

1. [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) 명령을 사용하여 영역 위치 목록을 가져옵니다.

   ```azurepowershell
   Get-AzureRmLocation | select Location
   ```

1. 가까운 위치를 선택하고 변수에 할당합니다.

   ```azurepowershell
   $location = "westus"
   ```

1. [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령을 사용하여 새 리소스 그룹을 만듭니다.

   ```azurepowershell
   New-AzureRmResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>액세스 권한 부여

그룹에 액세스 권한을 부여하려면 [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) 명령을 사용하여 역할을 할당합니다. 보안 주체, 역할 정의 및 범위를 지정해야 합니다.

1. [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup) 명령을 사용하여 그룹의 개체 ID를 가져옵니다.

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. 그룹 개체 ID를 변수에 저장합니다.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) 명령을 사용하여 구독 ID를 가져옵니다.

    ```azurepowershell
    Get-AzureRmSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. 구독 범위를 변수에 저장합니다.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. 구독 범위에서 그룹에 [읽기 권한자](built-in-roles.md#reader) 역할을 할당합니다.

    ```azurepowershell
    New-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. 리소스 그룹 범위에서 그룹에 [기여자](built-in-roles.md#contributor) 역할을 할당합니다.

    ```azurepowershell
    New-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>액세스 권한 나열

1. 구독에 대한 액세스 권한을 확인하려면 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) 명령을 사용하여 역할 할당을 나열합니다.

    ```azurepowershell
    Get-AzureRmRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    출력을 보면 구독 범위에서 읽기 권한자 역할이 RBAC 자습서 그룹에 할당된 것을 볼 수 있습니다.

1. 리소스 그룹에 대한 액세스 권한을 확인하려면 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) 명령을 사용하여 역할 할당을 나열합니다.

    ```azurepowershell
    Get-AzureRmRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    출력을 보면 RBAC 자습서 그룹에 기여자 및 읽기 권한자 역할이 모두 할당된 것을 볼 수 있습니다. 기여자 역할은 rbac-tutorial-resource-group 범위이고 읽기 권한자 역할은 구독 범위입니다.

## <a name="optional-list-access-using-the-azure-portal"></a>(선택 사항) Azure Portal을 사용하여 액세스 권한 나열

1. Azure Portal에서 역할 할당이 어떻게 보이는지 확인하려면 구독의 **액세스 제어(IAM)** 블레이드를 살펴봅니다.

    ![구독 범위에서 그룹의 역할 할당](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. 리소스 그룹의 **액세스 제어(IAM)** 블레이드를 살펴봅니다.

    ![리소스 그룹에서 그룹의 역할 할당](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>액세스 권한 제거

사용자, 그룹 및 응용 프로그램의 액세스 권한을 제거하려면 [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) 명령을 사용하여 역할 할당을 제거합니다.

1. 다음 명령을 사용하여 리소스 그룹 범위에서 그룹의 기여자 역할 할당을 제거합니다.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. 다음 명령을 사용하여 구독 범위에서 그룹의 읽기 권한자 역할 할당을 제거합니다.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 리소스 그룹 및 그룹을 삭제합니다.

1. [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹을 제거합니다.

    ```azurepowershell
    Remove-AzureRmResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. 확인을 묻는 메시지가 나타나면 **Y**를 선택합니다. 삭제에 몇 초 정도 걸립니다.

1. [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup) 명령을 사용하여 그룹을 삭제합니다.

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    그룹을 삭제하려고 할 때 오류가 발생하면 포털에서 그룹을 삭제해도 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [RBAC 및 PowerShell을 사용하여 액세스 관리](role-assignments-powershell.md)
