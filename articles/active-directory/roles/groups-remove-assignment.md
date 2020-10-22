---
title: Azure Active Directory에서 그룹의 역할 할당 제거 Microsoft Docs
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
ms.openlocfilehash: 3c7f441930d9d99f35c2e53bb040b0db0a427659
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377574"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹의 역할 할당 제거

이 문서에서는 IT 관리자가 그룹에 할당 된 Azure AD 역할을 제거 하는 방법을 설명 합니다. Azure Portal에서 이제 사용자에 대 한 직접 및 간접 역할 할당을 모두 제거할 수 있습니다. 사용자에 게 그룹 멤버 자격에 의해 역할이 할당 된 경우 해당 그룹에서 사용자를 제거 하 여 역할 할당을 제거 합니다.

## <a name="using-azure-admin-center"></a>Azure 관리 센터 사용

1. Azure ad 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로 [AZURE ad 관리 센터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 에 로그인 합니다.

1. **역할 및 관리자** > **_역할 이름_*_을 선택 합니다.

1. 역할 할당을 제거할 그룹을 선택 하 고 _ * 할당 제거 * *를 선택 합니다.

   ![선택한 그룹에서 역할 할당을 제거 합니다.](./media/groups-remove-assignment/remove-assignment.png)

1. 작업을 확인 하 라는 메시지가 표시 되 면 **예**를 선택 합니다.

## <a name="using-powershell"></a>PowerShell 사용

### <a name="create-a-group-that-can-be-assigned-to-role"></a>역할에 할당 될 수 있는 그룹 만들기

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>그룹을 할당 하려는 역할 정의를 가져옵니다.

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>역할 할당 만들기

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>역할 할당 제거

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 사용

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Azure AD 역할을 할당할 수 있는 그룹 만들기

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
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

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>역할 할당 만들기

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>역할 할당 삭제

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>다음 단계

- [클라우드 그룹을 사용 하 여 역할 할당 관리](groups-concept.md)
- [클라우드 그룹에 할당된 역할 문제 해결](groups-faq-troubleshooting.md)
