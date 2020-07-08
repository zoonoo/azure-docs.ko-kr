---
title: 관리 단위 (미리 보기)에서 사용자 추가, 제거 및 나열-Azure Active Directory | Microsoft Docs
description: Azure Active Directory 관리 단위에서 사용자 및 해당 역할 권한 관리
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9b76ac103b873026dce3d3f8f92e54dc3afc14c
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850938"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory 관리 단위에서 사용자 추가 및 관리

Azure Active Directory (Azure AD)에서 관리 단위 (AU)에 사용자를 추가 하 여 관리 범위를 세부적으로 제어할 수 있습니다.

PowerShell 및 Microsoft Graph를 관리 단위에 사용하기 위해 준비하는 단계는 [시작](roles-admin-units-manage.md#get-started)을 참조하세요.

## <a name="add-users-to-an-au"></a>AU에 사용자 추가

### <a name="azure-portal"></a>Azure portal

두 가지 방법으로 관리 단위에 사용자를 할당할 수 있습니다.

1. 개별 할당

    1. 포털에서 Azure AD로 이동 하 여 사용자를 선택 하 고 관리 단위에 할당할 사용자를 선택할 수 있습니다. 그런 다음 왼쪽 패널에서 관리 단위를 선택할 수 있습니다. 관리 단위에 할당을 클릭 하 고 사용자를 할당할 관리 단위를 선택 하 여 하나 이상의 관리 단위에 사용자를 할당할 수 있습니다.

       ![추가를 선택 하 고 관리 단위의 이름을 입력 합니다.](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. 포털에서 Azure AD로 이동 하 여 왼쪽 창에서 관리 단위를 선택 하 고 사용자를 할당할 관리 단위를 선택할 수 있습니다. 왼쪽 창에서 모든 사용자를 선택한 후 구성원 추가를 선택 합니다. 그런 다음 오른쪽 창에서 관리 단위에 할당할 사용자를 하나 이상 선택할 수 있습니다.

        ![관리 단위를 선택한 다음, 구성원 추가](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. 대량 할당

    포털에서 Azure AD로 이동 하 고 관리 단위를 선택 합니다. 사용자를 추가할 관리 단위를 선택 합니다. 모든 사용자-.csv 파일에서 멤버 추가 > 클릭 하 여 계속 합니다. 그런 다음 CSV 템플릿을 다운로드 하 고 파일을 편집할 수 있습니다. 형식은 단순 하 고 각 줄에 단일 UPN을 추가 해야 합니다. 파일이 준비 되 면 적절 한 위치에 저장 한 다음 스냅숏에서 강조 표시 된 대로 3 단계에서 업로드 합니다.

    ![관리 단위에 사용자 대량 할당](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

위의 예에서는 AzureADAdministrativeUnitMember cmdlet을 사용 하 여 관리 단위에 사용자를 추가 합니다. 사용자를 추가할 관리 단위의 개체 ID 이며 추가 될 사용자의 개체 ID는 인수로 사용 됩니다. 강조 표시된 섹션은 특정 환경의 요구에 따라 달라질 수 있습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
}
```

예제:

```http
{
  "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>사용자의 관리 단위 나열

### <a name="azure-portal"></a>Azure portal

Azure Portal에서 Azure AD > 사용자로 이동 하 여 사용자의 프로필을 열 수 있습니다. 사용자를 클릭 하 여 사용자의 프로필을 엽니다.

![Azure Active Directory에서 사용자 프로필을 엽니다.](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

왼쪽 패널에서 **관리 단위** 를 선택 하 여 사용자가 할당 된 관리 단위 목록을 표시 합니다.

![사용자의 관리 단위 나열](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>AU에서 단일 사용자 제거

### <a name="azure-portal"></a>Azure portal

관리 단위에서 사용자를 제거 하는 방법에는 두 가지가 있습니다. Azure Portal에서 **Azure AD**사용자로 이동 하 여 사용자의 프로필을 열 수 있습니다  >  **Users**. 사용자를 선택 하 여 사용자의 프로필을 엽니다. 사용자를 제거 하려는 관리 단위를 선택 하 고 **관리 단위에서 제거**를 선택 합니다.

![사용자 프로필에서 관리 단위에서 사용자 제거](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

사용자를 제거 하려는 관리 단위를 선택 하 여 **Azure AD**  >  **관리 단위** 에서 사용자를 제거할 수도 있습니다. 사용자를 선택 하 고 **구성원 제거**를 선택 합니다.
  
![관리 단위 수준에서 사용자 제거](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>둘 이상의 사용자를 대량 제거

Azure AD > 관리 단위로 이동 하 여 사용자를 제거할 관리 단위를 선택할 수 있습니다. 구성원 대량 제거를 클릭 합니다. 제거할 사용자 목록을 제공 하기 위한 CSV 템플릿을 다운로드 합니다.

다운로드 한 CSV 템플릿을 관련 사용자 항목으로 편집 합니다. 템플릿의 처음 두 행을 제거 하지 마십시오. 각 행에 하나의 사용자 UPN을 추가 합니다.

![관리 단위에서 대량 사용자 제거를 위해 CSV 파일 편집](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

파일에 항목을 저장 한 후 파일을 업로드 하 고 **제출**을 선택 합니다.

![대량 업로드 파일 제출](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](roles-admin-units-assign-roles.md)
- [관리 단위에 그룹 추가](roles-admin-units-add-manage-groups.md)
