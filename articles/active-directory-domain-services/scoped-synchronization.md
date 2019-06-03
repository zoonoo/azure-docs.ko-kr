---
title: 'Azure Active Directory Domain Services: 범위가 지정된 동기화 | Microsoft Docs'
description: Azure AD에서 관리되는 도메인으로 범위 동기화 구성
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 332c0bc43a269734e0dc4db37228006a78e460e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246811"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Azure AD에서 관리되는 도메인으로 범위 동기화 구성
이 문서에서는 Azure AD 디렉터리에서 Azure AD Domain Services 관리되는 도메인으로 특정 사용자 계정만 동기화하도록 구성하는 방법을 보여 줍니다.


## <a name="group-based-scoped-synchronization"></a>그룹 기반 범위 동기화
기본적으로 Azure AD 디렉터리 내의 모든 사용자와 그룹이 관리되는 도메인에 동기화됩니다. 몇몇 사용자만 관리되는 도메인을 사용하는 경우 해당 사용자 계정만 동기화할 수 있습니다. 그룹 기반 범위 동기화를 사용하여 이를 수행할 수 있습니다. 구성될 경우 지정한 그룹에 속한 사용자 계정만 관리되는 도메인에 동기화됩니다.

다음 표는 범위 동기화를 사용하는 방법을 확인하는 데 도움이 됩니다.

| **현재 상태** | **필요한 상태** | **필수 구성** |
| --- | --- | --- |
| 기존 관리되는 도메인이 모든 사용자 계정 및 그룹을 동기화하도록 구성되어 있습니다. | 특정 그룹에 속한 사용자 계정만 관리되는 도메인에 동기화하려고 합니다. | [기존 관리되는 도메인을 삭제](delete-aadds.md)합니다. 그런 다음, 이 문서의 지침에 따라 범위 동기화를 구성하고 관리되는 도메인을 다시 만듭니다. |
| 기존 관리되는 도메인이 없습니다. | 새 관리되는 도메인을 만들고 특정 그룹에 속한 사용자 계정만 동기화하려고 합니다. | 이 문서의 지침에 따라 범위 동기화를 구성하고 새 관리되는 도메인을 만듭니다. |
| 기존 관리되는 도메인이 특정 그룹에 속한 계정만 동기화하도록 구성되어 있습니다. | 관리되는 도메인에 동기화되어야 하는 사용자가 속한 그룹 목록을 수정하려고 합니다. | 이 문서의 지침에 따라 범위 동기화를 수정합니다. |

> [!WARNING]
> **동기화 범위를 변경하면 관리되는 도메인이 다시 동기화됩니다.**
> 
>  * 관리되는 도메인의 동기화 범위를 변경하면 전체 다시 동기화가 수행됩니다.
>  * 관리되는 도메인에서 더 이상 필요하지 않은 개체는 삭제됩니다. 새 개체는 관리되는 도메인에서 만들어집니다.
>  * 다시 동기화는 관리되는 도메인 및 Azure AD 디렉터리의 개체(사용자, 그룹 및 그룹 멤버십) 수에 따라 완료하는 데 오래 걸릴 수 있습니다. 수십만 개의 많은 개체가 있는 큰 디렉터리의 경우 다시 동기화에 며칠이 걸릴 수 있습니다.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>새 관리형 도메인 만들기 및 Azure Portal을 사용하여 그룹 기반 범위 동기화 사용하도록 설정

1. [시작 가이드](create-instance.md)에 따라 관리형 도메인을 만듭니다.
2. Azure AD Domain Services 만들기 마법사에서 동기화 스타일 선택 중에 **범위**를 선택합니다.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>새 관리형 도메인 만들기 및 PowerShell을 사용하여 그룹 기반 범위 동기화 사용하도록 설정
PowerShell을 사용하여 이 단계 집합을 완료합니다. [PowerShell을 사용하여 Azure Active Directory Domain Services를 사용](powershell-create-instance.md)하도록 설정하려면 지침을 참조하세요. 이 문서의 몇 단계는 범위 동기화를 구성하기 위해 약간 수정되었습니다.

