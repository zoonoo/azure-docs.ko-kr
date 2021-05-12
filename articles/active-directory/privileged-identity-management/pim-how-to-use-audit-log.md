---
title: Azure AD 역할에 대한 감사 로그 보고서 보기 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 역할에 대한 감사 기록을 보는 방법을 알아봅니다.
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
ms.openlocfilehash: 8481c562ecbab1f26e877e55a5a9454695ddf4c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370834"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대한 감사 기록 보기

Azure Privileged Identity Management(PIM) 감사 기록을 사용하여 권한 있는 모든 역할에 대한 지난 30일의 모든 역할 할당 및 활성화를 볼 수 있습니다. 관리자, 최종 사용자 및 동기화 작업을 포함하여 Azure Active Directory(Azure AD) 조직에서 활동의 전체 감사 기록을 보려면 [Azure Active Directory 보안 및 작업 보고서](../reports-monitoring/overview-reports.md)를 사용할 수 있습니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

2019년 11월부터 Privileged Identity Management의 Azure AD 역할 부분은 Azure 리소스 역할의 환경과 일치하는 새 버전으로 업데이트됩니다. 그러면 [기존 API에 대한 변경](azure-ad-roles-features.md#api-changes)뿐만 아니라 추가 기능이 생성됩니다. 새 버전이 롤아웃되는 동안 이 문서에서 수행하는 절차는 현재 보유하고 있는 Privileged Identity Management 버전에 따라 달라집니다. 이 섹션의 단계에 따라 사용 중인 Privileged Identity Management 버전을 확인합니다. Privileged Identity Management 버전을 확인한 후에는 이 문서에서 해당 버전과 일치하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../roles/permissions-reference.md#privileged-role-administrator) 역할인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management** 를 엽니다. 개요 페이지의 맨 위에 배너가 있는 경우 이 문서의 **새 버전** 탭에 있는 지침을 따릅니다. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

    [!["Azure AD 역할-디렉터리 역할 감사 기록" 페이지를 보여 주는 스크린샷.](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "버전에 대한 탭을 선택합니다.")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[새 버전](#tab/new)

Azure AD 역할에 대한 감사 기록을 보려면 다음 단계를 따르세요.

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

리소스 감사는 Azure AD 역할과 연결된 모든 활동에 대한 보기를 제공합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **리소스 감사** 를 선택합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![필터를 사용하는 리소스 감사 목록](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>내 감사 보기

내 감사를 통해 자신의 개인 역할 작업을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. 감사 기록을 보려는 리소스를 선택합니다.

1. **내 앱** 을 선택합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![현재 사용자에 대한 감사 목록](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[이전 버전](#tab/previous)

## <a name="view-audit-history"></a>감사 기록 보기

Azure AD 역할에 대한 감사 기록을 보려면 다음 단계를 따르세요.

1. [권한 있는 역할 관리자](../roles/permissions-reference.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **디렉터리 역할 감사 기록** 을 선택합니다.

    감사 기록에 따라 세로 막대형 차트는 총 활성화, 일별 최대 활성화 및 일별 평균 활성화와 함께 표시됩니다.

    [![Azure AD 역할 새 버전](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "디렉터리 역할 감사 기록 보기")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    페이지의 맨 아래에는 사용 가능한 감사 기록의 각 작업에 대한 정보가 포함된 테이블이 표시됩니다. 이러한 열에는 다음과 같은 의미가 있습니다.

    | 열 | Description |
    | --- | --- |
    | Time | 작업이 발생한 시간. |
    | Requestor | 역할 활성화 또는 변경을 요청한 사용자. 값이 **Azure 시스템** 인 경우 Azure 감사 기록에서 자세한 내용을 확인합니다. |
    | 작업 | 요청자가 수행하는 작업. 작업에는 할당, 할당 취소, 활성화, 비활성화 또는 AddedOutsidePIM이 포함 될 수 있습니다. |
    | 멤버 | 활성화되어 있거나 역할에 할당된 사용자. |
    | 역할 | 사용자가 할당하거나 활성화한 역할. |
    | 지각 장애 | 활성화하는 동안 이유 필드에 입력한 텍스트. |
    | 만료 | 활성화된 역할이 만료되는 경우. 적격 역할 할당에만 적용. |

1. 감사 기록을 정렬하려면 **시간**, **작업** 및 **역할** 단추를 클릭합니다.

## <a name="filter-audit-history"></a>감사 기록 필터링

1. 감사 기록 페이지의 맨 위에서 **필터** 단추를 클릭합니다.

    **차트 매개 변수 업데이트** 창이 나타납니다.

1. **시간 범위** 에서 시간 범위를 선택합니다.

1. **역할** 에서 확인란을 선택하여 보려는 역할을 표시합니다.

    ![차트 매개 변수 창 업데이트](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. **완료** 를 선택하여 필터링된 감사 기록을 봅니다.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>승인 이벤트에 대한 이유, 승인자 및 티켓 번호를 가져옵니다

1. 권한 있는 역할 관리자 역할 권한을 사용하여 [Azure portal](https://aad.portal.azure.com)에 로그인하고 Azure AD를 엽니다.
1. **감사 로그** 를 선택합니다.
1. **서비스** 필터를 사용하여 Privileged Identity Management 서비스에 대한 감사 이벤트만 표시합니다. **감사 로그** 페이지에서 다음을 수행할 수 있습니다.

    - **상태 이유** 열에서 감사 이벤트의 이유를 참조하세요.
    - "역할 요청에 구성원 추가가 승인됨" 이벤트의 경우 **행위자(시작한 사람)** 열에서 승인자를 확인합니다.

    [!["행위자(시작한 사람)" 메뉴가 열리고 "PIM"이 선택된 "감사 로그" 페이지를 보여주는 스크린샷.](media/pim-how-to-use-audit-log/filter-audit-logs.png "PIM 서비스에 대한 감사 로그 필터링")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. 감사 로그 이벤트를 선택하여 **세부 정보** 창의 **작업** 탭에서 티켓 번호를 확인합니다.
  
    [!["세부 정보" 창에 티켓 번호가 강조 표시된 "감사 로그" 페이지를 보여주는 스크린샷.](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "감사 이벤트의 티켓 번호를 확인하십시오")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. 감사 이벤트에 대한 **세부 정보** 창의 **대상** 탭에서 요청자(역할 활성화 사용자)를 볼 수 있습니다. Azure AD 역할에는 두 가지 대상 유형이 있습니다.

    - 역할(**유형** = 역할)
    - 요청자(**유형** = 사용자)

일반적으로 승인 이벤트 바로 위의 감사 로그 이벤트는 "역할에 구성원 추가가 완료됨"에 대한 이벤트이며, 여기서 **행위자(시작한 사람)** 은 요청자입니다. 대부분의 경우 감사 큐브 뷰에서 승인 요청에 대한 요청자를 찾을 필요가 없습니다.

---

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할에 대한 작업 및 감사 기록을 봅니다](azure-pim-resource-rbac.md)
