---
title: Azure AD 도메인 서비스에 대한 범위 동기화 | 마이크로 소프트 문서
description: Azure AD에서 Azure Active Directory 도메인 서비스 관리 도메인으로 범위 별 동기화를 구성하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 9ef7e14cc2a290cc5583e3e599e278f98882152c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654732"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Azure AD에서 Azure Active Directory 도메인 서비스로 범위별 동기화 구성

인증 서비스를 제공하기 위해 Azure Active Directory 도메인 서비스(Azure AD DS)는 Azure AD에서 사용자 및 그룹을 동기화합니다. 하이브리드 환경에서 는 온-프레미스 Active Directory 도메인 서비스(AD DS) 환경의 사용자 및 그룹을 Azure AD Connect를 사용하여 Azure AD에 먼저 동기화한 다음 Azure AD DS에 동기화할 수 있습니다.

기본적으로 Azure AD 디렉터리에서 모든 사용자 및 그룹은 Azure AD DS 관리 도메인에 동기화됩니다. 특정 요구 사항이 있는 경우 대신 정의된 사용자 집합만 동기화하도록 선택할 수 있습니다.

이 문서에서는 범위 별 동기화를 사용하는 Azure AD DS 관리 도메인을 만든 다음 범위가 지정된 사용자 집합을 변경하거나 사용하지 않도록 설정하는 방법을 보여 주십니다.

## <a name="scoped-synchronization-overview"></a>범위 별 동기화 개요

기본적으로 Azure AD 디렉터리에서 모든 사용자 및 그룹은 Azure AD DS 관리 도메인에 동기화됩니다. 소수의 사용자만 관리되는 도메인에 액세스해야 하는 경우 해당 사용자 계정만 동기화할 수 있습니다. 이 범위 동기화는 그룹 기반입니다. 그룹 기반 범위 의 동기화를 구성할 때 지정한 그룹에 속한 사용자 계정만 Azure AD DS 관리 도메인에 동기화됩니다.

다음 표에서는 범위 지정 동기화를 사용하는 방법을 설명합니다.

| 현재 상태 | 필요한 상태 | 필요한 구성 |
| --- | --- | --- |
| 기존 관리되는 도메인은 모든 사용자 계정 및 그룹을 동기화하도록 구성됩니다. | 특정 그룹에 속한 사용자 계정만 동기화하려고 합니다. | 모든 사용자 동기화에서 범위 별 동기화를 사용하는 것으로 변경할 수 없습니다. [기존 관리되는 도메인을 삭제한](delete-aadds.md)다음 이 문서의 단계를 수행하여 범위가 설정된 동기화가 구성된 Azure AD DS 관리 도메인을 다시 만듭니다. |
| 기존 관리되는 도메인이 없습니다. | 새 관리되는 도메인을 만들고 특정 그룹에 속한 사용자 계정만 동기화하려고 합니다. | 이 문서의 단계에 따라 범위가 설정된 동기화가 구성된 Azure AD DS 관리 도메인을 만듭니다. |
| 기존 관리 되는 도메인은 특정 그룹에 속한 계정만 동기화 하도록 구성 됩니다. | Azure AD DS 관리 도메인에 동기화해야 하는 그룹 목록을 수정하려고 합니다. | 범위 별 동기화를 수정하려면 이 문서의 단계를 따릅니다. |

Azure 포털 또는 PowerShell을 사용하여 범위별 동기화 설정을 구성합니다.

