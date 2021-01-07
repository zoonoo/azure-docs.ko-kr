---
title: 관리 단위에서 그룹 추가, 제거 및 나열-Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 관리 단위에서 그룹 및 해당 역할 권한을 관리 합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 092604429cf5a527d7ee62b412e879ad9991eace
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394750"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory에서 관리 단위의 그룹 추가 및 관리

Azure Active Directory (Azure AD)에서 관리 단위에 그룹을 추가 하 여 보다 세부적인 관리 범위를 제어할 수 있습니다.

PowerShell을 사용 하 고 관리 장치 관리에 Microsoft Graph을 준비 하려면 [시작](admin-units-manage.md#get-started)을 참조 하세요.

## <a name="add-groups-to-an-administrative-unit"></a>관리 단위에 그룹 추가

Azure Portal, PowerShell 또는 Microsoft Graph를 사용 하 여 관리 단위에 그룹을 추가할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

관리 단위에는 개별 그룹만 할당할 수 있습니다. 그룹을 대량 작업으로 할당할 수 있는 옵션은 없습니다. Azure Portal에서 다음 두 가지 방법 중 하나로 관리 단위에 그룹을 할당할 수 있습니다.

* **그룹** 창에서 다음을 수행 합니다.

  1. Azure Portal에서 **AZURE AD** 로 이동 합니다.
  1. **그룹** 을 선택 하 고 관리 단위에 할당 하려는 그룹을 선택 합니다. 
  1. 왼쪽 창에서 **관리 단위** 를 선택 하 여 그룹이 할당 된 관리 단위의 목록을 표시 합니다. 

     !["관리 단위" 창에서 "관리 단위에 할당" 링크의 스크린샷](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. **관리 단위에 할당을** 선택 합니다.
  1. 오른쪽 창에서 관리 단위를 선택 합니다.

* **관리 단위**  >  **모든 그룹** 창에서 다음을 수행 합니다.

  1. Azure Portal에서 **AZURE AD** 로 이동 합니다.
  
  1. 왼쪽 창에서 **관리 단위** 를 선택한 다음 **모든 그룹** 을 선택 합니다. 
     관리 단위에 이미 할당 된 모든 그룹은 오른쪽 창에 표시 됩니다. 

  1. **그룹** 창에서 **추가** 를 선택 합니다.
    오른쪽 창에는 Azure AD 조직에서 사용 가능한 모든 그룹이 나열 됩니다. 

     ![관리 단위에 그룹을 추가 하기 위한 "추가" 단추의 스크린샷](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. 관리 단위에 할당할 그룹을 하나 이상 선택한 다음 **선택** 단추를 선택 합니다.

### <a name="use-powershell"></a>PowerShell 사용

다음 예에서는 cmdlet을 사용 `Add-AzureADMSAdministrativeUnitMember` 하 여 관리 단위에 그룹을 추가 합니다. 관리 단위의 개체 ID와 추가할 그룹의 개체 ID는 인수로 사용 됩니다. 강조 표시 된 섹션을 특정 환경에 필요한 대로 변경 합니다.


```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

다음 명령을 실행합니다.

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{id}"
}
```

예제:

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>관리 단위에서 그룹 목록 보기

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure Portal에서 **AZURE AD** 로 이동 합니다.

1. 왼쪽 창에서 **관리 단위** 를 선택 하 고 보려는 그룹의 관리 단위를 선택 합니다. 기본적으로 **모든 사용자** 가 왼쪽 창에서 선택 됩니다. 

1. 왼쪽 창에서 **그룹** 을 선택 합니다. 오른쪽 창에는 선택한 관리 단위의 구성원 인 그룹의 목록이 표시 됩니다.

   ![관리 단위의 그룹 목록을 표시 하는 "그룹" 창의 스크린샷](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell 사용

관리 단위의 모든 구성원 목록을 표시 하려면 다음 명령을 실행 합니다. 

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

관리 단위의 구성원 인 모든 그룹을 표시 하려면 다음 코드 조각을 사용 합니다.

```http
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

다음 명령을 실행합니다.

```http
HTTP request
GET /directory/administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>그룹에 대 한 관리 단위 목록 보기

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure Portal에서 **AZURE AD** 로 이동 합니다.

1. 왼쪽 창 **에서 그룹을 선택 하** 여 그룹 목록을 표시 합니다.

1. 그룹의 프로필을 열 그룹을 선택합니다. 

1. 왼쪽 창에서 **관리 단위** 를 선택 하 여 그룹이 구성원 인 모든 관리 단위를 나열 합니다.

   ![그룹이 할당 된 목록 관리 단위를 표시 하는 "관리 단위" 창의 스크린샷](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>PowerShell 사용

다음 명령을 실행합니다.

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

다음 명령을 실행합니다.

```http
https://graph.microsoft.com/v1.0/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>관리 단위에서 그룹 제거

### <a name="use-the-azure-portal"></a>Azure Portal 사용

다음 두 가지 방법 중 하나로 Azure Portal의 관리 단위에서 그룹을 제거할 수 있습니다.

- 그룹 개요에서 제거 합니다.

  1. Azure Portal에서 **AZURE AD** 로 이동 합니다.
  1. 왼쪽 창에서 **그룹** 을 선택 하 고 관리 단위에서 제거 하려는 그룹의 프로필을 엽니다.
  1. 왼쪽 창에서 **관리 단위** 를 선택 하 여 그룹이 할당 된 모든 관리 단위를 나열 합니다. 
  1. 그룹에서 제거하려는 관리 단위를 선택한 다음, **관리 단위에서 제거** 를 선택합니다.

     ![선택한 관리 단위에 할당 된 그룹의 목록을 표시 하는 "관리 단위" 창의 스크린샷](./media/admin-units-add-manage-groups/group-au-remove.png)

- 관리 단위에서 제거:

  1. Azure Portal에서 **AZURE AD** 로 이동 합니다.
  1. 왼쪽 창에서 **관리 단위** 를 선택 하 고 그룹이 할당 된 관리 단위를 선택 합니다.
  1. 왼쪽 창에서 **그룹** 을 선택 하 여 관리 단위에 할당 된 모든 그룹을 나열 합니다.
  1. 제거할 그룹을 선택 하 고 **그룹 제거** 를 선택 합니다.

    ![관리 단위의 그룹 목록을 표시 하는 "그룹" 창의 스크린샷](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell 사용

다음 명령을 실행합니다.

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

다음 명령을 실행합니다.

```http
https://graph.microsoft.com/v1.0/directory/AdministrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](admin-units-assign-roles.md)
- [관리 단위에서 사용 관리](admin-units-add-manage-users.md)
