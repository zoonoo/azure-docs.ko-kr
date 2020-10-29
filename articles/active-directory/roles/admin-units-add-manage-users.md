---
title: 관리 단위에서 사용자 추가, 제거 및 나열-Azure Active Directory | Microsoft Docs
description: Azure Active Directory 관리 단위에서 사용자 및 해당 역할 권한 관리
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
ms.openlocfilehash: c877878fe25d4c6c8904840c3c3350fbe2acf7b5
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026669"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory 관리 단위에서 사용자 추가 및 관리

Azure Active Directory (Azure AD)에서 관리 단위에 사용자를 추가 하 여 보다 세부적인 관리 범위를 제어할 수 있습니다.

PowerShell을 사용 하 고 관리 장치 관리에 Microsoft Graph을 준비 하려면 [시작](admin-units-manage.md#get-started)을 참조 하세요.

## <a name="add-users-to-an-administrative-unit"></a>관리 단위에 사용자 추가

### <a name="use-the-azure-portal"></a>Azure Portal 사용

관리 단위에 사용자를 개별적으로 할당 하거나 대량 작업으로 할당할 수 있습니다.

- 사용자 프로필에서 개별 사용자를 할당 합니다.

   1. 권한 있는 역할 관리자 권한으로 [AZURE AD 관리 센터](https://portal.azure.com) 에 로그인 합니다.

   1. 사용자 **를 선택한 다음** 사용자의 프로필을 열려면 관리 단위에 할당할 사용자를 선택 합니다.
   
   1. **관리 단위** 를 선택 합니다. 
   
   1. 하나 이상의 관리 단위에 사용자를 할당 하려면 **관리 단위에 할당** 을 선택 하 고 오른쪽 창에서 사용자를 할당 하려는 관리 단위를 선택 합니다.

       ![관리 단위에 사용자를 할당 하기 위한 "관리 단위" 창의 스크린샷](./media/admin-units-add-manage-users/assign-users-individually.png)

- 관리 단위에서 개별 사용자를 할당 합니다.

   1. 권한 있는 역할 관리자 권한으로 [AZURE AD 관리 센터](https://portal.azure.com) 에 로그인 합니다.
   1. **관리 단위** 를 선택 하 고 사용자를 할당할 관리 단위를 선택 합니다.
   1. **모든 사용자** 를 선택 하 고 **구성원 추가** 를 선택한 다음 **구성원 추가** 창에서 관리 단위에 할당 하려는 사용자를 하나 이상 선택 합니다.

        ![관리 단위에 사용자를 할당 하기 위한 관리 단위 "사용자" 창의 스크린샷](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- 사용자를 대량 작업으로 할당:

   1. 권한 있는 역할 관리자 권한으로 [AZURE AD 관리 센터](https://portal.azure.com) 에 로그인 합니다.

   1. **관리 단위** 를 선택 합니다.

   1. 사용자를 추가 하려는 관리 단위를 선택 합니다.

   1. **사용자**  >  **일괄 작업**  >  **일괄 추가 멤버** 를 선택 합니다. 그런 다음 CSV (쉼표로 구분 된 값) 템플릿을 다운로드 하 고 파일을 편집할 수 있습니다. 형식은 단순 하 고 각 줄에 단일 사용자 계정 이름을 추가 해야 합니다. 파일이 준비 되 면 적절 한 위치에 저장 한 후이 단계의 일부로 업로드 합니다.

      ![관리 단위에 사용자를 대량 작업으로 할당 하는 "사용자" 창의 스크린샷](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>PowerShell 사용

PowerShell에서 `Add-AzureADAdministrativeUnitMember` 다음 예의 cmdlet을 사용 하 여 관리 단위에 사용자를 추가 합니다. 사용자를 추가할 관리 단위의 개체 ID와 추가 하려는 사용자의 개체 ID는 인수로 사용 됩니다. 강조 표시 된 섹션을 특정 환경에 필요한 대로 변경 합니다.

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

자리 표시자를 테스트 정보로 바꾸고 다음 명령을 실행 합니다.

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

예제:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>사용자의 관리 단위 목록 보기

### <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal에서 다음을 수행 하 여 사용자의 프로필을 열 수 있습니다.

1. **AZURE AD** 로 이동한 다음 **사용자** 를 선택 합니다.

1. 보려는 프로필의 사용자를 선택 합니다.

1. **관리 단위** 를 선택 하 여 사용자가 할당 된 관리 단위 목록을 표시 합니다.

   ![사용자가 할당 된 관리 단위의 스크린샷](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>PowerShell 사용

다음 명령을 실행합니다.

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
> [!NOTE]
> 기본적으로는 `Get-AzureADAdministrativeUnitMember` 관리 단위의 100 멤버만 반환 합니다. 더 많은 멤버를 검색 하기 위해를 추가할 수 있습니다 `"-All $true"` .

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

자리 표시자를 테스트 정보로 바꾸고 다음 명령을 실행 합니다.

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>관리 단위에서 단일 사용자 제거

### <a name="use-the-azure-portal"></a>Azure Portal 사용

다음 두 가지 방법 중 하나를 사용 하 여 관리 단위에서 사용자를 제거할 수 있습니다. 

* Azure Portal에서 **AZURE AD** 로 이동한 다음 **사용자** 를 선택 합니다. 
  1. 사용자를 선택 하 여 사용자의 프로필을 엽니다. 
  1. 사용자를 제거할 관리 단위를 선택 하 고 **관리 단위에서 제거** 를 선택 합니다.

     ![사용자의 프로필 창에서 관리 단위에서 사용자를 제거 하는 방법을 보여 주는 스크린샷](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Azure Portal에서 **AZURE AD** 로 이동한 다음 **관리 단위** 를 선택 합니다.
  1. 사용자를 제거 하려는 관리 단위를 선택 합니다. 
  1. 사용자를 선택한 후 **구성원 제거** 를 선택 합니다.
  
     ![관리 단위 수준에서 사용자를 제거 하는 방법을 보여 주는 스크린샷](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>PowerShell 사용

다음 명령을 실행합니다.

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

자리 표시자를 테스트 정보로 바꾸고 다음 명령을 실행 합니다.

`https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref`

## <a name="remove-multiple-users-as-a-bulk-operation"></a>대량 작업으로 여러 사용자 제거

관리 단위에서 여러 사용자를 제거 하려면 다음을 수행 합니다.

1. Azure Portal에서 **AZURE AD** 로 이동 합니다.

1. **관리 단위** 를 선택 하 고 사용자를 제거할 관리 단위를 선택 합니다. 

1. **구성원 대량 제거** 를 선택 하 고 제거할 사용자를 나열 하는 데 사용할 CSV 템플릿을 다운로드 합니다.

   !["사용자" 창에서 "멤버 대량 제거" 링크를 보여 주는 스크린샷](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. 다운로드 한 CSV 템플릿을 관련 사용자 항목으로 편집 합니다. 템플릿의 처음 두 행을 제거 하지 마십시오. 각 행에 UPN (사용자 계정 이름)을 하나 추가 합니다.

   ![대량으로 관리 단위에서 사용자를 제거 하는 편집 된 CSV 파일의 스크린샷](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. 변경 내용을 저장 하 고 파일을 업로드 한 다음 **제출** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](admin-units-assign-roles.md)
- [관리 단위에 그룹 추가](admin-units-add-manage-groups.md)
