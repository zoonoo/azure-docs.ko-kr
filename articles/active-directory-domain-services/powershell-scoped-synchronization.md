---
title: PowerShell을 사용 하 여 Azure AD Domain Services에 대해 범위 동기화 | Microsoft Docs
description: Azure ad PowerShell을 사용 하 여 Azure AD에서 Azure Active Directory Domain Services 관리 되는 도메인으로 범위 동기화를 구성 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: iainfou
ms.openlocfilehash: 197ae37b0c63b19ebe4dcdf2732169be0f357a07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294229"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Azure ad PowerShell을 사용 하 여 Azure AD에서 Azure Active Directory Domain Services로 범위 동기화 구성

인증 서비스를 제공 하기 위해 Azure Active Directory Domain Services (Azure AD DS)는 Azure AD에서 사용자 및 그룹을 동기화 합니다. 하이브리드 환경에서 온-프레미스 Active Directory Domain Services (AD DS) 환경의 사용자와 그룹은 먼저 Azure AD Connect를 사용 하 여 Azure AD에 동기화 한 후 Azure AD DS로 동기화 할 수 있습니다.

기본적으로 Azure AD 디렉터리의 모든 사용자 및 그룹은 Azure AD DS 관리 되는 도메인에 동기화 됩니다. 특정 요구 사항이 있는 경우에는 정의 된 사용자 집합만 동기화 하도록 선택할 수 있습니다.

이 문서에서는 범위 동기화를 사용 하는 관리 되는 도메인을 만든 다음 Azure AD PowerShell을 사용 하 여 범위 지정 된 사용자 집합을 변경 하거나 사용 하지 않도록 설정 하는 방법을 보여 줍니다. [Azure Portal를 사용 하 여 이러한 단계를 완료할][scoped-sync]수도 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료 하 여 [관리 되는 Azure Active Directory Domain Services 도메인을 만들고 구성][tutorial-create-instance]합니다.
* Azure AD DS 동기화 범위를 변경 하려면 Azure AD 테 넌 트에서 *전역 관리자* 권한이 필요 합니다.

## <a name="scoped-synchronization-overview"></a>범위 동기화 개요

기본적으로 Azure AD 디렉터리의 모든 사용자 및 그룹은 관리 되는 도메인에 동기화 됩니다. 사용자가 관리 되는 도메인에만 액세스 해야 하는 경우에는 해당 사용자 계정만 동기화 할 수 있습니다. 이 범위 동기화는 그룹을 기반으로 합니다. 그룹 기반 범위 동기화를 구성 하는 경우 지정한 그룹에 속하는 사용자 계정만 관리 되는 도메인에 동기화 됩니다. 중첩 그룹이 동기화 되지 않고 선택한 특정 그룹만 동기화 됩니다.

관리 되는 도메인을 만들 때 또는 배포 된 후에 동기화 범위를 변경할 수 있습니다. 이제 기존 관리 되는 도메인의 동기화 범위를 다시 만들 필요 없이 변경할 수 있습니다.

동기화 프로세스에 대해 자세히 알아보려면 [Azure AD Domain Services의 동기화 이해][concepts-sync]를 참조 하세요.

> [!WARNING]
> 동기화 범위를 변경 하면 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. 고려 사항은 다음과 같습니다.
>
>  * 관리되는 도메인의 동기화 범위를 변경하면 전체 다시 동기화가 수행됩니다.
>  * 관리 되는 도메인에서 더 이상 필요 하지 않은 개체는 삭제 됩니다. 새 개체는 관리되는 도메인에서 만들어집니다.

## <a name="powershell-script-for-scoped-synchronization"></a>범위가 지정 된 동기화를 위한 PowerShell 스크립트

PowerShell을 사용 하 여 범위 동기화를 구성 하려면 먼저 다음 스크립트를 라는 파일에 저장 `Select-GroupsToSync.ps1` 합니다.

이 스크립트는 azure AD에서 선택한 그룹을 동기화 하도록 Azure AD DS를 구성 합니다. 지정 된 그룹에 속하는 모든 사용자 계정은 관리 되는 도메인에 동기화 됩니다.

이 스크립트는이 문서의 추가 단계에서 사용 됩니다.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>범위 동기화 사용

관리 되는 도메인에 대해 그룹 기반 범위 동기화를 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. 먼저 Azure AD DS 리소스에서 *"filteredSync" = "Enabled"* 를 설정 하 고 관리 되는 도메인을 업데이트 합니다.

    메시지가 표시 되 면 *전역 관리자* 가 [AzureAD][Connect-AzureAD] cmdlet을 사용 하 여 Azure AD 테 넌 트에 로그인 하는 데 사용할 자격 증명을 지정 합니다.

    ```powershell
    // Connect to your Azure AD tenant
    Connect-AzureAD

    // Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    // Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    // Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. 이제 관리 되는 도메인에 해당 사용자를 동기화 해야 하는 그룹 목록을 지정 합니다.

    스크립트를 실행 `Select-GroupsToSync.ps1` 하 고 동기화 할 그룹 목록을 지정 합니다. 다음 예제에서 동기화 할 그룹은 *GroupName1* 및 *GroupName2*입니다.

    > [!WARNING]
    > 범위 동기화에 대 한 그룹 목록에 *AAD DC 관리자* 그룹을 포함 해야 합니다. 이 그룹을 포함 하지 않으면 관리 되는 도메인을 사용할 수 없습니다.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

동기화 범위를 변경 하면 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되며 다시 동기화를 완료 하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="modify-scoped-synchronization"></a>범위 동기화 수정

사용자가 관리 되는 도메인에 동기화 해야 하는 그룹 목록을 수정 하려면 스크립트를 실행 하 `Select-GroupsToSync.ps1` 고 동기화 할 새 그룹 목록을 지정 합니다.

다음 예제에서는 동기화 할 그룹이 더 이상 *GroupName2*를 포함 하지 않으며 이제 *GroupName3*를 포함 합니다.

> [!WARNING]
> 범위 동기화에 대 한 그룹 목록에 *AAD DC 관리자* 그룹을 포함 해야 합니다. 이 그룹을 포함 하지 않으면 관리 되는 도메인을 사용할 수 없습니다.

메시지가 표시 되 면 *전역 관리자* 가 [AzureAD][Connect-AzureAD] cmdlet을 사용 하 여 Azure AD 테 넌 트에 로그인 하는 데 사용할 자격 증명을 지정 합니다.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

동기화 범위를 변경 하면 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되며 다시 동기화를 완료 하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="disable-scoped-synchronization"></a>범위 동기화 사용 안 함

관리 되는 도메인에 대 한 그룹 기반 범위 동기화를 사용 하지 않도록 설정 하려면 Azure AD DS 리소스에서 *"filteredSync" = "Disabled"* 를 설정 하 고 관리 되는 도메인을 업데이트 합니다. 완료 되 면 모든 사용자 및 그룹이 Azure AD에서 동기화 되도록 설정 됩니다.

메시지가 표시 되 면 *전역 관리자* 가 [AzureAD][Connect-AzureAD] cmdlet을 사용 하 여 Azure AD 테 넌 트에 로그인 하는 데 사용할 자격 증명을 지정 합니다.

```powershell
// Connect to your Azure AD tenant
Connect-AzureAD

// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

동기화 범위를 변경 하면 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되며 다시 동기화를 완료 하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

동기화 프로세스에 대해 자세히 알아보려면 [Azure AD Domain Services의 동기화 이해](synchronization.md)를 참조 하세요.

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
