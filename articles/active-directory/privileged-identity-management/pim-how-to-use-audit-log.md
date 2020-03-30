---
title: Azure AD PIM에서 Azure AD 역할에 대한 감사 로그 보고서 보기 | 마이크로 소프트 문서
description: AZURE AD 권한 ID 관리(PIM)에서 Azure AD 역할에 대한 감사 로그 기록을 보는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329517"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure AD 역할에 대한 감사 기록 보기

PIM(권한 있는 ID 관리) 감사 기록을 사용하여 모든 권한 있는 역할에 대해 지난 30일 이내에 모든 역할 할당 및 활성화를 확인할 수 있습니다. 관리자, 최종 사용자 및 동기화 활동을 포함하여 Azure Active Directory(Azure AD) 조직의 활동에 대한 전체 감사 기록을 보려면 [Azure Active Directory 보안 및 활동 보고서를](../reports-monitoring/overview-reports.md)사용할 수 있습니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

2019년 11월부터 권한 있는 Id Management의 Azure AD 역할 부분이 Azure 리소스 역할에 대한 환경과 일치하는 새 버전으로 업데이트됩니다. 이렇게 하면 [기존 API에 대한 변경](azure-ad-roles-features.md#api-changes)사항뿐만 아니라 추가 기능이 만들어집니다. 새 버전이 롤아웃되는 동안 이 문서에서 따르는 절차는 현재 가지고 있는 권한 있는 ID 관리 버전에 따라 다릅니다. 이 섹션의 단계에 따라 사용 중인 권한 ID 관리 버전을 확인합니다. 권한 있는 ID 관리 버전을 알고 나면 이 문서에서 해당 버전과 일치하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할에 있는 사용자와 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. Azure **AD 권한 ID 관리**를 엽니다. 개요 페이지 상단에 배너가 있는 경우 이 문서의 **새 버전** 탭의 지침을 따릅니다. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

    [![Azure AD 역할 새 버전](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "버전의 탭 선택")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[새 버전](#tab/new)

다음 단계에 따라 Azure AD 역할에 대한 감사 기록을 봅니다.

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

리소스 감사를 통해 Azure AD 역할과 연결된 모든 활동을 볼 수 있습니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. **리소스 감사를 선택합니다.**

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![필터가 있는 리소스 감사 목록](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>내 감사 보기

내 감사를 통해 자신의 개인 역할 작업을 볼 수 있습니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. 감사 기록을 보려는 리소스를 선택합니다.

1. **내 감사를**선택합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![현재 사용자에 대한 감사 목록](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[이전 버전](#tab/previous)

## <a name="view-audit-history"></a>감사 기록 보기

다음 단계에 따라 Azure AD 역할에 대한 감사 기록을 봅니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. **디렉터리 역할 감사 기록을**선택합니다.

    감사 기록에 따라 총 활성화, 일일 최대 활성화 및 일평균 활성화와 함께 열차트가 표시됩니다.

    [![Azure AD 역할 새 버전](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "디렉터리 역할 감사 기록 보기")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    페이지 하단에 사용 가능한 감사 기록의 각 작업에 대한 정보가 테이블이 표시됩니다. 열에는 다음과 같은 의미가 있습니다.

    | 열 | 설명 |
    | --- | --- |
    | Time | 작업이 발생한 경우입니다. |
    | Requestor | 역할 활성화 또는 변경을 요청한 사용자입니다. 값이 Azure **시스템인**경우 자세한 내용은 Azure 감사 기록을 확인합니다. |
    | 작업 | 요청자가 수행한 작업입니다. 작업에는 할당, 부호 해제, 활성화, 비활성화 또는 추가외부PIM이 포함될 수 있습니다. |
    | 멤버 | 역할에 활성화하거나 할당된 사용자입니다. |
    | 역할 | 사용자가 할당하거나 활성화한 역할입니다. |
    | 추론 | 활성화 하는 동안 이유 필드에 입력 된 텍스트입니다. |
    | 만료 | 활성화된 역할이 만료되는 경우 적격 역할 할당에만 적용됩니다. |

1. 감사 기록을 정렬하려면 **시간,** **작업**및 **역할** 단추를 클릭합니다.

## <a name="filter-audit-history"></a>감사 기록 필터링

1. 감사 기록 페이지 상단에서 **필터** 단추를 클릭합니다.

    **업데이트 차트 매개 변수 창이** 나타납니다.

1. **시간 범위에서**시간 범위를 선택합니다.

1. **역할에서**확인란을 선택하여 보려는 역할을 나타냅니다.

    ![차트 매개 변수 창 업데이트](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 필터링된 감사 기록을 보려면 **완료를** 선택합니다.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>승인 이벤트에 대한 이유, 승인자 및 티켓 번호 받기

1. 권한 있는 역할 관리자 역할 권한을 사용하여 [Azure 포털에](https://aad.portal.azure.com) 로그인하고 Azure AD를 엽니다.
1. **감사 로그를 선택합니다.**
1. **서비스** 필터를 사용하여 권한 있는 ID 관리 서비스에 대한 감사 이벤트만 표시합니다. 감사 **로그** 페이지에서 다음을 수행할 수 있습니다.

    - **상태 이유** 열에서 감사 이벤트에 대한 이유를 확인합니다.
    - "역할 요청에 멤버 추가 승인" 이벤트에 대한 **(행위자)** 열에서 승인자를 참조하십시오.

    [![Azure AD 역할 새 버전](media/pim-how-to-use-audit-log/filter-audit-logs.png "PIM 서비스에 대한 감사 로그 필터링")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. **세부 정보** 창의 **활동** 탭에서 티켓 번호를 보려면 감사 로그 이벤트를 선택합니다.
  
    [![Azure AD 역할 새 버전](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "감사 이벤트의 티켓 번호 확인")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. 감사 이벤트에 대한 **세부 정보** 창의 **대상** 탭에서 요청자(역할을 활성화하는 사람)를 볼 수 있습니다. Azure AD 역할에는 두 가지 대상 유형이 있습니다.

    - **역할(유형** = 역할)
    - 요청자(유형**Type** = 사용자)

일반적으로 승인 이벤트 바로 위의 감사 로그 이벤트는 **시작(행위자)이** 요청자인 "역할에 멤버 추가 완료"에 대한 이벤트입니다. 대부분의 경우 감사 관점에서 승인 요청에서 요청인을 찾을 필요가 없습니다.

---

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할에 대한 활동 및 감사 기록 보기](azure-pim-resource-rbac.md)
