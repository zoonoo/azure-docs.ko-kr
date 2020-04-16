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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428161"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Azure Active 디렉터리에서 관리 단위 관리

Azure Active Directory(Azure AD)에서 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위(AUS)로 제한된 범위의 Azure AD 역할에 사용자를 할당할 수 있습니다.

## <a name="getting-started"></a>시작

1. [그래프 탐색기를](https://aka.ms/ge)통해 다음 지침에서 쿼리를 실행하려면 다음을 확인하십시오.

    1. 포털에서 Azure AD로 이동한 다음 응용 프로그램에서 그래프 탐색기를 선택하고 그래프 탐색기에서 관리자 동의를 제공합니다.

        ![그래프 탐색기를 선택하고 이 페이지에서 관리자 동의를 제공합니다.](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. 그래프 탐색기에서 베타 버전을 선택해야 합니다.

        ![POST 작업 전에 베타 버전을 선택합니다.](./media/roles-admin-units-manage/select-beta-version.png)

1. Azure AD PowerShell의 미리 보기 버전을 사용하십시오. 자세한 지침은 여기를 참조하십시오.

## <a name="add-an-administrative-unit"></a>관리 단위 추가

### <a name="azure-portal"></a>Azure portal

1. 포털의 활성 디렉터리로 이동하여 왼쪽 패널에서 관리 단위를 선택합니다.

    ![Azure 활성 디렉터리에서 관리 단위로 이동](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. * **추가를**선택하고 관리 단위의 이름을 제공하고 선택적으로 관리 단위에 대한 설명을 추가할 수 있습니다.

    ![추가를 선택한 다음 관리 단위의 이름을 입력합니다.](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. 관리 단위를 완료하려면 **추가를** 선택합니다.

### <a name="powershell"></a>PowerShell

아래 작업을 수행하기 전에 Azure AD PowerShell(미리 보기 버전)을 설치합니다.

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

위에서 강조 표시된 값은 필요에 따라 수정할 수 있습니다.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>관리 단위 제거

Azure Active Directory(Azure AD)에서 더 이상 필요하지 않은 관리 단위를 관리 역할의 범위 단위로 제거할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

포털의 **Azure AD > 관리 단위로** 이동합니다. 삭제할 관리 단위를 선택한 다음 **삭제를**선택합니다. **예,** 관리 단위를 확인 하면 삭제 됩니다.

![삭제할 관리 단위 선택](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

강조 표시된 섹션은 특정 환경에 필요에 따라 변경될 수 있습니다.

### <a name="graph-api"></a>그래프 API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>다음 단계

[관리 단위의](roles-admin-units-add-manage-users.md)
사용자 관리 관리[그룹 관리 관리](roles-admin-units-add-manage-groups.md)
