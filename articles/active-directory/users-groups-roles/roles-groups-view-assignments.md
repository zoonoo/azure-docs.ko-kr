---
title: Azure Active Directory에서 그룹에 할당 된 역할 보기 Microsoft Docs
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
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476089"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Azure Active Directory의 그룹에 할당 된 역할 보기

이 섹션에서는 Azure AD 관리 센터를 사용 하 여 그룹에 할당 된 역할을 볼 수 있는 방법에 대해 설명 합니다. 그룹 및 할당 된 역할 보기는 기본 사용자 권한입니다.

1. 비관리자 또는 관리자 자격 증명을 사용 하 여 [AZURE AD 관리 센터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 에 로그인 합니다.

1. 관심이 있는 그룹을 선택 합니다.

1. **할당 된 역할**을 선택 합니다. 이제이 그룹에 할당 된 모든 Azure AD 역할을 볼 수 있습니다.

   ![선택한 그룹에 할당 된 모든 역할 보기](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>PowerShell 사용

### <a name="get-object-id-of-the-group"></a>그룹의 개체 ID를 가져옵니다.

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>그룹에 대 한 역할 할당 보기

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 사용

### <a name="get-object-id-of-the-group"></a>그룹의 개체 ID를 가져옵니다.

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>그룹에 역할 할당 가져오기

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>다음 단계

- [클라우드 그룹을 사용 하 여 역할 할당 관리](roles-groups-concept.md)
- [클라우드 그룹에 할당 된 역할 문제 해결](roles-groups-faq-troubleshooting.md)
