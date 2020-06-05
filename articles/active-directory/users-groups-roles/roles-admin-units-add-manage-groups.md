---
title: 관리 단위에서 그룹 추가, 제거 및 나열(미리 보기) - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 관리 단위에서 그룹 및 역할 권한 관리
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
ms.openlocfilehash: 2547a0fdbfbcc143ff822de333b41198f469375c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649327"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Azure Active Directory의 관리 단위에서 그룹 추가 및 관리

Azure AD(Azure Active Directory)에서는 AU(관리 단위)에 그룹을 추가하여 관리 범위를 더 상세하게 제어할 수 있습니다.

PowerShell 및 Microsoft Graph를 관리 단위에 사용하기 위해 준비하는 단계는 [시작](roles-admin-units-manage.md#get-started)을 참조하세요.

## <a name="add-groups-to-an-au"></a>AU에 그룹 추가

### <a name="azure-portal"></a>Azure portal

미리 보기에서는 관리 단위에 개별적으로 그룹을 할당할 수 있습니다. 그룹을 대량으로 관리 단위에 할당하는 옵션은 없습니다. 포털에서 다음 두 방법 중 하나로 관리 단위에 그룹을 할당할 수 있습니다.

1. **Azure AD > 그룹** 페이지에서

    Azure AD에서 그룹 개요 페이지를 열고 관리 단위에 할당해야 하는 그룹을 선택합니다. 왼쪽에서 **관리 단위**를 선택하여 그룹이 할당된 관리 단위를 나열합니다. 맨 위에 있는 관리 단위에 할당 옵션을 찾아 클릭하면 오른쪽에 관리 단위를 선택하는 패널이 표시됩니다.

    ![관리 단위에 개별적으로 그룹 할당](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. **Azure AD > 관리 단위 > 모든 그룹** 페이지에서

    Azure AD의 모든 그룹 블레이드 > 관리 단위를 엽니다. 이미 관리 단위에 할당된 그룹이 있으면 오른쪽에 표시됩니다. 맨 위의 **추가**를 클릭하면 오른쪽 패널에 해당 Azure AD 조직에서 사용 가능한 그룹이 슬라이드 방식으로 나열됩니다. 관리 단위에 할당할 그룹을 하나 이상 선택합니다.

    ![관리 단위를 선택한 다음, 구성원 추가](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

이 예제에서는 cmdlet Add-AzureADAdministrativeUnitMember를 사용하여 그룹을 관리 단위에 추가합니다. 관리 단위의 개체 ID와 추가할 그룹의 개체 ID를 인수로 취합니다. 강조 표시된 섹션은 특정 환경의 요구에 따라 달라질 수 있습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

예제:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>AU에서 그룹 나열

### <a name="azure-portal"></a>Azure portal

포털에서 **Azure AD > 관리 단위**로 이동합니다. 사용자를 나열할 관리 단위를 선택합니다. 기본적으로 왼쪽 패널에 **모든 사용자**가 이미 선택되어 있습니다. 오른쪽에서 **모든 그룹**을 선택하면 선택한 관리 단위의 멤버인 그룹의 목록이 표시됩니다.

![관리 단위에 그룹 나열](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

관리 단위의 모든 멤버를 가져올 수 있습니다. 관리 단위의 멤버인 모든 그룹을 표시하려면 아래 코드 조각을 사용할 수 있습니다.

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>그룹에 대한 AU 나열

### <a name="azure-portal"></a>Azure portal

Azure AD 포털에서 **그룹**을 열어 그룹의 세부 정보를 열 수 있습니다. 그룹의 프로필을 열 그룹을 선택합니다. **관리 단위**를 선택하여 그룹이 멤버인 모든 관리 단위를 나열합니다.

![그룹에 대한 관리 단위 나열](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>AU에서 그룹 제거

### <a name="azure-portal"></a>Azure portal

두 가지 방법으로 Azure Portal의 관리 단위에서 그룹을 제거할 수 있습니다.

**Azure AD** > **그룹**을 열고 관리 단위에서 제거하려는 그룹의 프로필을 엽니다. 왼쪽 패널에서 **관리 단위**를 선택하여 그룹이 멤버인 모든 관리 단위를 나열합니다. 그룹에서 제거하려는 관리 단위를 선택한 다음, **관리 단위에서 제거**를 선택합니다.

![관리 단위에서 그룹 제거](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

또는 **Azure AD** > **관리 단위**로 이동하고 그룹이 멤버인 관리 단위를 선택합니다. 왼쪽 패널에서 **그룹**을 선택하여 멤버 그룹을 나열합니다. 관리 단위에서 제거할 그룹을 선택한 다음, **그룹 제거**를 선택합니다.

![관리 단위에 그룹 나열](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](roles-admin-units-assign-roles.md)
- [관리 단위에서 사용 관리](roles-admin-units-add-manage-users.md)
