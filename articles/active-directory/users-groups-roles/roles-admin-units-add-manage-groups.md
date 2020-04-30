---
title: 관리 단위 (미리 보기)에서 그룹 추가, 제거 및 나열-Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 관리 단위에서 그룹 및 해당 역할 권한 관리
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
ms.openlocfilehash: 1f0b0c0a7b666aed56ad24625c80c0a57683b998
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683267"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Azure Active Directory의 관리 단위에서 그룹 추가 및 관리

Azure Active Directory (Azure AD)에서 관리 단위 (AU)에 그룹을 추가 하 여 제어의 보다 세분화 된 관리 범위를 만들 수 있습니다.

PowerShell 사용을 준비 하 고 관리 장치 관리를 Microsoft Graph 하는 단계는 [시작](roles-admin-units-manage.md#get-started)을 참조 하세요.

## <a name="add-groups-to-an-au"></a>AU에 그룹 추가

### <a name="azure-portal"></a>Azure portal

미리 보기에서는 관리 단위에만 그룹을 개별적으로 할당할 수 있습니다. 관리 단위에 그룹을 대량으로 할당할 수 있는 옵션은 없습니다. 포털에서 다음 두 가지 방법 중 하나를 수행 하 여 관리 단위에 그룹을 할당할 수 있습니다.

1. **AZURE AD > 그룹** 페이지에서

    Azure AD에서 그룹 개요 페이지를 열고 관리 단위에 할당 해야 하는 그룹을 선택 합니다. 왼쪽에서 **관리 단위** 를 선택 하 여 그룹이 할당 된 관리 단위를 나열 합니다. 위쪽에서 관리 단위에 할당 옵션을 찾은 다음이 옵션을 클릭 하면 오른쪽에 관리 단위를 선택할 수 있는 패널이 제공 됩니다.

    ![관리 단위에 개별적으로 그룹 할당](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. **AZURE AD > 관리 단위 > 모든 그룹** 페이지에서

    Azure AD > 관리 단위에서 모든 그룹 블레이드를 엽니다. 관리 단위에 이미 할당 된 그룹이 있는 경우 오른쪽에 표시 됩니다. 위쪽에서 **추가** 를 선택 하면 Azure AD 조직에서 사용할 수 있는 그룹이 나열 될 때 오른쪽 패널이 표시 됩니다. 관리 단위에 할당할 그룹을 하나 이상 선택 합니다.

    ![관리 단위를 선택한 후 구성원 추가를 선택 합니다.](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

이 예제에서 cmdlet AzureADAdministrativeUnitMember는 관리 단위에 그룹을 추가 하는 데 사용 됩니다. 관리 단위의 개체 ID와 추가할 그룹의 개체 ID는 인수로 사용 됩니다. 강조 표시 된 섹션은 특정 환경에 필요한 대로 변경할 수 있습니다.

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

## <a name="list-groups-in-an-au"></a>AU의 그룹 나열

### <a name="azure-portal"></a>Azure portal

포털에서 **AZURE AD > 관리 단위로** 이동 합니다. 사용자를 나열 하려는 관리 단위를 선택 합니다. 기본적으로 왼쪽 패널에는 **모든 사용자** 가 이미 선택 되어 있습니다. **모든 그룹** 을 선택 하 고 오른쪽에서 선택한 관리 단위의 구성원 인 그룹 목록을 찾습니다.

![삭제할 관리 단위 선택](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

이렇게 하면 관리 단위의 모든 구성원을 가져오는 데 도움이 됩니다. 관리 단위의 구성원 인 모든 그룹을 표시 하려면 아래 코드 조각을 사용할 수 있습니다.

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

## <a name="list-aus-for-a-group"></a>그룹에 대 한 오스트레일리아 나열

### <a name="azure-portal"></a>Azure portal

Azure AD 포털 **에서 그룹을**열어 그룹의 세부 정보를 열 수 있습니다. 그룹을 선택 하 여 그룹의 프로필을 엽니다. 그룹이 멤버인 모든 관리 단위를 나열 하려면 **관리 단위** 를 선택 합니다.

![그룹에 대 한 관리 단위 나열](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>AU에서 그룹 제거

### <a name="azure-portal"></a>Azure portal

Azure Portal의 관리 단위에서 그룹을 제거 하는 방법에는 두 가지가 있습니다.

**Azure AD** > **그룹** 을 열고 관리 단위에서 제거 하려는 그룹의 프로필을 엽니다. 왼쪽 패널에서 **관리 단위** 를 선택 하 여 그룹이 구성원 인 모든 관리 단위를 나열 합니다. 그룹을 제거 하려는 관리 단위를 선택 하 고 **관리 단위에서 제거**를 선택 합니다.

![관리 단위에서 그룹 제거](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

또는 **Azure AD** > **관리 단위로** 이동 하 여 그룹이 구성원 인 관리 단위를 선택할 수 있습니다. 왼쪽 패널에서 **그룹** 을 선택 하 여 멤버 그룹을 나열 합니다. 관리 단위에서 제거할 그룹을 선택 하 고 **그룹 제거**를 선택 합니다.

![관리 단위의 그룹 나열](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](roles-admin-units-assign-roles.md)
- [관리 단위의 사용자 관리](roles-admin-units-add-manage-users.md)
