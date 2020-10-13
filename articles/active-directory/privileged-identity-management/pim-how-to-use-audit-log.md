---
title: Azure AD PIM에서 Azure AD 역할에 대 한 감사 로그 보고서 보기 | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할에 대 한 감사 로그 기록을 보는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2f464791995b9168868f1661a3c303d46f7987
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530122"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대 한 감사 기록 보기

PIM (Privileged Identity Management) 감사 기록을 사용 하 여 모든 권한 있는 역할에 대해 지난 30 일 이내에 모든 역할 할당 및 활성화를 볼 수 있습니다. 관리자, 최종 사용자 및 동기화 작업을 포함 하 여 Azure Active Directory (Azure AD) 조직에서 활동의 전체 감사 기록을 보려면 [보안 및 활동 보고서 Azure Active Directory](../reports-monitoring/overview-reports.md)를 사용할 수 있습니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

11 월 2019부터 Privileged Identity Management의 Azure AD 역할 부분은 Azure 리소스 역할의 환경과 일치 하는 새 버전으로 업데이트 됩니다. 그러면 [기존 API에 대 한 변경](azure-ad-roles-features.md#api-changes)뿐만 아니라 추가 기능이 생성 됩니다. 새 버전이 롤아웃 되는 동안이 문서에서 수행 하는 절차는 현재 보유 하 고 있는 Privileged Identity Management 버전에 따라 달라 집니다. 이 섹션의 단계에 따라 Privileged Identity Management 버전을 확인 합니다. Privileged Identity Management 버전을 확인 한 후에는이 문서에서 해당 버전과 일치 하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할에 있는 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. **Azure AD Privileged Identity Management**를 엽니다. 개요 페이지의 맨 위에 배너가 있는 경우이 문서의 **새 버전** 탭에 있는 지침을 따르세요. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

    [!["Azure AD 역할-디렉터리 역할 감사 기록" 페이지를 보여 주는 스크린샷](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "해당 버전에 대 한 탭을 선택 합니다.")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[새 버전](#tab/new)

Azure AD 역할에 대 한 감사 기록을 보려면 다음 단계를 따르세요.

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

리소스 감사는 Azure AD 역할에 연결 된 모든 활동에 대 한 보기를 제공 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **리소스 감사**를 선택 합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![필터를 사용 하는 리소스 감사 목록](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>내 감사 보기

내 감사를 통해 자신의 개인 역할 작업을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. 감사 기록을 보려는 리소스를 선택 합니다.

1. **내 감사**를 선택 합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![현재 사용자에 대 한 감사 목록](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[이전 버전](#tab/previous)

## <a name="view-audit-history"></a>감사 기록 보기

Azure AD 역할에 대 한 감사 기록을 보려면 다음 단계를 따르세요.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **디렉터리 역할 감사 기록**을 선택 합니다.

    감사 기록에 따라 세로 막대형 차트는 총 활성화, 일별 최대 활성화 및 일별 평균 활성화와 함께 표시 됩니다.

    [![Azure AD 역할 새 버전](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "디렉터리 역할 감사 기록 보기")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    페이지의 맨 아래에는 사용 가능한 감사 기록의 각 작업에 대 한 정보가 포함 된 테이블이 표시 됩니다. 열에는 다음과 같은 의미가 있습니다.

    | 열 | Description |
    | --- | --- |
    | Time | 작업이 발생 한 시간입니다. |
    | Requestor | 역할 활성화 또는 변경을 요청한 사용자입니다. 값이 **Azure System**이면 azure 감사 기록에서 자세한 내용을 확인 합니다. |
    | 작업 | 요청자에 의해 수행 된 작업입니다. 작업에는 Assign, 할당 취소, 활성화, 비활성화 또는 AddedOutsidePIM이 포함 될 수 있습니다. |
    | 멤버 | 역할을 활성화 하거나 역할에 할당 된 사용자입니다. |
    | 역할 | 사용자가 할당 하거나 활성화 한 역할입니다. |
    | 지각 장애 | 활성화 하는 동안 이유 필드에 입력 한 텍스트입니다. |
    | 만료 | 활성화 된 역할이 만료 되는 경우 적격 역할 할당에만 적용 됩니다. |

1. 감사 기록을 정렬 하려면 **시간**, **작업**및 **역할** 단추를 클릭 합니다.

## <a name="filter-audit-history"></a>감사 기록 필터링

1. 감사 기록 페이지의 맨 위에서 **필터** 단추를 클릭 합니다.

    **차트 매개 변수 업데이트** 창이 나타납니다.

1. **시간 범위**에서 시간 범위를 선택 합니다.

1. **역할**에서 확인란을 선택 하 여 보려는 역할을 표시 합니다.

    ![차트 매개 변수 창 업데이트](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. **완료** 를 선택 하 여 필터링 된 감사 기록을 봅니다.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>승인 이벤트에 대 한 이유, 승인자 및 티켓 번호를 가져옵니다.

1. 권한 있는 역할 관리자 역할 권한을 사용 하 여 [Azure Portal](https://aad.portal.azure.com) 에 로그인 하 고 Azure AD를 엽니다.
1. **감사 로그**를 선택합니다.
1. **서비스** 필터를 사용 하 여 권한 있는 id 관리 서비스에 대 한 감사 이벤트만 표시 합니다. **감사 로그** 페이지에서 다음을 수행할 수 있습니다.

    - **상태 이유** 열에서 감사 이벤트의 이유를 참조 하세요.
    - "역할 요청이 승인 되었습니다." 이벤트의 경우 **시작 (행위자)** 열에서 승인자를 확인 합니다.

    [!["시작 된 사람 (행위자)" 메뉴가 열리고 "PIM"이 선택 된 "감사 로그" 페이지를 보여 주는 스크린샷](media/pim-how-to-use-audit-log/filter-audit-logs.png "PIM 서비스에 대 한 감사 로그 필터링")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. 감사 로그 이벤트를 선택 하 여 **세부 정보** 창의 **작업** 탭에서 티켓 번호를 확인 합니다.
  
    [!["세부 정보" 창에 티켓 번호가 강조 표시 된 "감사 로그" 페이지를 보여 주는 스크린샷](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "감사 이벤트의 티켓 번호를 확인 합니다.")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. 감사 이벤트에 대 한 **세부 정보** 창의 **대상** 탭에서 요청자 (역할 활성화 사용자)를 볼 수 있습니다. Azure AD 역할에는 두 가지 대상 유형이 있습니다.

    - Role (**Type** = role)
    - 요청자 (**Type** = User)

일반적으로 승인 이벤트 바로 위의 감사 로그 이벤트는 "역할에 구성원 추가"에 대 한 이벤트입니다. 여기서 **시작 된 (행위자)** 은 요청자입니다. 대부분의 경우 감사 관점에서 승인 요청에 대 한 요청자를 찾을 필요가 없습니다.

---

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할에 대 한 활동 및 감사 기록 보기](azure-pim-resource-rbac.md)
