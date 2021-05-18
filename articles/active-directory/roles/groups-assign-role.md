---
title: Azure Active Directory에서 그룹에 역할 할당 | Microsoft Docs
description: Azure Portal, PowerShell 또는 Graph API의 역할 할당 가능 그룹에 Azure AD 역할을 할당합니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816009abb688525cd7663311c79300a6d12cf146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742950"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹에 역할 할당

이 섹션에서는 IT 관리자가 Azure AD 그룹에 Azure AD (Azure Active Directory) 역할을 할당할 수 있는 방법에 대해 설명합니다.

## <a name="using-azure-ad-admin-center"></a>Azure AD 관리 센터 사용

Azure AD 역할에 그룹을 할당하는 것은 역할을 할당할 수 있는 그룹만 사용할 수 있다는 점을 제외하고 사용자 및 서비스 사용자를 할당하는 것과 비슷합니다. Azure Portal에서는 역할을 할당할 수 있는 그룹만 표시됩니다.

1. Azure AD 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로 [Azure AD 관리 센터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.

1. **Azure Active Directory** >  **역할 및 관리자** 를 선택하고, 할당 하려는 역할을 선택합니다.

1. ***role name** _ page에서 > _*Add assignment**를 선택합니다.

   ![새 역할 할당 추가](./media/groups-assign-role/add-assignment.png)

1. 그룹을 선택합니다. Azure AD 역할을 할당할 수 있는 그룹만 표시됩니다.

    [![할당할 수 있는 그룹만 새 역할 할당에 표시됩니다.](./media/groups-assign-role/eligible-groups.png "할당할 수 있는 그룹만 새 역할 할당에 표시됩니다.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. **추가** 를 선택합니다.

역할 권한 할당에 관한 자세한 내용은 [사용자에게 관리자 역할 및 관리자가 아닌 역할 할당](../fundamentals/active-directory-users-assign-role-azure-portal.md)을 참조하세요.

## <a name="using-powershell"></a>PowerShell 사용

### <a name="create-a-group-that-can-be-assigned-to-role"></a>역할에 할당할 수 있는 그룹 만들기

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>할당하려는 역할 정의 가져오기

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>역할 할당 만들기

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 사용

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Azure AD 역할 할당이 가능한 그룹 만들기

```
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

### <a name="get-the-role-definition"></a>역할 정의 가져오기

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>역할 할당 만들기

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>다음 단계

- [클라우드 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [클라우드 그룹에 할당된 역할 문제 해결](groups-faq-troubleshooting.md)
