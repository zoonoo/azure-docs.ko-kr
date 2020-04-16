---
title: 관리 단위에 사용자를 추가, 제거 및 나열(미리 보기) - Azure Active Directory | 마이크로 소프트 문서
description: Azure Active Directory의 관리 단위에서 사용자 및 해당 역할 권한 관리
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
ms.openlocfilehash: 92d96abd343266372984918fd0c942ec1cae865f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428304"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory의 관리 단위에 사용자 추가 및 관리

Azure Active Directory(Azure AD)에서 보다 세분화된 관리 범위의 제어를 위해 사용자를 관리 단위(AU)에 추가할 수 있습니다.

관리 단위 관리를 위해 PowerShell 및 Microsoft 그래프를 사용할 준비를 준비하는 단계는 [시작 하기](roles-admin-units-manage.md#getting-started)를 참조하십시오.

## <a name="add-users-to-an-au"></a>AU에 사용자 추가

### <a name="azure-portal"></a>Azure portal

두 가지 방법으로 사용자를 관리 단위에 할당할 수 있습니다.

1. 개별 과제

    1. 포털에서 Azure AD로 이동하여 사용자를 선택하고 관리 장치에 할당할 사용자를 선택할 수 있습니다. 그런 다음 왼쪽 패널에서 관리 단위를 선택할 수 있습니다. 사용자는 관리 단위에 할당을 클릭하고 사용자가 할당될 관리 단위를 선택하여 하나 이상의 관리 단위에 할당할 수 있습니다.

       ![추가를 선택한 다음 관리 단위의 이름을 입력합니다.](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. 포털에서 Azure AD로 이동하여 왼쪽 창에서 관리 단위를 선택한 다음 사용자가 할당할 관리 단위를 선택할 수 있습니다. 왼쪽 창에서 모든 사용자를 선택한 다음 멤버 추가를 선택합니다. 그런 다음 오른쪽 창에서 관리 단위에 할당할 사용자를 하나 이상 선택할 수 있습니다.

        ![관리 단위를 선택한 다음 구성원 추가를 선택합니다.](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. 대량 할당

    포털에서 Azure AD로 이동하여 관리 단위를 선택합니다. 사용자를 추가할 관리 단위를 선택합니다. 모든 사용자 -> .csv 파일에서 멤버 추가를 클릭하여 진행합니다. 그런 다음 CSV 템플릿을 다운로드하고 파일을 편집할 수 있습니다. 형식은 간단하며 각 줄에 단일 UPN을 추가해야 합니다. 파일이 준비되면 적절한 위치에 저장한 다음 스냅샷에서 강조 표시된 대로 3단계에서 업로드합니다.

    ![관리 단위에 사용자를 대량 할당](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $UserObj.ObjectId

위의 예에서 cmdlet 추가-AzureAD관리UnitMember 관리 단위에 사용자를 추가 하는 데 사용 됩니다. 사용자를 추가할 관리 단위의 개체 ID와 추가할 사용자의 개체 ID가 인수로 사용됩니다. 강조 표시된 섹션은 특정 환경에 필요에 따라 변경될 수 있습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

예제:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>사용자에 대한 관리 단위 목록

### <a name="azure-portal"></a>Azure portal

Azure 포털에서 Azure AD > 사용자로 이동하여 사용자 프로필을 열 수 있습니다. 사용자를 클릭하여 사용자 프로필을 엽니다.

![Azure Active 디렉터리에서 사용자 프로필 열기](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

왼쪽 패널에서 **관리 단위를** 선택하여 사용자가 할당된 관리 단위 목록을 확인합니다.

![사용자의 관리 단위 나열](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>AU에서 단일 사용자 제거

### <a name="azure-portal"></a>Azure portal

관리 단위에서 사용자를 제거할 수 있는 방법에는 두 가지가 있습니다. Azure 포털에서 **Azure AD** > **사용자로**이동하여 사용자 프로필을 열 수 있습니다. 사용자 프로필을 열 사용자를 선택합니다. 사용자를 제거할 관리 단위를 선택하고 관리 장치에서 **제거를**선택합니다.

![사용자 프로필에서 관리 장치에서 사용자 제거](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

사용자를 제거할 관리 단위를 선택하여 **Azure AD** > **관리 단위에서** 사용자를 제거할 수도 있습니다. 사용자를 선택하고 **멤버 제거를**선택합니다.
  
![관리 단위 수준에서 사용자 제거](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>두 명 이상의 사용자를 일괄 제거

Azure AD > 관리 단위로 이동하여 사용자를 제거할 관리 단위를 선택할 수 있습니다. 벌크 제거 멤버를 클릭합니다. 제거할 사용자 목록을 제공하기 위해 CSV 템플릿을 다운로드합니다.

다운로드한 CSV 템플릿을 관련 사용자 항목으로 편집합니다. 템플릿의 처음 두 행을 제거하지 마십시오. 각 행에 하나의 사용자 UPN을 추가합니다.

![관리 단위에서 대량 사용자 제거를 위해 CSV 파일 편집](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

파일에 항목을 저장한 후 파일을 업로드하고 **제출을**선택합니다.

![대량 업로드 파일 제출](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](roles-admin-units-assign-roles.md)
- [관리 단위에 그룹 추가](roles-admin-units-add-manage-groups.md)
