---
title: 관리 단위 범위를 사용 하 여 역할 할당 및 나열-Azure Active Directory | Microsoft Docs
description: 관리 단위를 사용 하 여 Azure Active Directory에서 역할 할당의 범위를 제한할 수 있습니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfae813f01d3e7a08e18cde76e5c26ca253a371f
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026601"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>관리 단위에 범위 지정 역할 할당

Azure AD (Azure Active Directory)에서 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위로 제한 되는 범위를 사용 하 여 Azure AD 역할에 사용자를 할당할 수 있습니다.

PowerShell을 사용 하 고 관리 장치 관리에 Microsoft Graph을 준비 하려면 [시작](admin-units-manage.md#get-started)을 참조 하세요.

## <a name="available-roles"></a>사용 가능한 역할

역할  |  설명
----- |  -----------
인증 관리자  |  할당 된 관리 단위 에서만 관리자가 아닌 사용자에 대 한 인증 방법 정보를 보고 설정 하 고 다시 설정 하기 위한 액세스 권한이 있습니다.
그룹 관리자  |  는 할당 된 관리 단위 에서만 이름 지정 및 만료 정책 같은 그룹 및 그룹 설정의 모든 측면을 관리할 수 있습니다.
기술 지원팀 관리자  |  에서는 관리자가 아닌 관리자와 기술 지원팀 관리자에 대 한 암호를 할당 된 관리 단위로만 다시 설정할 수 있습니다.
라이선스 관리자  |  는 관리 단위 내 에서만 라이선스 할당을 할당, 제거 및 업데이트할 수 있습니다.
암호 관리자  |  는 할당 된 관리 단위 내에서 비관리자 및 암호 관리자에 대 한 암호를 다시 설정할 수 있습니다.
사용자 관리자  |  할당 된 관리 단위 내 에서만 제한 된 관리자에 대 한 암호 재설정을 포함 하 여 사용자 및 그룹의 모든 측면을 관리할 수 있습니다.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>범위가 지정 된 역할에 할당할 수 있는 보안 주체

관리 단위 범위가 있는 역할에는 다음 보안 주체를 할당할 수 있습니다.

* 사용자
* 역할 할당 가능한 클라우드 그룹 (미리 보기)
* SPN(서비스 주체 이름)

## <a name="assign-a-scoped-role"></a>범위 지정 역할 할당

Azure Portal, PowerShell 또는 Microsoft Graph를 사용 하 여 범위 지정 역할을 할당할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure Portal에서 **AZURE AD** 로 이동 합니다.

1. **관리 단위** 를 선택 하 고 사용자 역할 범위를 할당 하려는 관리 단위를 선택 합니다. 

1. 왼쪽 창에서 **역할 및 관리자** 를 선택 하 여 사용 가능한 모든 역할을 나열 합니다.

   ![역할 범위를 할당 하려는 관리 단위를 선택 하기 위한 "역할 및 관리자" 창의 스크린샷](./media/admin-units-assign-roles/select-role-to-scope.png)

1. 할당할 역할을 선택 하 고 **할당 추가** 를 선택 합니다. 

1. **할당 추가** 창에서 역할에 할당할 사용자를 하나 이상 선택 합니다.

   ![범위를 지정 하는 역할을 선택 하 고 할당 추가를 선택 합니다.](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> PIM (Azure AD Privileged Identity Management)을 사용 하 여 관리 단위에 역할을 할당 하려면 [pim에서 AZURE AD 역할 할당](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)을 참조 하세요.

### <a name="use-powershell"></a>PowerShell 사용

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

강조 표시 된 섹션을 특정 환경에 필요한 대로 변경할 수 있습니다.

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>관리 단위로 범위가 지정 된 관리자 목록 보기

Azure Portal, PowerShell 또는 Microsoft Graph를 사용 하 여 범위가 지정 된 관리자 목록을 볼 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

[AZURE AD의 관리 단위 섹션](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)에서 관리 단위 범위를 사용 하 여 만든 모든 역할 할당을 볼 수 있습니다. 

1. Azure Portal에서 **AZURE AD** 로 이동 합니다.

1. 왼쪽 창에서 **관리 단위** 를 선택 하 고 보려는 역할 할당 목록에 대 한 관리 단위를 선택 합니다. 

1. **역할 및 관리자** 를 선택 하 고 역할을 열어 관리 단위의 할당을 확인 합니다.

### <a name="use-powershell"></a>PowerShell 사용

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

강조 표시 된 섹션을 특정 환경에 필요한 대로 변경할 수 있습니다.

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>다음 단계

- [클라우드 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [클라우드 그룹에 할당 된 역할 문제 해결](groups-faq-troubleshooting.md)