다음 단계를 완료하여 관리되는 도메인에 대한 그룹 기반 범위 동기화를 구성합니다.

1. 다음 작업을 완료합니다.
   * [작업 1: 필수 PowerShell 모듈 설치](powershell-create-instance.md#task-1-install-the-required-powershell-modules)
   * [작업 2: Azure AD 디렉터리에서 필요한 서비스 주체 만들기](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)
   * [작업 3: 만들기 및 ' AAD DC 관리자 ' group]powershell-create-instance.md#task-3-create-and-configure-the-aad-dc-administrators-group 구성).
   * [작업 4: Azure AD Domain Services 리소스 공급자 등록](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider)
   * [작업 5: 리소스 그룹 만들기](powershell-create-instance.md#task-5-create-a-resource-group)
   * [작업 6: 가상 네트워크 만들기 및 구성](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network)

2. 동기화할 그룹을 선택하고 관리되는 도메인에 동기화할 그룹의 표시 이름을 제공합니다.

3. [다음 섹션의 스크립트](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1)를 ```Select-GroupsToSync.ps1``` 파일에 저장합니다. 다음과 같이 스크립트를 실행합니다.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **‘AAD DC 관리자’ 그룹을 포함해야 합니다.**
   >
   > 범위 동기화를 위해 구성된 그룹 목록에 ‘AAD DC 관리자’ 그룹을 포함해야 합니다. 이 그룹을 포함하지 않으면 관리되는 도메인을 사용할 수 없습니다.
   >

4. 이제 관리되는 도메인을 만들고 관리되는 도메인의 그룹 기반 범위 동기화를 사용하도록 설정합니다. ```Properties``` 매개 변수에 ```"filteredSync" = "Enabled"``` 속성을 포함합니다. 예를 들어, [작업 7: Azure AD Domain Services 관리되는 도메인 프로비전](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain)에서 복사한 다음 스크립트 조각을 참조하세요.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > ```-Properties``` 매개 변수에 ```"filteredSync" = "Enabled"```를 포함해야 관리되는 도메인에 범위 동기화를 사용할 수 있습니다.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>관리되는 도메인에 동기화할 그룹을 선택하는 스크립트(Select-GroupsToSync.ps1)
다음 스크립트를 파일(```Select-GroupsToSync.ps1```)에 저장합니다. 이 스크립트는 선택한 그룹을 관리되는 도메인에 동기화하도록 Azure AD Domain Services를 구성합니다. 지정된 그룹에 속한 모든 사용자 계정이 관리되는 도메인에 동기화됩니다.

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


## <a name="modify-group-based-scoped-synchronization"></a>그룹 기반 범위 동기화 수정
관리되는 도메인에 동기화되어야 하는 사용자가 속한 그룹 목록을 수정하려면 [PowerShell 스크립트](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1)를 다시 실행하고 새 그룹 목록을 지정합니다. 항상 이 목록에서 ‘AAD DC 관리자’ 그룹을 지정해야 합니다.

> [!WARNING]
> **‘AAD DC 관리자’ 그룹을 포함해야 합니다.**
>
> 범위 동기화를 위해 구성된 그룹 목록에 ‘AAD DC 관리자’ 그룹을 포함해야 합니다. 이 그룹을 포함하지 않으면 관리되는 도메인을 사용할 수 없습니다.
>


## <a name="disable-group-based-scoped-synchronization"></a>그룹 기반 범위 동기화 사용 안 함
다음 PowerShell 스크립트를 사용하여 관리되는 도메인의 그룹 기반 범위 동기화를 사용하지 않도록 설정합니다.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>다음 단계
* [Azure AD Domain Services의 동기화 이해](synchronization.md)
* [PowerShell을 사용하여 Azure Active Directory Domain Services 사용](powershell-create-instance.md)
