---
title: Azure Active Directory에서 역할을 할당 하기 위한 그룹 만들기 Microsoft Docs
description: ID 관리를 위임하기 위한 사용자 지정 Azure AD 역할을 미리 봅니다. Azure Portal, PowerShell 또는 Graph API에서 Azure 역할을 관리합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f816c95b5e210e86e2215cadf8407f442d69ebe
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476116"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Azure Active Directory에서 역할 할당 가능 그룹 만들기

' IsAssignableToRole ' 속성을 True로 설정 하 여 만든 그룹에만 역할을 할당할 수 있으며, azure ad 역할을 사용 하 여 azure ad 포털에서 만든 그룹에는 설정 된 **그룹** 에 역할을 할당할 수 있습니다. 이 그룹 특성은 Azure Active Directory (Azure AD)에서 역할에 할당 될 수 있는 그룹을 만듭니다. 이 문서에서는 이러한 특수 한 종류의 그룹을 만드는 방법을 설명 합니다.

## <a name="using-azure-ad-admin-center"></a>Azure AD 관리 센터 사용

1. Azure ad 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로 [AZURE ad 관리 센터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 에 로그인 합니다.
1. **그룹**  >  **모든 그룹**  >  **새 그룹**을 선택 합니다.

    [![Azure Active Directory를 열고 새 그룹을 만듭니다.](media/roles-groups-create-eligible/new-group.png "Azure Active Directory를 열고 새 그룹을 만듭니다.")](media/roles-groups-create-eligible/new-group.png#<lightbox>)

1. **새 그룹** 탭에서 그룹 유형, 이름 및 설명을 입력 합니다.
1. Azure AD 역할에 대 한 사용 **을 그룹에 할당할 수 있습니다**. 이 스위치는 스위치를 설정할 수 있는 두 개의 역할만 있으므로 권한 있는 역할 관리자 및 전역 관리자 에게만 표시 됩니다.

    [![새 그룹을 역할 할당에 적합 하도록 설정](media/roles-groups-create-eligible/eligible-switch.png "새 그룹을 역할 할당에 적합 하도록 설정")](media/roles-groups-create-eligible/eligible-switch.png#<lightbox>)

1. 그룹의 멤버 및 소유자를 선택 합니다. 그룹에 역할을 할당 하는 옵션도 있지만 여기에서는 역할을 할당할 필요가 없습니다.

    [![역할 할당 가능 그룹에 멤버를 추가 하 고 역할을 할당 합니다.](media/roles-groups-create-eligible/specify-members.png "역할 할당 가능 그룹에 멤버를 추가 하 고 역할을 할당 합니다.")](media/roles-groups-create-eligible/specify-members.png#<lightbox>)

1. 멤버 및 소유자를 지정한 후 **만들기**를 선택 합니다.

    [![페이지 맨 아래에 만들기 단추가 있습니다.](media/roles-groups-create-eligible/create-button.png "페이지 맨 아래에 만들기 단추가 있습니다.")](media/roles-groups-create-eligible/create-button.png#<lightbox>)

사용자가 할당 했을 수 있는 모든 역할로 그룹이 만들어집니다.

## <a name="using-powershell"></a>PowerShell 사용

### <a name="install-the-azure-ad-preview-module"></a>Azure AD 미리 보기 모듈 설치

```powershell
install-module azureadpreview
import-module azureadpreview
```

모듈을 사용할 준비가 되었는지 확인 하려면 다음 명령을 실행 합니다.

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>역할에 할당 될 수 있는 그룹 만들기

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

이 유형의 그룹에 대해는 `isPublic` 항상 false가 되며 `isSecurityEnabled` 항상 true입니다.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>한 그룹의 사용자 및 서비스 사용자를 역할 할당 가능 그룹에 복사 합니다.

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating O365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 사용

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Azure AD에서 역할 할당 가능 그룹 만들기

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

이 유형의 그룹에 대해는 `isPublic` 항상 false가 되며 `isSecurityEnabled` 항상 true입니다.

## <a name="next-steps"></a>다음 단계

- [클라우드 그룹에 역할 할당](roles-groups-assign-role.md)
- [클라우드 그룹을 사용 하 여 역할 할당 관리](roles-groups-concept.md)
- [클라우드 그룹에 할당 된 역할 문제 해결](roles-groups-faq-troubleshooting.md)
