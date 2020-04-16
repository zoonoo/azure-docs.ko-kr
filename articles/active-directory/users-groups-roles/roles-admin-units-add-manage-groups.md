---
title: 관리 단위에 그룹 추가, 제거 및 목록(미리 보기) - Azure Active Directory | 마이크로 소프트 문서
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
ms.openlocfilehash: 78f45d9e1ca5b8237779fe096154c83749aa08fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428434"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Azure Active Directory에서 관리 단위로 그룹 추가 및 관리

Azure Active Directory(Azure AD)에서 보다 세분화된 관리 범위의 제어를 위해 관리 단위(AU)에 그룹을 추가할 수 있습니다.

관리 단위 관리를 위해 PowerShell 및 Microsoft 그래프를 사용할 준비를 준비하는 단계는 [시작 하기](roles-admin-units-manage.md#getting-started)를 참조하십시오.

## <a name="add-groups-to-an-au"></a>AU에 그룹 추가

### <a name="azure-portal"></a>Azure portal

미리 보기에서 관리 단위에 개별적으로만 그룹을 할당할 수 있습니다. 관리 단위에 그룹을 대량 으로 할당하는 옵션은 없습니다. 포털에서 다음 두 가지 방법 중 하나로 관리 단위에 그룹을 할당할 수 있습니다.

1. Azure **AD > 그룹** 페이지에서

    Azure AD에서 그룹 개요 페이지를 열고 관리 장치에 할당해야 하는 그룹을 선택합니다. 왼쪽에서 **관리 단위를** 선택하여 그룹이 할당된 관리 단위를 나열합니다. 상단에 당신은 옵션을 찾을 수 있습니다 관리 단위에 할당하고 그것을 클릭하면 관리 단위를 선택하는 오른쪽에 패널을 줄 것이다.

    ![관리 단위에 개별적으로 그룹 할당](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Azure **AD > 모든 그룹** > 관리 단위에서

    Azure AD > 관리 단위에서 모든 그룹 블레이드를 엽니다. 관리 단위에 이미 할당된 그룹이 있는 경우 오른쪽에 표시됩니다. 상단에 **추가를** 선택하면 오른쪽 패널이 Azure AD 조직에서 사용할 수 있는 그룹을 나열합니다. 관리 단위에 할당할 그룹을 하나 이상 선택합니다.

    ![관리 단위를 선택한 다음 구성원 추가를 선택합니다.](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

이 예제에서는 cmdlet 추가 AzureAD관리UnitMember 관리 단위에 그룹을 추가 하는 데 사용 됩니다. 관리 단위의 개체 ID와 추가할 그룹의 개체 ID는 인수로 간주됩니다. 강조 표시된 섹션은 특정 환경에 필요에 따라 변경될 수 있습니다.

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

## <a name="list-groups-in-an-au"></a>AU의 그룹 목록

### <a name="azure-portal"></a>Azure portal

포털의 **Azure AD > 관리 단위로** 이동합니다. 사용자를 나열할 관리 단위를 선택합니다. 기본적으로 **모든 사용자가** 왼쪽 패널에 이미 선택되어 있습니다. **모든 그룹을** 선택하면 오른쪽에 선택한 관리 단위의 구성원인 그룹 목록이 표시됩니다.

![삭제할 관리 단위 선택](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

이렇게 하면 관리 단위의 모든 구성원을 얻는 데 도움이 됩니다. 관리 단위의 구성원인 모든 그룹을 표시하려면 아래 코드 조각을 사용할 수 있습니다.

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

## <a name="list-aus-for-a-group"></a>그룹에 대한 AUS 나열

### <a name="azure-portal"></a>Azure portal

Azure AD 포털에서 **그룹을**열어 그룹의 세부 정보를 열 수 있습니다. 그룹을 선택하여 그룹 프로필을 엽니다. **관리 단위를** 선택하여 그룹이 구성원인 모든 관리 단위를 나열합니다.

![그룹에 대한 관리 단위 나열](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>AU에서 그룹 제거

### <a name="azure-portal"></a>Azure portal

Azure 포털의 관리 단위에서 그룹을 제거할 수 있는 방법에는 두 가지가 있습니다.

**Azure AD** > **그룹을** 열고 관리 단위에서 제거할 그룹에 대한 프로필을 엽니다. 왼쪽 패널에서 **관리 단위를** 선택하여 그룹이 구성원인 모든 관리 단위를 나열합니다. 그룹에서 제거할 관리 단위를 선택한 다음 **관리 단위에서 제거를**선택합니다.

![관리 단위에서 그룹 제거](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

또는 **Azure AD** > **관리 단위로** 이동하여 그룹이 구성원인 관리 단위를 선택할 수 있습니다. 왼쪽 패널에서 **그룹을** 선택하여 구성원 그룹을 나열합니다. 관리 단위에서 제거할 그룹을 선택한 다음 **그룹 제거를**선택합니다.

![관리 단위의 그룹 목록](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](roles-admin-units-assign-roles.md)
- [관리 단위의 사용자 관리](roles-admin-units-add-manage-users.md)
