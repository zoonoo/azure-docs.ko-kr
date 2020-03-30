---
title: PIM - Azure AD에서 Azure AD 역할에 대한 요청을 승인 또는 거부 | 마이크로 소프트 문서
description: AZURE AD 권한 ID 관리(PIM)에서 Azure AD 역할에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049010"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure AD 역할에 대한 요청을 승인 하거나 거부합니다.

Azure Active Directory(Azure AD) 권한 있는 ID 관리(PIM)를 사용하면 활성화에 대한 승인이 필요한 역할을 구성하고 하나 또는 여러 사용자 또는 그룹을 위임된 승인자로 선택할 수 있습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

2019년 11월부터 권한 있는 Id Management의 Azure AD 역할 부분이 Azure 역할에 대한 환경과 일치하는 새 버전으로 업데이트됩니다. 이렇게 하면 [기존 API에 대한 변경](azure-ad-roles-features.md#api-changes)사항뿐만 아니라 추가 기능이 만들어집니다. 새 버전이 롤아웃되는 동안 이 문서에서 따르는 절차는 현재 가지고 있는 권한 있는 ID 관리 버전에 따라 다릅니다. 이 섹션의 단계에 따라 사용 중인 권한 ID 관리 버전을 확인합니다. 권한 있는 ID 관리 버전을 알고 나면 이 문서에서 해당 버전과 일치하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할에 있는 사용자와 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. Azure **AD 권한 ID 관리**를 엽니다. 개요 페이지 상단에 배너가 있는 경우 이 문서의 **새 버전** 탭의 지침을 따릅니다. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

이 문서의 단계에 따라 Azure AD 역할에 대한 요청을 승인하거나 거부합니다.

# <a name="new-version"></a>[새 버전](#tab/new)

## <a name="view-pending-requests"></a>보류 중인 요청 보기

위임된 승인자는 Azure AD 역할 요청이 승인 대기 중일 때 전자 메일 알림을 받게 됩니다. 권한 있는 ID 관리에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **요청 승인을**선택합니다.

    ![요청 승인 - Azure AD 역할 검토 요청을 보여주는 페이지](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인할 요청을 찾아 선택합니다. 승인 또는 거부 페이지가 나타납니다.

    ![요청 승인 - 세부 정보 및 자리 맞추기 상자로 창 승인 또는 거부](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. **[자리 맞추기]** 상자에 비즈니스 근거를 입력합니다.

1. **을 선택합니다.** 승인에 대한 Azure 알림을 받게 됩니다.

    ![요청이 승인된 것으로 표시된 알림 승인](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 찾아 선택합니다. 승인 또는 거부 페이지가 나타납니다.

    ![요청 승인 - 세부 정보 및 자리 맞추기 상자로 창 승인 또는 거부](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **[자리 맞추기]** 상자에 비즈니스 근거를 입력합니다.

1. **거부를 선택합니다.** 거부가 포함된 알림이 표시됩니다.

## <a name="workflow-notifications"></a>워크플로 알림

워크플로 알림에 대한 일부 정보는 다음과 같습니다.

- 역할에 대한 요청이 검토 대기 중일 때 승인자는 이메일로 알림을 받습니다. 메일 알림에는 승인자가 승인 또는 거부할 수 있는 요청에 대한 직접 링크가 포함되어 있습니다.
- 요청은 승인 또는 거부하는 첫 번째 승인자가 해결합니다.
- 승인자가 요청에 응답하면 모든 승인자가 작업에 대한 알림을 받습니다.
- 승인된 사용자가 해당 역할에 활성화되면 전역 관리자 및 권한 있는 역할 관리자에게 알림이 전송됩니다.

>[!NOTE]
>승인된 사용자가 활성화되어서는 안 된다고 생각하는 글로벌 관리자 또는 권한 있는 역할 관리자는 권한 있는 ID 관리에서 활성 역할 할당을 제거할 수 있습니다. 관리자는 승인자가 아닌 보류 중인 요청에 대한 알림을 받지 않지만 권한 있는 ID 관리에서 보류 중인 요청을 확인하여 모든 사용자에 대한 보류 중인 요청을 보고 취소할 수 있습니다.

# <a name="previous-version"></a>[이전 버전](#tab/previous)

## <a name="view-pending-requests"></a>보류 중인 요청 보기

위임된 승인자는 Azure AD 역할 요청이 승인 대기 중일 때 전자 메일 알림을 받게 됩니다. 권한 있는 ID 관리에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **요청 승인**을 클릭합니다.

    ![Azure AD 역할 - 요청 승인](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    승인 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인할 요청을 클릭하고 **승인**을 클릭하여 선택한 요청 승인 창을 엽니다.

    ![승인 옵션이 강조 표시된 요청 목록 승인](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. **승인 이유** 상자에 이유를 입력합니다.

    ![승인 된 이유로 선택한 요청 창 승인](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. **승인을 클릭합니다.**

    상태 기호가 승인으로 업데이트됩니다.

    ![승인 단추를 클릭한 후 선택한 요청 창 승인](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 클릭하고 **거부**를 클릭하여 선택한 요청 거부 창을 엽니다.

    ![거부 옵션이 강조 표시된 요청 목록 승인](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. **거부 이유** 상자에 이유를 입력합니다.

    ![거부 된 이유로 선택한 요청 창을 거부](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. **거부를 선택합니다.**

    상태 기호가 거부로 업데이트됩니다.

---

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리의 전자 메일 알림](pim-email-notifications.md)
- [권한 있는 ID 관리에서 Azure 리소스 역할에 대한 요청을 승인 하거나 거부합니다.](pim-resource-roles-approval-workflow.md)
