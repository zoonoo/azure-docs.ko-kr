---
title: 관리 단위 범위 (미리 보기)를 사용 하 여 역할 할당 및 나열-Azure Active Directory | Microsoft Docs
description: 관리 단위를 사용 하 여 Azure Active Directory에서 역할 할당 범위 제한
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870425"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>관리 단위에 범위 지정 역할 할당

Azure AD (Azure Active Directory)에서는 보다 세부적인 관리 제어를 위해 하나 이상의 au (관리 단위)로 제한 된 범위를 사용 하 여 Azure AD 역할에 사용자를 할당할 수 있습니다.

PowerShell 사용을 준비 하 고 관리 장치 관리를 Microsoft Graph 하는 단계는 [시작](roles-admin-units-manage.md#get-started)을 참조 하세요.

## <a name="roles-available"></a>사용 가능한 역할

역할  |  Description
----- |  -----------
인증 관리자  |  할당 된 관리 단위 에서만 관리자가 아닌 사용자에 대 한 인증 방법 정보를 보고 설정 하 고 다시 설정 하기 위한 액세스 권한이 있습니다.
그룹 관리자  |  할당 된 관리 단위로 명명 및 만료 정책 같은 그룹 및 그룹 설정의 모든 측면을 관리할 수 있습니다.
기술 지원팀 관리자  |  에서는 관리자가 아닌 관리자와 기술 지원팀 관리자에 대 한 암호를 할당 된 관리 단위로만 다시 설정할 수 있습니다.
라이선스 관리자  |  관리 단위 내 에서만 라이선스 할당을 할당, 제거 및 업데이트할 수 있습니다.
암호 관리자  |  는 할당 된 관리 단위 내에서 비관리자 및 암호 관리자에 대 한 암호를 다시 설정할 수 있습니다.
사용자 관리자  |  할당 된 관리 단위 내 에서만 제한 된 관리자에 대 한 암호 재설정을 포함 하 여 사용자 및 그룹의 모든 측면을 관리할 수 있습니다.

## <a name="assign-a-scoped-role"></a>범위 지정 역할 할당

### <a name="azure-portal"></a>Azure portal

포털에서 **AZURE AD > 관리 단위로** 이동 합니다. 사용자에 게 역할을 할당 하려는 관리 단위를 선택 합니다. 왼쪽 창에서 역할 및 관리자를 선택 하 여 사용 가능한 모든 역할을 나열 합니다.

![관리 단위를 선택 하 여 역할 범위 변경](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

할당할 역할을 선택 하 고 **할당 추가**를 선택 합니다. 그러면 역할에 할당 될 사용자를 한 명 이상 선택할 수 있는 오른쪽에 패널이 열립니다.

![범위를 지정 하는 역할을 선택 하 고 할당 추가를 선택 합니다.](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

강조 표시 된 섹션은 특정 환경에 필요한 대로 변경할 수 있습니다.

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

## <a name="list-the-scoped-admins-on-an-au"></a>AU에 범위가 지정 된 관리자 나열

### <a name="azure-portal"></a>Azure portal

관리 단위 범위에서 수행 되는 모든 역할 할당은 [AZURE AD의 관리 단위 섹션](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)에서 볼 수 있습니다. 포털에서 **AZURE AD > 관리 단위로** 이동 합니다. 나열할 역할 할당의 관리 단위를 선택 합니다. **역할 및 관리자** 를 선택 하 고 역할을 열어 관리 단위의 할당을 확인 합니다.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

강조 표시 된 섹션은 특정 환경에 필요한 대로 변경할 수 있습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>다음 단계

- [관리 단위 문제 해결 및 FAQ](roles-admin-units-faq-troubleshoot.md)