| 작업 | | |
|--|--|--|
| Azure AD DS 관리 도메인 만들기 및 범위 별 동기화 구성 | [Azure portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| 범위 별 동기화 수정 | [Azure portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| 범위 지정 동기화 사용 안 함 | [Azure portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> 동기화 범위를 변경하면 Azure AD DS 관리 도메인이 모든 데이터를 다시 동기화합니다. 고려 사항은 다음과 같습니다.
> 
>  * Azure AD DS 관리 도메인의 동기화 범위를 변경하면 전체 재동기화가 발생합니다.
>  * Azure AD DS 관리 도메인에 더 이상 필요하지 않은 개체가 삭제됩니다. 새 개체는 관리되는 도메인에서 만들어집니다.
>  * 다시 동기화를 완료하는 데 시간이 오래 걸릴 수 있습니다. 동기화 시간은 Azure AD DS 관리 도메인 및 Azure AD 디렉터리에서 사용자, 그룹 및 그룹 구성원 자격과 같은 개체 수에 따라 달라집니다. 수십만 개의 많은 개체가 있는 큰 디렉터리의 경우 다시 동기화에 며칠이 걸릴 수 있습니다.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Azure 포털을 사용하여 범위 별 동기화 를 활성화합니다.

Azure 포털에서 범위 지정 동기화를 사용하려면 다음 단계를 완료합니다.

1. [자습서를 수행하여 Azure AD DS 인스턴스를 만들고 구성합니다.](tutorial-create-instance-advanced.md) 동기화 범위를 제외한 모든 필수 구성 및 배포 단계를 완료합니다.
1. 동기화 단계에서 **범위를** 선택한 다음 Azure AD 그룹을 선택하여 Azure AD DS 인스턴스와 동기화합니다.

Azure AD DS 관리 도메인은 배포를 완료하는 데 최대 1시간이 걸릴 수 있습니다. Azure 포털에서 Azure AD DS 관리 도메인의 **개요** 페이지에는 이 배포 단계 전체의 현재 상태가 표시됩니다.

Azure 포털에서 Azure AD DS 관리 도메인이 프로비저닝을 완료했다는 표시가 표시되면 다음 작업을 완료해야 합니다.

* 가상 머신이 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 DNS 설정을 업데이트합니다.
    * DNS를 구성하려면 포털에서 Azure AD DS 관리 도메인을 선택합니다. **개요** 창에서 이러한 DNS 설정을 자동으로 구성하라는 메시지가 표시됩니다.
* 최종 사용자가 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있도록 [Azure AD 도메인 서비스에 대한 암호 동기화를 사용하도록 설정합니다.](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds)

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Azure 포털을 사용하여 범위 별 동기화 수정

Azure AD DS 관리 도메인에 사용자를 동기화해야 하는 그룹 목록을 수정하려면 다음 단계를 완료하십시오.

1. Azure 포털에서 **Azure AD 도메인 서비스를**검색하고 선택합니다. *aaddscontoso.com*.
1. 왼쪽의 메뉴에서 **동기화를** 선택합니다.
1. 그룹을 추가하려면 상단에서 **+ 그룹 선택을** 선택한 다음 추가할 그룹을 선택합니다.
1. 동기화 범위에서 그룹을 제거하려면 현재 동기화된 그룹 목록에서 그룹을 선택하고 그룹 **제거를**선택합니다.
1. 모든 변경이 완료되면 **동기화 범위 저장을**선택합니다.

동기화 범위를 변경하면 Azure AD DS 관리 도메인이 모든 데이터를 다시 동기화합니다. Azure AD DS 관리 도메인에 더 이상 필요하지 않은 개체는 삭제되며 다시 동기화를 완료하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Azure 포털을 사용하여 범위 지정 동기화 를 사용하지 않도록 설정

Azure AD DS 관리 도메인에 대한 그룹 기반 범위 동기화를 사용하지 않도록 설정하려면 다음 단계를 완료합니다.

1. Azure 포털에서 **Azure AD 도메인 서비스를**검색하고 선택합니다. *aaddscontoso.com*.
1. 왼쪽의 메뉴에서 **동기화를** 선택합니다.
1. 동기화 범위를 **Scoped에서** **All로**설정한 다음 **동기화 범위 저장을**선택합니다.

동기화 범위를 변경하면 Azure AD DS 관리 도메인이 모든 데이터를 다시 동기화합니다. Azure AD DS 관리 도메인에 더 이상 필요하지 않은 개체는 삭제되며 다시 동기화를 완료하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="powershell-script-for-scoped-synchronization"></a>범위 동기화를 위한 PowerShell 스크립트

PowerShell을 사용하여 범위 별 동기화를 구성하려면 먼저 다음 `Select-GroupsToSync.ps1`스크립트를 라는 파일에 저장합니다. 이 스크립트는 Azure AD DS에서 선택한 그룹을 동기화하도록 Azure AD DS를 구성합니다. 지정된 그룹의 일부인 모든 사용자 계정은 Azure AD DS 관리 도메인에 동기화됩니다.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>PowerShell을 사용하여 범위 별 동기화 활성화

PowerShell을 사용하여 다음 단계를 완료합니다. [PowerShell을 사용하여 Azure Active Directory Domain Services를 사용](powershell-create-instance.md)하도록 설정하려면 지침을 참조하세요. 이 문서의 몇 단계는 범위 동기화를 구성하기 위해 약간 수정되었습니다.

1. PowerShell을 사용하여 Azure AD DS를 사용하도록 설정하려면 문서에서 다음 작업을 완료합니다. 실제로 관리되는 도메인을 만드는 단계에서 중지합니다. Azure AD DS 관리 도메인을 만드는 범위 별 동기화를 구성합니다.

   * [필요한 PowerShell 모듈을 설치합니다.](powershell-create-instance.md#prerequisites)
   * [관리 액세스에 필요한 서비스 주체 및 Azure AD 그룹을 만듭니다.](powershell-create-instance.md#create-required-azure-ad-resources)
   * [가상 네트워크 및 서브넷과 같은 지원 Azure 리소스를 만듭니다.](powershell-create-instance.md#create-supporting-azure-resources)

1. Azure AD에서 동기화할 그룹에 포함된 사용자를 결정합니다. Azure AD DS에 동기화할 그룹의 표시 이름 목록을 만듭니다.

1. 이전 [섹션에서 스크립트를](#powershell-script-for-scoped-synchronization) 실행 하 고 사용 하 여 *-groupsToAdd* 매개 변수 를 사용 하 여 동기화 하는 그룹 목록을 전달 합니다.

   > [!WARNING]
   > 범위 동기화를 위한 그룹 목록에 *AAD DC Administrators* 그룹을 포함해야 합니다. 이 그룹을 포함하지 않으면 Azure AD DS 관리 도메인을 사용할 수 없습니다.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. 이제 Azure AD DS 관리 도메인을 만들고 그룹 기반 범위 동기화를 사용하도록 설정합니다. *-Properties* 매개 변수에 *"필터링된 Sync" = "사용"을* 포함합니다.

    Azure 구독 ID를 설정한 다음 *aaddscontoso.com*. [Get-AzSubscription][Get-AzSubscription] cmdlet을 사용하여 구독 ID를 얻을 수 있습니다. 리소스 그룹 이름, 가상 네트워크 이름 및 지역을 이전 단계에서 지원 Azure 리소스를 만드는 데 사용된 값으로 설정합니다.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

리소스를 만들고 PowerShell 프롬프트에 제어를 반환하는 데 몇 분 정도 걸립니다. Azure AD DS 관리 도메인은 백그라운드에서 계속 프로비전되며 배포를 완료하는 데 최대 1시간이 걸릴 수 있습니다. Azure 포털에서 Azure AD DS 관리 도메인의 **개요** 페이지에는 이 배포 단계 전체의 현재 상태가 표시됩니다.

Azure 포털에서 Azure AD DS 관리 도메인이 프로비저닝을 완료했다는 표시가 표시되면 다음 작업을 완료해야 합니다.

* 가상 머신이 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 DNS 설정을 업데이트합니다.
    * DNS를 구성하려면 포털에서 Azure AD DS 관리 도메인을 선택합니다. **개요** 창에서 이러한 DNS 설정을 자동으로 구성하라는 메시지가 표시됩니다.
* 가용성 영역을 지원하는 리전에서 Azure AD DS 관리 도메인을 만든 경우 네트워크 보안 그룹을 만들어 Azure AD DS 관리 도메인의 가상 네트워크에서 트래픽을 제한합니다. 이러한 규칙을 배치해야 하는 Azure 표준 로드 밸런서가 만들어집니다. 이 네트워크 보안 그룹은 Azure AD DS를 보호하며 관리되는 도메인이 올바르게 작동하려면 필요합니다.
    * 네트워크 보안 그룹 및 필수 규칙을 만들려면 포털에서 Azure AD DS 관리 도메인을 선택합니다. **개요** 창에서 네트워크 보안 그룹을 자동으로 만들고 구성하라는 메시지가 표시됩니다.
* 최종 사용자가 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있도록 [Azure AD 도메인 서비스에 대한 암호 동기화를 사용하도록 설정합니다.](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds)

## <a name="modify-scoped-synchronization-using-powershell"></a>PowerShell을 사용하여 범위 별 동기화 수정

Azure AD DS 관리 도메인에 동기화해야 하는 그룹 목록을 수정하려면 [PowerShell 스크립트를](#powershell-script-for-scoped-synchronization) 다시 실행하고 새 그룹 목록을 지정합니다. 다음 예제에서는 동기화할 그룹에 더 이상 *GroupName2가*포함되어 있지 않으며 이제 *GroupName3*를 포함합니다.

> [!WARNING]
> 범위 동기화를 위한 그룹 목록에 *AAD DC Administrators* 그룹을 포함해야 합니다. 이 그룹을 포함하지 않으면 Azure AD DS 관리 도메인을 사용할 수 없습니다.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

동기화 범위를 변경하면 Azure AD DS 관리 도메인이 모든 데이터를 다시 동기화합니다. Azure AD DS 관리 도메인에 더 이상 필요하지 않은 개체는 삭제되며 다시 동기화를 완료하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="disable-scoped-synchronization-using-powershell"></a>PowerShell을 사용하여 범위 별 동기화 를 사용하지 않도록 설정

Azure AD DS 관리 도메인에 대한 그룹 기반 범위 별 동기화를 사용하지 않도록 설정하려면 Azure AD DS 리소스에서 *"필터링된 Sync" = "사용 안 함"을* 설정한 다음 관리되는 도메인을 업데이트합니다. 완료되면 모든 사용자 및 그룹이 Azure AD에서 동기화되도록 설정됩니다.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

동기화 범위를 변경하면 Azure AD DS 관리 도메인이 모든 데이터를 다시 동기화합니다. Azure AD DS 관리 도메인에 더 이상 필요하지 않은 개체는 삭제되며 다시 동기화를 완료하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

동기화 프로세스에 대한 자세한 내용은 [Azure AD 도메인 서비스의 동기화 이해](synchronization.md)를 참조하십시오.

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
