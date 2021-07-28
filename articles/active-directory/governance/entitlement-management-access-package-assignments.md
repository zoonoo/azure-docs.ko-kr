---
title: Azure AD 권한 관리에서 액세스 패키지에 대한 할당 보기, 추가, 제거 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 할당을 확인, 추가, 제거하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 245781f22db75d27f335c0a81d0ee9793b076c47
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109713867"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 할당 보기, 추가, 제거

Azure AD 권한 관리에서 액세스 패키지에 할당된 사용자, 해당 정책, 상태를 확인할 수 있습니다. 액세스 패키지에 적절한 정책이 있는 경우 액세스 패키지에 직접 사용자를 할당할 수도 있습니다. 이 문서에서는 액세스 패키지에 대한 할당을 확인, 추가, 제거하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 권한 관리를 사용하고 액세스 패키지에 사용자를 할당하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5 라이선스

## <a name="view-who-has-an-assignment"></a>할당이 있는 사용자 보기

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. **할당** 을 클릭하여 활성 할당 목록을 확인합니다.

    ![액세스 패키지에 대한 할당 목록](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 특정 할당을 클릭하여 추가 세부 정보를 확인합니다.

1. 모든 리소스 역할이 제대로 프로비저닝되지 않은 할당 목록을 보려면 필터 상태를 클릭하고 **배달 중** 을 선택합니다.

    **요청** 페이지에서 사용자의 해당 요청을 찾아 배달 오류에 대한 추가 세부 정보를 확인할 수 있습니다.

1. 만료된 할당을 보려면 필터 상태를 클릭하고 **만료됨** 을 선택합니다.

1. 필터링된 목록의 CSV 파일을 다운로드하려면 **다운로드** 를 클릭합니다.

### <a name="viewing-assignments-programmatically"></a>프로그래밍 방식으로 할당 보기

Microsoft Graph를 사용하여 액세스 패키지에서 할당을 검색할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션을 사용하는 적절한 역할의 사용자는 [accessPackageAssignments 나열](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true) API를 호출할 수 있습니다.

## <a name="directly-assign-a-user"></a>사용자 직접 할당

경우에 따라 사용자가 액세스 패키지를 요청하는 프로세스를 진행할 필요가 없도록 특정 사용자를 액세스 패키지에 직접 할당할 수 있습니다. 사용자를 직접 할당하려면 액세스 패키지에 관리자 직접 할당을 허용하는 정책이 있어야 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당** 을 클릭합니다.

1. **새 할당** 을 클릭하여 액세스 패키지에 사용자 추가를 엽니다.

    ![할당 - 액세스 패키지에 사용자 추가](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. **사용자 추가** 를 클릭하여 이 액세스 패키지를 할당할 사용자를 선택합니다.

1. **정책 선택** 목록에서 사용자의 향후 요청 및 수명 주기를 관리하고 추적하는 데 사용할 정책을 선택합니다. 선택한 사용자에게 다른 정책 설정을 사용하려는 경우 **새 정책 만들기** 를 클릭하여 새 정책을 추가할 수 있습니다.

1. 선택한 사용자의 할당을 시작하고 종료할 날짜 및 시간을 설정합니다. 종료 날짜를 지정하지 않으면 정책의 수명 주기 설정이 사용됩니다.

1. 필요에 따라 레코드 보관을 위해 직접 할당의 사유를 제공합니다.

1. **추가** 를 클릭하여 선택한 사용자를 액세스 패키지에 직접 할당합니다.

    잠시 후에 **새로 고침** 을 클릭하여 할당 목록에서 사용자를 확인합니다.

### <a name="directly-assigning-users-programmatically"></a>프로그래밍 방식으로 사용자 직접 할당

Microsoft Graph를 사용하여 액세스 패키지에 사용자를 직접 할당할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션을 사용하는 적절한 역할의 사용자는 [accessPackageAssignmentRequest 만들기](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true) API를 호출할 수 있습니다.

## <a name="remove-an-assignment"></a>할당 제거

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당** 을 클릭합니다.
 
1. 액세스 패키지에서 할당을 제거할 사용자 옆에 있는 확인란을 클릭합니다. 

1. 왼쪽 창의 위쪽에 있는 **제거** 단추를 클릭합니다. 
 
    ![할당 - 액세스 패키지에서 사용자 제거](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    할당이 제거되었다는 알림이 표시됩니다. 

## <a name="next-steps"></a>다음 단계

- [액세스 패키지의 요청 및 설정 변경](entitlement-management-access-package-request-policy.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)