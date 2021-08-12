---
title: Azure AD Domain Services에서 PowerShell을 사용하여 범위가 지정된 동기화 | Microsoft Docs
description: Azure AD PowerShell을 사용하여 Azure AD에서 Azure Active Directory Domain Services 관리되는 도메인으로 범위가 지정된 동기화를 구성하는 방법 알아보기
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 29df4e71d0df5a2770474294789721dfd3b28bc8
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108285721"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용하여 Azure AD에서 Azure Active Directory Domain Services로 범위가 지정된 동기화 구성

인증 서비스를 제공하기 위해 Azure AD DS(Azure Active Directory Domain Services)는 Azure AD에서 사용자 및 그룹을 동기화합니다. 하이브리드 환경에서 온-프레미스 AD DS(Active Directory Domain Services) 환경의 사용자 및 그룹을 먼저 Azure AD Connect를 사용하여 Azure AD에 동기화한 다음, Azure AD DS에 동기화할 수 있습니다.

기본적으로 Azure AD 디렉터리 내의 모든 사용자와 그룹이 Azure AD DS 관리되는 도메인에 동기화됩니다. 특정 요구 사항이 있는 경우 정의된 사용자 집합만 동기화하도록 선택할 수 있습니다.

이 문서에서는 범위가 지정된 동기화를 사용하는 관리되는 도메인을 만든 다음, Azure AD PowerShell을 사용하여 범위가 지정된 사용자 집합을 변경하거나 사용하지 않도록 설정하는 방법을 보여줍니다. [Azure Portal을 사용하여 이러한 단계를 완료][scoped-sync]할 수도 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성][tutorial-create-instance]하려면 자습서를 완료합니다.
* Azure AD DS 동기화 범위를 변경하려면 Azure AD 테넌트에서 *전역 관리자* 권한이 필요합니다.

## <a name="scoped-synchronization-overview"></a>범위가 지정된 동기화 개요

기본적으로 Azure AD 디렉터리 내의 모든 사용자와 그룹이 관리되는 도메인에 동기화됩니다. 소수의 사용자만 관리되는 도메인에 액세스해야 하는 경우 해당 사용자 계정만 동기화할 수 있습니다. 이 범위 지정 동기화는 그룹 기반입니다. 그룹 기반 범위 지정 동기화를 구성할 때 지정한 그룹에 속한 사용자 계정만 관리되는 도메인에 동기화됩니다. 중첩된 그룹은 동기화되지 않으며 선택한 특정 그룹만 동기화됩니다.

관리되는 도메인을 만들기 전이나 후 언제든 동기화 범위를 변경할 수 있습니다. 동기화 범위는 응용 프로그램 식별자가 2565bd9d-da50-47d4-8b85-4c97f669dc36인 서비스 주체에 의해 정의됩니다. 범위 손실을 방지하려면 서비스 주체를 삭제하거나 변경하지 마세요. 실수로 삭제된 경우 동기화 범위를 복구할 수 없습니다. 

동기화 범위를 변경하는 경우 다음과 같은 주의 사항이 있습니다.

- 전체 동기화가 발생합니다.
- 관리되는 도메인에 더 이상 필요하지 않은 개체는 삭제됩니다. 새 개체는 관리되는 도메인에서 만들어집니다.

동기화 프로세스에 대한 자세한 내용은 [Azure AD Domain Services 동기화 이해][concepts-sync]를 참조하세요.

## <a name="powershell-script-for-scoped-synchronization"></a>범위가 지정된 동기화를 위한 PowerShell 스크립트

PowerShell을 사용하여 범위가 지정된 동기화를 구성하려면 먼저 다음 스크립트를 `Select-GroupsToSync.ps1`이라는 파일에 저장합니다.

이 스크립트는 Azure AD에서 선택한 그룹을 동기화하도록 Azure AD DS를 구성합니다. 지정된 그룹에 속하는 모든 사용자 계정은 관리되는 도메인에 동기화됩니다.

이 스크립트는 이 문서의 추가 단계에서 사용됩니다.

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

## <a name="enable-scoped-synchronization"></a>범위가 지정된 동기화 사용

관리되는 도메인에 그룹 기반 범위가 지정된 동기화를 사용하도록 설정하려면 다음 단계를 완료합니다.

1. 먼저 Azure AD DS 리소스에서 *"filteredSync" = "Enabled"* 를 설정한 다음, 관리되는 도메인을 업데이트합니다.

    메시지가 표시되면 [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 로그인할 전역 관리자의 자격 증명을 지정합니다.

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. 관리되는 도메인에 동기화되어야 하는 사용자가 속한 그룹 목록을 지정합니다.

    `Select-GroupsToSync.ps1` 스크립트를 실행하고 동기화할 그룹 목록을 지정합니다. 다음 예에서 동기화할 그룹은 *GroupName1* 및 *GroupName2* 입니다.

    > [!WARNING]
    > 범위가 지정된 동기화를 위한 그룹 목록에 *AAD DC* 관리자 그룹을 포함해야 합니다. 이 그룹을 포함하지 않으면 관리되는 도메인을 사용할 수 없습니다.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

동기화 범위를 변경하면 관리되는 도메인에서 모든 데이터를 다시 동기화합니다. 관리되는 도메인에서 더 이상 필요하지 않은 개체가 삭제되며 다시 동기화를 완료하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="modify-scoped-synchronization"></a>범위 지정 동기화 수정

관리되는 도메인에 동기화되어야 하는 사용자가 속한 그룹 목록을 수정하려면 `Select-GroupsToSync.ps1` 스크립트를 실행하고 동기화할 새 그룹 목록을 지정합니다.

다음 예에서 동기화할 그룹에 *GroupName2* 가 더 이상 포함되지 않고 이제는 *GroupName3* 이 포함됩니다.

> [!WARNING]
> 범위가 지정된 동기화를 위한 그룹 목록에 *AAD DC* 관리자 그룹을 포함해야 합니다. 이 그룹을 포함하지 않으면 관리되는 도메인을 사용할 수 없습니다.

메시지가 표시되면 [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 로그인할 전역 관리자의 자격 증명을 지정합니다.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

동기화 범위를 변경하면 관리되는 도메인에서 모든 데이터를 다시 동기화합니다. 관리되는 도메인에서 더 이상 필요하지 않은 개체가 삭제되며 다시 동기화를 완료하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="disable-scoped-synchronization"></a>범위가 지정된 동기화 해제

관리되는 도메인에 대한 그룹 기반 범위가 지정된 동기화를 사용하지 않도록 설정하려면 Azure AD DS 리소스에서 *"filteredSync" = "Disabled"* 를 설정한 다음, 관리되는 도메인을 업데이트합니다. 완료되면 모든 사용자 및 그룹이 Azure AD에서 동기화되도록 설정됩니다.

메시지가 표시되면 [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 로그인할 전역 관리자의 자격 증명을 지정합니다.

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

동기화 범위를 변경하면 관리되는 도메인에서 모든 데이터를 다시 동기화합니다. 관리되는 도메인에서 더 이상 필요하지 않은 개체가 삭제되며 다시 동기화를 완료하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

동기화 프로세스에 대한 자세한 내용은 [Azure AD Domain Services 동기화 이해](synchronization.md)를 참조하세요.

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
