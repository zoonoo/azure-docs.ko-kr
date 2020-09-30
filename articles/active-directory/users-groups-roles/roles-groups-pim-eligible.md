---
title: Azure AD에서 Privileged Identity Management를 사용 하 여 그룹에 역할 할당 | Microsoft Docs
description: PIM (Azure AD Privileged Identity Management)을 사용 하 여 그룹에 Azure Active Directory (Azure AD) 역할을 할당 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 93c97055c6e31050dbc545445ec144ba37abc865
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539897"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Privileged Identity Management를 사용 하 여 그룹에 역할 할당

이 문서에서는 PIM (Azure AD Privileged Identity Management)을 사용 하 여 그룹에 Azure Active Directory (Azure AD) 역할을 할당 하는 방법을 설명 합니다.

> [!NOTE]
> PIM을 사용 하 여 Azure AD 역할에 그룹을 할당 하려면 업데이트 된 버전의 Privileged Identity Management를 사용 해야 합니다. Azure AD 조직에서 Privileged Identity Management API를 활용 하는 경우 이전 버전의 PIM에 있을 수 있습니다. 그렇다면 별칭에 연결 pim_preview@microsoft.com 하 여 조직을 이동 하 고 API를 업데이트 하세요. [PIM의 AZURE AD 역할 및 기능](../privileged-identity-management/azure-ad-roles-features.md)에 대해 자세히 알아보세요.

## <a name="using-azure-ad-admin-center"></a>Azure AD 관리 센터 사용

1. [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) 에 로그인 하 여 조직의 권한 있는 역할 관리자 또는 전역 관리자 권한으로 로그인 합니다.

1. **Privileged Identity Management**  >  **Azure AD 역할**  >  **역할**  >  **할당 추가** 를 선택 합니다.

1. 역할을 선택한 다음 그룹을 선택 합니다. 역할 할당 (역할 할당 가능한 그룹)에 적합 한 그룹만 표시 되 고 모든 그룹이 표시 되지 않습니다.

    !["역할 선택" 및 "멤버 선택" 섹션이 강조 표시 된 "할당 추가" 페이지를 보여 주는 스크린샷](./media/roles-groups-pim-eligible/select-member.png)

1. 원하는 멤버 자격 설정을 선택 합니다. 활성화가 필요한 역할의 경우 **적격**을 선택 합니다. 기본적으로 사용자는 영구적으로 사용할 수 있지만 사용자의 자격에 대 한 시작 및 종료 시간을 설정할 수도 있습니다. 완료 되 면 저장 및 추가를 눌러 역할 할당을 완료 합니다.

    ![역할을 할당할 사용자 선택](./media/roles-groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>PowerShell 사용

### <a name="download-the-azure-ad-preview-powershell-module"></a>Azure AD Preview PowerShell 모듈을 다운로드 합니다.

Azure AD #PowerShell 모듈을 설치 하려면 다음 cmdlet을 사용 합니다.

```powershell
install-module azureadpreview
import-module azureadpreview
```

모듈을 사용할 준비가 되었는지 확인 하려면 다음 cmdlet을 사용 합니다.

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>그룹을 역할의 적격 멤버로 할당

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 사용

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>다음 단계

- [클라우드 그룹을 사용 하 여 역할 할당 관리](roles-groups-concept.md)
- [클라우드 그룹에 할당된 역할 문제 해결](roles-groups-faq-troubleshooting.md)
- [Privileged Identity Management에서 Azure AD 관리자 역할 설정 구성](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 할당](../privileged-identity-management/pim-resource-roles-assign-roles.md)
