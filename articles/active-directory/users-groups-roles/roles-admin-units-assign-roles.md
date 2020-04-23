---
title: 관리 단위 범위(미리 보기)가 있는 역할 할당 및 목록 - Azure Active Directory | 마이크로 소프트 문서
description: 관리 단위를 사용하여 Azure Active Directory에서 역할 할당 범위를 제한합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870425"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>관리 단위에 범위 지정 역할 할당

Azure Active Directory(Azure AD)에서 보다 세분화된 관리 제어를 위해 하나 이상의 관리 단위(AUs)로 제한된 범위의 Azure AD 역할에 사용자를 할당할 수 있습니다.

관리 단위 관리를 위해 PowerShell 및 Microsoft 그래프를 사용할 준비를 하는 단계는 [시작 을](roles-admin-units-manage.md#get-started)참조하십시오.

## <a name="roles-available"></a>사용 가능한 역할

역할  |  Description
----- |  -----------
인증 관리자  |  할당된 관리 단위의 모든 비관리자 사용자에 대한 인증 방법 정보를 보고, 설정하고 재설정할 수 있습니다.
그룹 관리자  |  할당된 관리 단위에서만 이름 지정 및 만료 정책과 같은 그룹 및 그룹 설정의 모든 측면을 관리할 수 있습니다.
기술 지원팀 관리자  |  할당된 관리 단위에서만 비관리자 및 헬프데스크 관리자에 대한 암호를 재설정할 수 있습니다.
라이선스 관리자  |  관리 단위 내에서만 라이센스 할당을 할당, 제거 및 업데이트할 수 있습니다.
암호 관리자  |  할당된 관리 단위 내에서만 비관리자 및 암호 관리자에 대한 암호를 재설정할 수 있습니다.
사용자 관리자  |  할당된 관리 단위 내에서만 제한된 관리자에 대한 암호 재설정을 포함하여 사용자 및 그룹의 모든 측면을 관리할 수 있습니다.

## <a name="assign-a-scoped-role"></a>범위 지정 역할 할당

### <a name="azure-portal"></a>Azure portal

포털의 **Azure AD > 관리 단위로** 이동합니다. 사용자에게 역할을 할당할 관리 단위를 선택합니다. 왼쪽 창에서 역할 및 관리자를 선택하여 사용 가능한 모든 역할을 나열합니다.

![역할 범위를 변경하려면 관리 단위를 선택합니다.](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

할당할 역할을 선택한 다음 **할당 추가를 선택합니다.** 그러면 오른쪽에 있는 패널이 열리면 역할에 할당할 사용자 중 하나 이상을 선택할 수 있습니다.

![범위를 지정할 역할을 선택한 다음 과제 추가를 선택합니다.](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

강조 표시된 섹션은 특정 환경에 필요에 따라 변경될 수 있습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>AU에 범위 가 있는 관리자 나열

### <a name="azure-portal"></a>Azure portal

관리 단위 범위로 수행된 모든 역할 할당은 [Azure AD의 관리 단위 섹션에서](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)볼 수 있습니다. 포털의 **Azure AD > 관리 단위로** 이동합니다. 나열할 역할 할당의 관리 단위를 선택합니다. **역할 및 관리자를** 선택하고 역할을 열어 관리자 단위에서 할당을 봅니다.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

강조 표시된 섹션은 특정 환경에 필요에 따라 변경될 수 있습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>다음 단계

- [관리 단위 문제 해결 및 FAQ](roles-admin-units-faq-troubleshoot.md)
