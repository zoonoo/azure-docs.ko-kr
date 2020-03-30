---
title: Azure AD 권한 관리 - Azure Active Directory에서 액세스 패키지에 대한 할당보기, 추가 및 제거
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 할당을 보고 추가 및 제거하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262023"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 할당보기, 추가 및 제거

Azure AD 권한 관리에서 패키지, 해당 정책 및 상태에 액세스하도록 할당된 사람을 확인할 수 있습니다. 액세스 패키지에 적절한 정책이 있는 경우 액세스 패키지에 사용자를 직접 할당할 수도 있습니다. 이 문서에서는 액세스 패키지에 대한 할당을 보고, 추가하고, 제거하는 방법을 설명합니다.

## <a name="view-who-has-an-assignment"></a>과제에 있는 사람 보기

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. **과제를** 클릭하여 활성 과제 목록을 확인합니다.

    ![액세스 패키지에 대한 할당 목록](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 특정 과제를 클릭하여 추가 세부 정보를 확인합니다.

1. 모든 리소스 역할이 제대로 프로비전되지 않은 할당 목록을 보려면 필터 상태를 클릭하고 **전달을**선택합니다.

    **요청** 페이지에서 사용자의 해당 요청을 찾아 배달 오류에 대한 추가 세부 정보를 볼 수 있습니다.

1. 만료된 할당을 보려면 필터 상태를 클릭하고 만료됨 을 **선택합니다.**

1. 필터링된 목록의 CSV 파일을 다운로드하려면 **다운로드**를 클릭합니다.

### <a name="viewing-assignments-programmatically"></a>프로그래밍 방식으로 과제 보기

Microsoft Graph를 사용하여 액세스 패키지에서 할당을 검색할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램이 있는 적절한 역할의 사용자는 API를 호출하여 [accessPackageAssignments를 나열할](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)수 있습니다.

## <a name="directly-assign-a-user"></a>사용자 직접 할당

경우에 따라 사용자가 액세스 패키지를 요청하는 프로세스를 거치지 않도록 액세스 패키지에 특정 사용자를 직접 할당할 수 있습니다. 사용자를 직접 할당하려면 액세스 패키지에 관리자가 직접 할당할 수 있는 정책이 있어야 합니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 과제 를 **클릭합니다.**

1. **새 할당을** 클릭하여 패키지에 액세스할 사용자 추가를 엽니다.

    ![과제 - 사용자 추가하여 패키지 액세스](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. **사용자 추가를** 클릭하여 이 액세스 패키지를 할당할 사용자를 선택합니다.

1. 정책 **선택** 목록에서 사용자의 향후 요청 및 수명 주기가 관리되고 추적되는 정책을 선택합니다. 선택한 사용자에게 다른 정책 설정을 사용하려면 새 **정책 만들기를** 클릭하여 새 정책을 추가할 수 있습니다.

1. 선택한 사용자의 할당을 시작하고 종료할 날짜와 시간을 설정합니다. 종료 날짜를 제공하지 않으면 정책의 수명 주기 설정이 사용됩니다.

1. 선택적으로 레코드 유지를 위한 직접 할당에 대한 근거를 제공합니다.

1. **추가를** 클릭하여 선택한 사용자를 액세스 패키지에 직접 할당합니다.

    잠시 후 새로 **고침을** 클릭하여 과제 목록에서 사용자를 확인합니다.

### <a name="directly-assigning-users-programmatically"></a>프로그래밍 방식으로 사용자를 직접 할당

Microsoft Graph를 사용하여 액세스 패키지에 사용자를 직접 할당할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램이 있는 적절한 역할의 사용자는 API를 호출하여 [accessPackageAssignment Request](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)를 만들 수 있습니다.

## <a name="remove-an-assignment"></a>과제 제거

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 과제 를 **클릭합니다.**
 
1. 액세스 패키지에서 제거할 할당이 있는 사용자 옆에 있는 확인란을 클릭합니다. 

1. 왼쪽 창 상단 근처의 **제거** 단추를 클릭합니다. 
 
    ![할당 - 액세스 패키지에서 사용자 제거](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    할당이 제거되었음을 알리는 알림이 나타납니다. 

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 요청 및 설정 변경](entitlement-management-access-package-request-policy.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)
