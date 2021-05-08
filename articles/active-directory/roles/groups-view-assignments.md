---
title: Azure Active Directory에서 그룹에 할당된 역할 보기 | Microsoft Docs
description: Azure AD 관리 센터를 사용하여 그룹에 할당된 역할을 볼 수 있는 방법에 대해 알아봅니다. 그룹 및 할당된 역할 보기는 기본 사용자 권한입니다.
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
ms.openlocfilehash: 7dac23897f8a17c9adb4ae78736a6a8afa85a18b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012006"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹에 할당된 역할 보기

이 섹션에서는 Azure AD 관리 센터를 사용하여 그룹에 할당된 역할을 볼 수 있는 방법에 대해 설명합니다. 그룹 및 할당된 역할 보기는 기본 사용자 권한입니다.

1. 비관리자 또는 관리자 로그인 정보를 사용하여 [Azure AD 관리 센터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.

1. 원하는 그룹을 선택합니다.

1. **할당된 역할** 을 선택합니다. 해당 그룹에 할당된 모든 Azure AD 역할을 볼 수 있습니다.

   ![선택한 그룹에 할당된 모든 역할 보기](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>PowerShell 사용

### <a name="get-object-id-of-the-group"></a>그룹의 개체 ID 가져오기

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>그룹에 대한 역할 할당 보기

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 사용

### <a name="get-object-id-of-the-group"></a>그룹의 개체 ID 가져오기

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>그룹에 대한 역할 할당 가져오기

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>다음 단계

- [클라우드 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [클라우드 그룹에 할당된 역할 문제 해결](groups-faq-troubleshooting.md)
