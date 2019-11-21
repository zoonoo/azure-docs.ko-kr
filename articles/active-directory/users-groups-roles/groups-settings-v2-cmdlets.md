---
title: PowerShell V2 examples for managing groups - Azure AD  | Microsoft Docs
description: 이 페이지에는 Azure Active Directory에서 그룹을 관리하는 데 도움이 되는 PowerShell 예제가 나와 있습니다.
keywords: Azure AD, Azure Active Directory, PowerShell, 그룹, 그룹 관리
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a218e956c72f8005e533db7b8800e98ee72ce223
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233108"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>그룹 관리를 위한 Azure Active Directory 버전 2 cmdlet

> [!div class="op_single_selector"]
> - [Azure 포털](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](groups-settings-v2-cmdlets.md)
>
>

이 문서에서는 Azure AD(Azure Active Directory)에서 PowerShell을 사용하여 그룹을 관리하는 방법의 예제를 포함합니다.  또한 Azure AD PowerShell 모듈을 사용하여 설정하는 방법을 설명합니다. 먼저 [Azure AD PowerShell 모듈을 다운로드](https://www.powershellgallery.com/packages/AzureAD/)해야 합니다.

## <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell 모듈 설치

Azure AD PowerShell 모듈을 설치하려면 다음 명령을 사용합니다.

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

모듈을 사용할 수 있는지 확인하려면 다음 명령을 사용합니다.

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

이제 모듈에서 cmdlet 사용을 시작할 수 있습니다. Azure AD 모듈의 cmdlet에 대한 자세한 내용은 [Azure Active Directory PowerShell 버전 2](/powershell/azure/install-adv2?view=azureadps-2.0)에 대한 온라인 참조 문서를 참조하세요.

## <a name="connect-to-the-directory"></a>디렉터리에 연결

Azure AD PowerShell cmdlet을 사용하여 그룹 관리를 시작하기 전에 PowerShell 세션을 관리하려는 디렉터리에 연결해야 합니다. 다음 명령을 사용합니다.

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

이 cmdlet은 디렉터리에 액세스하는 데 사용할 자격 증명을 묻습니다. 이 예제에서는 karen@drumkit.onmicrosoft.com 을 사용하여 데모 디렉터리에 액세스합니다. 이 cmdlet은 세션이 디렉터리에 정상적으로 연결되었음을 표시하기 위한 확인을 반환합니다.

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

이제 Azure AD cmdlet을 사용하여 디렉터리에서 그룹 관리를 시작할 수 있습니다.

## <a name="retrieve-groups"></a>그룹 검색

디렉터리에서 기존 그룹을 검색하려면 Get-AzureADGroups cmdlet을 사용합니다. 

디렉터리의 모든 그룹을 검색하려면 매개 변수 없이 다음 cmdlet을 사용합니다.

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

이 cmdlet은 연결된 디렉터리의 모든 그룹을 반환합니다.

-objectID 매개 변수를 사용하여 그룹의 objectID를 지정하는 특정 그룹을 검색할 수 있습니다.

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

이제 이 cmdlet은 해당 objectID가 사용자가 입력한 매개 변수의 값과 일치하는 그룹을 반환합니다.

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

-filter 매개 변수를 사용하여 특정 그룹을 검색할 수 있습니다. 이 매개 변수는 다음 예제와 같이 ODATA 필터 절을 사용하고 필터와 일치하는 모든 그룹을 반환합니다.

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> Azure AD PowerShell cmdlet에서는 OData 쿼리 표준을 구현합니다. 자세한 내용은 [OData 엔드포인트를 사용하는 OData 시스템 쿼리 옵션](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter)의 **$filter**를 참조하세요.

## <a name="create-groups"></a>그룹 만들기

디렉터리에 새 그룹을 만들려면 New-AzureADGroup cmdlet을 사용합니다. 이 cmdlet을 "Marketing"이라는 새 보안 그룹을 만듭니다.

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>그룹 업데이트

기존 그룹을 업데이트하려면 Set-AzureADGroup cmdlet을 사용합니다. 이 예제에서는 그룹 “Intune Administrators”의 DisplayName 속성을 변경합니다. 먼저 Get-AzureADGroup cmdlet을 사용하여 그룹을 찾고 DisplayName 특성을 사용하여 필터링합니다.

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

다음으로 Description 속성을 새 값인 “Intune 디바이스 관리자”로 변경합니다.

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Now, if we find the group again, we see the Description property is updated to reflect the new value:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>그룹 삭제

디렉터리에서 그룹을 삭제하려면 다음과 같이 Remove-AzureADGroup cmdlet을 사용합니다.

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>그룹 구성원 자격 관리

### <a name="add-members"></a>구성원 추가

그룹에 새 구성원을 추가하려면 Add-AzureADGroupMember cmdlet을 사용합니다. 이 명령을 수행하면 이전 예제에서 사용한 Intune 관리자 그룹에 구성원이 추가됩니다.

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

-ObjectId 매개 변수는 구성원을 추가하려는 그룹의 ObjectID이며, -RefObjectId는 그룹에 구성원으로 추가하려는 사용자의 ObjectID입니다.

### <a name="get-members"></a>구성원 가져오기

그룹의 기존 구성원을 가져오려면 다음 예제와 같이 Get-AzureADGroupMember cmdlet을 사용합니다.

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>구성원 제거

이전에 추가한 그룹에 구성원을 제거하려면 다음과 같이 Remove-AzureADGroupMember cmdlet을 사용합니다.

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>구성원 확인

사용자의 그룹 구성원 자격을 확인하려면 Select-AzureADGroupIdsUserIsMemberOf cmdlet을 사용합니다. 이 cmdlet은 구성원 자격을 확인할 사용자의 ObjectId와 구성원 자격을 확인하려는 그룹의 목록을 해당 매개 변수로 사용합니다. 그룹 목록은 "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck" 형식의 복잡한 변수 형태로 제공되므로 먼저 해당 형식의 변수를 만들어야 합니다.

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

다음으로 다음 복합 변수의 "GroupIds" 특성에 확인할 groupId 값을 제공합니다.

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

이제 $g의 그룹에 대해 ObjectID가 72cd4bbd-2594-40a2-935c-016f3cfeeeea인 사용자의 그룹 구성원 자격을 확인하려는 경우 다음을 사용해야 합니다.

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

반환되는 값은 이 사용자가 구성원인 그룹의 목록입니다. 또한 Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf 또는 Select-AzureADGroupIdsServicePrincipalIsMemberOf를 사용하여 지정된 그룹 목록에 대해 연락처, 그룹 또는 서비스 주체 구성원 자격을 확인할 수도 있습니다.

## <a name="disable-group-creation-by-your-users"></a>사용자의 그룹 만들기를 사용하지 않도록 설정

관리자가 아닌 사용자가 보안 그룹을 만들지 못하도록 할 수 있습니다. MSODS(Microsoft Online Directory Service)의 기본 동작은 SSGM(셀프 서비스 그룹 관리)도 사용 가능으로 설정되어 있는지에 관계없이 관리자가 아닌 사용자가 그룹을 만들 수 있도록 허용하는 것입니다. SSGM 설정은 내 앱 액세스 패널에서만 동작을 제어합니다.

관리자가 아닌 사용자의 그룹 만들기를 사용하지 않도록 설정하려면

1. 관리자가 아닌 사용자가 그룹을 만들 수 있는지 확인합니다.
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. `UsersPermissionToCreateGroupsEnabled : True`가 반환되면 관리자가 아닌 사용자가 그룹을 만들 수 있는 것입니다. 이 기능을 사용하지 않도록 설정하려면
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>그룹 소유자 관리

그룹에 소유자를 추가하려면 Add-AzureADGroupOwner cmdlet을 사용합니다.

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

The -ObjectId parameter is the ObjectID of the group to which we want to add an owner, and the -RefObjectId is the ObjectID of the user or service principal we want to add as an owner of the group.

그룹의 소유자를 검색하려면 Get-AzureADGroupOwner cmdlet을 사용합니다.

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

The cmdlet returns the list of owners (users and service principals) for the specified group:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

그룹에서 소유자를 제거하려면 Remove-AzureADGroupOwner cmdlet을 사용합니다.

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>예약된 별칭

그룹을 만들 때 특정 엔드포인트는 최종 사용자가 mailNickname 또는 별칭이 그룹의 전자 메일 주소의 일부로 사용되도록 지정할 수 있게 합니다. Groups with the following highly privileged email aliases can only be created by an Azure AD global administrator. 
  
* abuse
* 관리자
* administrator
* hostmaster
* majordomo
* postmaster
* root
* 안전성
* 보안
* ssl-admin
* webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Group writeback to on-premises (preview)

Today, many groups are still managed in on-premises Active Directory. To answer requests to sync cloud groups back to on-premises, Office 365 groups writeback feature for Azure AD is now available for preview.

Office 365 groups are created and managed in the cloud. The writeback capability allows you to write back Office 365 groups as distribution groups to an Active Directory forest with Exchange installed. Users with on-premises Exchange mailboxes can then send and receive emails from these groups. The group writeback feature doesn't support Azure AD security groups or distribution groups.

For more details, please refer to documentation for the [Azure AD Connect sync service](../hybrid/how-to-connect-syncservice-features.md).

Office 365 group writeback is a public preview feature of Azure Active Directory (Azure AD) and is available with any paid Azure AD license plan. For some legal information about previews, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>다음 단계

[Azure Active Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0)에서 더 많은 Azure Active Directory PowerShell 설명서를 찾을 수 있습니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
