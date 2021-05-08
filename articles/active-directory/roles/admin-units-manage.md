---
title: 관리 단위 추가 및 제거 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 관리 단위를 사용하여 역할 권한 범위를 제한할 수 있습니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0706fad1e5340625c32eab691ac3e4d58eeafc9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012121"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Azure Active Directory의 관리 단위 관리

Azure Active Directory(Azure AD)의 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위로 제한되는 범위를 사용하여 Azure AD 역할에 사용자를 할당할 수 있습니다.

## <a name="get-started"></a>시작

1. [Graph 탐색기](https://aka.ms/ge)를 통해 다음 안내에서 쿼리를 실행하려면 다음을 수행합니다.

    a. Azure Portal에서 Azure AD로 이동합니다. 
    
    b. 애플리케이션 목록에서 **Graph 탐색기** 를 선택합니다.
    
    다. **권한** 창에서 **Graph 탐색기에 대한 관리자 동의 허용** 을 선택합니다.

    ![“Graph 탐색기에 대한 관리자 동의 허용” 링크를 보여 주는 스크린샷.](./media/admin-units-manage/select-graph-explorer.png)


1. [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/)을 사용합니다.

## <a name="add-an-administrative-unit"></a>관리 단위 추가

Azure Portal 또는 PowerShell을 사용하여 관리 단위를 추가할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure Portal에서 Azure AD로 이동합니다. 그런 다음 왼쪽 창에서 **관리 단위** 를 선택합니다.

    ![Azure AD의 “관리 단위” 링크 스크린샷.](./media/admin-units-manage/nav-to-admin-units.png)

1. 창 상단에 있는 **추가** 단추를 선택한 다음 **이름** 란에 관리 단위의 이름을 입력합니다. 필요에 따라 관리 단위에 대한 설명을 추가합니다.

    ![추가 단추와 관리 단위의 이름을 입력하는 이름 란을 보여 주는 스크린샷.](./media/admin-units-manage/add-new-admin-unit.png)

1. 파란색 **추가** 단추를 선택하여 관리 단위를 확정합니다.

### <a name="use-powershell"></a>PowerShell 사용

다음 명령을 실행하기 전에 [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/)을 설치합니다.

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

필요에 따라 따옴표 안의 값을 수정할 수 있습니다.

### <a name="use-microsoft-graph"></a>Microsoft Graph 사용

요청

```http
POST /administrativeUnits
```

본문

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>관리 단위 제거

Azure AD에서 관리 역할의 범위 단위로 더 이상 필요하지 않은 관리 단위를 제거할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure Portal에서 **Azure AD** 로 이동한 다음 **관리 단위** 를 선택합니다. 
1. 삭제할 관리 단위를 선택하고 **삭제** 를 선택합니다. 
1. 관리 단위 삭제를 확인하려면 **예** 를 선택합니다. 관리 단위가 삭제됩니다.

![관리 단위 삭제 단추 및 확인 창 스크린샷.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell 사용

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $adminUnitObj.ObjectId
```

특정 환경의 필요에 따라 따옴표 안의 값을 수정할 수 있습니다.

### <a name="use-the-graph-api"></a>Graph API 사용

요청

```http
DELETE /administrativeUnits/{admin-unit-id}
```

본문

```http
{}
```

## <a name="next-steps"></a>다음 단계

* [관리 단위에서 사용 관리](admin-units-add-manage-users.md)
* [관리 단위에 속한 그룹 관리](admin-units-add-manage-groups.md)
