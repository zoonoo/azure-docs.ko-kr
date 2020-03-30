---
title: 권한 있는 ID 관리(PIM) - Azure AD | 에서 Azure 리소스 역할에 대한 감사 보고서 보기 마이크로 소프트 문서
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 작업 및 감사 기록을 봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329625"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure 리소스 역할에 대한 활동 및 감사 기록 보기

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 조직 내 Azure 리소스 역할에 대한 작업, 활성화 및 감사 기록을 볼 수 있습니다. 여기에는 구독, 리소스 그룹 및 가상 머신도 포함됩니다. Azure 역할 기반 액세스 제어 기능을 활용하는 Azure 포털 내의 모든 리소스는 권한 있는 ID 관리의 보안 및 수명 주기 관리 기능을 활용할 수 있습니다.

> [!NOTE]
> 조직에서 [Azure 위임리소스 관리를](../../lighthouse/concepts/azure-delegated-resource-management.md)사용하는 서비스 공급자에게 관리 기능을 아웃소싱한 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

## <a name="view-activity-and-activations"></a>작업 및 활성화 보기

특정 사용자가 다양한 리소스에서 수행한 작업을 살펴보려면 특정 활성화 기간에 연결된 Azure 리소스 작업을 보면 됩니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 활동 및 활성화를 볼 리소스를 선택합니다.

1. **역할** 또는 **구성원을**선택합니다.

1. 사용자를 선택합니다.

    날짜별로 Azure 리소스에서 사용자의 작업 요약이 표시됩니다. 같은 기간에 이루어진 최근 역할 활성화도 표시됩니다.

    ![리소스 활동 요약 및 역할 활성화를 통해 사용자 세부 정보](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 특정 역할 활성화를 선택하여 해당 사용자가 활성 상태인 동안 발생한 세부 정보 및 해당 Azure 리소스 활동을 확인합니다.

    [![역할 활성화 선택 및 활동 세부 정보](media/azure-pim-resource-rbac/export-membership.png "역할 활성화 선택 및 활동 세부 정보")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>자식 요소가 있는 역할 할당 내보내기

감사자에 역할 할당의 전체 목록을 제공해야 하는 규정 준수 요구 사항이 있을 수 있습니다. 권한 있는 ID 관리를 사용하면 모든 자식 리소스에 대한 역할 할당을 포함하는 특정 리소스에서 역할 할당을 쿼리할 수 있습니다. 이전에는 관리자가 구독의 역할 할당 전체 목록을 가져오기가 어려웠으며, 각 특정 리소스의 역할 할당을 내보내야 했습니다. 권한 있는 ID 관리를 사용하면 모든 리소스 그룹 및 리소스에 대한 역할 할당을 포함하여 구독의 모든 활성 및 적격 역할 할당을 쿼리할 수 있습니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 구독과 같은 역할 할당을 내보낼 리소스를 선택합니다.

1. **구성원**을 선택합니다.

1. **내보내기** 를 선택하여 내보내기 멤버 자격 창을 엽니다.

    [![모든 멤버내보내기에 대한 멤버 창 내보내기](media/azure-pim-resource-rbac/export-membership.png "모든 멤버를 내보낼 수 있는 멤버 내보내기 페이지 내보내기")](media/azure-pim-resource-rbac/export-membership.png)

1. CSV 파일에서 모든 역할 할당을 내보내려면 **모든 멤버 내보내기를** 선택합니다.

    ![CSV 파일에서 Excel에서 표시로 내보낸 역할 할당](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

리소스 감사를 통해 리소스에 대한 모든 역할 작업을 볼 수 있습니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 감사 기록을 보려는 리소스를 선택합니다.

1. **리소스 감사를 선택합니다.**

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    [![필터가 있는 리소스 감사 목록](media/azure-pim-resource-rbac/rbac-resource-audit.png "필터가 있는 리소스 감사 목록")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. **감사 유형**으로 **활성화(할당됨 + 활성화됨)** 을 선택합니다.

    [![활성화된 감사 유형](media/azure-pim-resource-rbac/rbac-audit-activity.png "활성화하여 필터링된 리소스 감사 목록")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![리소스 감사 에 의해 필터링된 리소스 감사 목록[감사 유형 활성화)'에 의해 필터링된 리소스 감사](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. **작업** 아래에서, Azure 리소스에서 수행한 작업 세부 정보를 보고 싶은 사용자의 **(작업)** 을 클릭합니다.

    ![특정 작업에 대한 사용자 활동 세부 정보](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>내 감사 보기

내 감사를 통해 자신의 개인 역할 작업을 볼 수 있습니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 감사 기록을 보려는 리소스를 선택합니다.

1. **내 감사를**선택합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    [![현재 사용자에 대한 감사 목록](media/azure-pim-resource-rbac/my-audit-time.png "현재 사용자에 대한 감사 목록")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> 감사 기록에 액세스하려면 글로벌 관리자 또는 권한 있는 역할 관리자 역할이 필요합니다.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>승인 이벤트에 대한 이유, 승인자 및 티켓 번호 받기

1. 권한 있는 역할 관리자 역할 권한을 사용하여 [Azure 포털에](https://aad.portal.azure.com) 로그인하고 Azure AD를 엽니다.
1. **감사 로그를 선택합니다.**
1. **서비스** 필터를 사용하여 권한 있는 ID 관리 서비스에 대한 감사 이벤트만 표시합니다. 감사 **로그** 페이지에서 다음을 수행할 수 있습니다.

    - **상태 이유** 열에서 감사 이벤트에 대한 이유를 확인합니다.
    - "역할 요청에 멤버 추가 승인" 이벤트에 대한 **(행위자)** 열에서 승인자를 참조하십시오.

    [![PIM 서비스에 대한 감사 로그 필터링](media/azure-pim-resource-rbac/filter-audit-logs.png "PIM 서비스에 대한 감사 로그 필터링")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. **세부 정보** 창의 **활동** 탭에서 티켓 번호를 보려면 감사 로그 이벤트를 선택합니다.
  
    [![감사 이벤트의 티켓 번호를 확인합니다.](media/azure-pim-resource-rbac/audit-event-ticket-number.png "감사 이벤트의 티켓 번호 확인")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)

1. 감사 이벤트에 대한 **세부 정보** 창의 **대상** 탭에서 요청자(역할을 활성화하는 사람)를 볼 수 있습니다. Azure 리소스 역할에 대한 세 가지 대상 유형이 있습니다.

    - **역할(유형** = 역할)
    - 요청자(유형**Type** = 기타)
    - 승인자 **(유형** = 사용자)

    [![대상 유형 확인](media/azure-pim-resource-rbac/audit-event-target-type.png "대상 유형 확인")](media/azure-pim-resource-rbac/audit-event-target-type.png)

일반적으로 승인 이벤트 바로 위의 로그 이벤트는 **시작(행위자)이** 요청자인 "역할에 멤버 추가 완료"에 대한 이벤트입니다. 대부분의 경우 감사 관점에서 승인 요청에서 요청인을 찾을 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
- [권한 있는 ID 관리에서 Azure 리소스 역할에 대한 요청을 승인 하거나 거부합니다.](pim-resource-roles-approval-workflow.md)
- [권한 있는 ID 관리에서 Azure AD 역할에 대한 감사 기록 보기](pim-how-to-use-audit-log.md)
