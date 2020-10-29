---
title: 관리 단위 추가 및 제거-Azure Active Directory | Microsoft Docs
description: 관리 단위를 사용 하 여 Azure Active Directory에서 역할 권한 범위를 제한할 수 있습니다.
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
ms.openlocfilehash: aa0abffc9bfb1a0c6511af331d1e8dbc10cff455
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026533"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Azure Active Directory에서 관리 단위 관리

Azure Active Directory (Azure AD)의 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위로 제한 되는 범위를 사용 하 여 Azure AD 역할에 사용자를 할당할 수 있습니다.

## <a name="get-started"></a>시작하기

1. [그래프 탐색기](https://aka.ms/ge)를 통해 다음 지침에서 쿼리를 실행 하려면 다음을 수행 합니다.

    a. Azure Portal에서 Azure AD로 이동합니다. 
    
    b. 응용 프로그램 목록에서 **그래프 탐색기** 를 선택 합니다.
    
    다. **사용 권한** 창에서 **그래프 탐색기에 대해 관리자 동의 부여** 를 선택 합니다.

    !["Graph explorer에 대 한 관리자 동의 부여" 링크를 보여 주는 스크린샷](./media/admin-units-manage/select-graph-explorer.png)


1. Azure AD PowerShell의 미리 보기 버전을 사용 합니다.

## <a name="add-an-administrative-unit"></a>관리 단위 추가

Azure Portal 또는 PowerShell을 사용 하 여 관리 단위를 추가할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure Portal에서 Azure AD로 이동합니다. 그런 다음 왼쪽 창에서 **관리 단위** 를 선택 합니다.

    ![Azure AD의 "관리 단위" 링크 스크린샷](./media/admin-units-manage/nav-to-admin-units.png)

1. 창의 위쪽에 있는 **추가** 단추를 선택한 다음 **이름** 상자에 관리 단위의 이름을 입력 합니다. 필요에 따라 관리 단위에 대 한 설명을 추가 합니다.

    ![추가 단추와 관리 단위의 이름을 입력 하기 위한 이름 상자를 보여 주는 스크린샷](./media/admin-units-manage/add-new-admin-unit.png)

1. 파란색 **추가** 단추를 선택 하 여 관리 단위를 마무리 합니다.

### <a name="use-powershell"></a>PowerShell 사용

다음 명령을 실행 하기 전에 Azure AD PowerShell (미리 보기)을 설치 합니다.

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

필요에 따라 따옴표로 묶인 값을 수정할 수 있습니다.

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>관리 단위 제거

Azure AD에서 관리 역할의 범위 단위로 더 이상 필요 하지 않은 관리 단위를 제거할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure Portal에서 **AZURE AD** 로 이동한 다음 **관리 단위** 를 선택 합니다. 
1. 삭제할 관리 단위를 선택 하 고 **삭제** 를 선택 합니다. 
1. 관리 단위 삭제를 확인 하려면 **예** 를 선택 합니다. 관리 단위가 삭제 됩니다.

![관리 단위 삭제 단추 및 확인 창의 스크린샷](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell 사용

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

특정 환경에 필요한 대로 따옴표로 묶인 값을 수정할 수 있습니다.

### <a name="use-the-graph-api"></a>Graph API 사용

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>다음 단계

* [관리 단위에서 사용 관리](admin-units-add-manage-users.md)
* [관리 단위의 그룹 관리](admin-units-add-manage-groups.md)
