---
title: 관리 단위 추가 및 제거(미리 보기) - Azure Active Directory | 마이크로 소프트 문서
description: 관리 단위를 사용하여 Azure Active Directory에서 역할 권한의 범위를 제한합니다.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684907"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Azure Active 디렉터리에서 관리 단위 관리

Azure Active Directory(Azure AD)에서 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위(AUS)로 제한되는 범위로 Azure AD 역할에 사용자를 할당할 수 있습니다.

## <a name="get-started"></a>시작하기

1. [그래프 탐색기를](https://aka.ms/ge)통해 다음 지침에서 쿼리를 실행하려면 다음을 수행합니다.

    a. Azure Portal에서 Azure AD로 이동합니다. 응용 프로그램 목록에서 **그래프 탐색기를**선택한 다음 **그래프 탐색기에 대한 관리자 동의를**선택합니다.

    !["관리자 동의 부여" 링크를 보여주는 스크린샷](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. 그래프 탐색기에서 **베타** 버전을 선택합니다.

    ![선택한 베타 버전을 보여주는 스크린샷](./media/roles-admin-units-manage/select-beta-version.png)

1. Azure AD PowerShell의 미리 보기 버전을 사용합니다.

## <a name="add-an-administrative-unit"></a>관리 단위 추가

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure 포털에서 Azure AD로 이동한 다음 왼쪽 창에서 **관리 단위를**선택합니다.

    ![Azure AD의 관리 단위(미리 보기) 링크의 스크린샷](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. **추가를** 선택한 다음 관리 단위의 이름을 입력합니다. 선택적으로 관리 단위에 대한 설명을 추가합니다.

    ![추가 단추의 스크린샷 및 관리 단위의 이름을 입력하기 위한 텍스트 상자](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. 관리 단위를 완료하려면 **추가를** 선택합니다.

### <a name="use-powershell"></a>PowerShell 사용

다음 명령을 실행하기 전에 Azure AD PowerShell(미리 보기)을 설치합니다.

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

필요에 따라 따옴표로 둘러싸인 값을 수정할 수 있습니다.

### <a name="use-microsoft-graph"></a>마이크로소프트 그래프 사용

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>관리 단위 제거

Azure AD에서 더 이상 관리 역할에 대한 범위 단위로 더 이상 필요하지 않은 관리 단위를 제거할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

1. Azure 포털에서 Azure **AD** > **관리 단위로**이동합니다. 
1. 삭제할 관리 단위를 선택한 다음 **삭제를**선택합니다. 
1. 관리 단위를 삭제할 지 확인하려면 **예**를 선택합니다. 관리 단위가 삭제됩니다.

![관리 단위의 스크린샷 삭제 버튼 및 확인 창](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell 사용

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

특정 환경에 필요에 따라 따옴표로 둘러싸인 값을 수정할 수 있습니다.

### <a name="use-the-graph-api"></a>그래프 API 사용

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>다음 단계

* [관리 단위의 사용자 관리](roles-admin-units-add-manage-users.md)
* [관리 단위의 그룹 관리](roles-admin-units-add-manage-groups.md)
