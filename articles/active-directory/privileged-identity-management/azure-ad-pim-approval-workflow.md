---
title: PIM에서 Azure AD 역할에 대 한 요청 승인 또는 거부 | Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할에 대 한 요청을 승인 하거나 거부 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4778ce5efcd6145447c194eaab34bc230bcbe34c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371548"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대 한 요청 승인 또는 거부

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)를 사용 하 여 활성화에 대 한 승인이 필요 하도록 역할을 구성 하 고 하나 이상의 사용자 또는 그룹을 위임 된 승인자로 선택할 수 있습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

11 월 2019부터 Privileged Identity Management의 Azure AD 역할 부분은 Azure 역할의 환경과 일치 하는 새 버전으로 업데이트 됩니다. 그러면 [기존 API에 대 한 변경](azure-ad-roles-features.md#api-changes)뿐만 아니라 추가 기능이 생성 됩니다. 새 버전이 롤아웃 되는 동안이 문서에서 수행 하는 절차는 현재 보유 하 고 있는 Privileged Identity Management 버전에 따라 달라 집니다. 이 섹션의 단계에 따라 Privileged Identity Management 버전을 확인 합니다. Privileged Identity Management 버전을 확인 한 후에는이 문서에서 해당 버전과 일치 하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../roles/permissions-reference.md#privileged-role-administrator) 역할에 있는 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. **Azure AD Privileged Identity Management**를 엽니다. 개요 페이지의 맨 위에 배너가 있는 경우이 문서의 **새 버전** 탭에 있는 지침을 따르세요. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

    [![Azure AD > Privileged Identity Management를 선택 합니다.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

이 문서의 단계에 따라 Azure AD 역할에 대 한 요청을 승인 하거나 거부 합니다.

# <a name="new-version"></a>[새 버전](#tab/new)

## <a name="view-pending-requests"></a>보류 중인 요청 보기

위임 승인자는 Azure AD 역할 요청이 승인 보류 중인 경우 전자 메일 알림을 받게 됩니다. Privileged Identity Management에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **요청 승인**을 선택 합니다.

    ![요청 승인-Azure AD 역할 검토 요청을 표시 하는 페이지](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인 하려는 요청을 찾아 선택 합니다. 승인 또는 거부 페이지가 표시 됩니다.

    !["요청 승인-Azure AD 역할" 페이지를 보여 주는 스크린샷](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. **근거** 상자에 비즈니스 근거를 입력 합니다.

1. **승인**을 선택합니다. 승인에 대 한 Azure 알림을 받게 됩니다.

    ![요청이 승인 되었음을 보여 주는 승인 알림](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 찾아 선택 합니다. 승인 또는 거부 페이지가 표시 됩니다.

    ![요청 승인-세부 정보 및 근거 상자를 사용 하 여 승인 또는 거부 창](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **근거** 상자에 비즈니스 근거를 입력 합니다.

1. **거부**를 선택 합니다. 거부가 포함된 알림이 표시됩니다.

## <a name="workflow-notifications"></a>워크플로 알림

워크플로 알림에 대한 일부 정보는 다음과 같습니다.

- 승인자는 역할에 대 한 요청이 검토를 보류 중인 경우 전자 메일로 알립니다. 메일 알림에는 승인자가 승인 또는 거부할 수 있는 요청에 대한 직접 링크가 포함되어 있습니다.
- 요청은 승인 또는 거부 하는 첫 번째 승인자에 의해 확인 됩니다.
- 승인자가 요청에 응답 하면 모든 승인자에 게 해당 작업에 대 한 알림이 표시 됩니다.
- 승인 된 사용자가 해당 역할에서 활성화 되 면 전역 관리자 및 권한 있는 역할 관리자에 게 알림이 제공 됩니다.

>[!NOTE]
>승인 된 사용자가 활성 상태가 아니어야 한다고 생각 하는 전역 관리자 또는 권한 있는 역할 관리자는 Privileged Identity Management에서 활성 역할 할당을 제거할 수 있습니다. 관리자는 승인자가 아니면 보류 중인 요청에 대 한 알림이 제공 되지 않지만 Privileged Identity Management에서 보류 중인 요청을 확인 하 여 모든 사용자에 대 한 보류 중인 요청을 확인 하 고 취소할 수 있습니다.

# <a name="previous-version"></a>[이전 버전](#tab/previous)

## <a name="view-pending-requests"></a>보류 중인 요청 보기

위임 승인자는 Azure AD 역할 요청이 승인 보류 중인 경우 전자 메일 알림을 받게 됩니다. Privileged Identity Management에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **요청 승인**을 클릭합니다.

    ![Azure AD 역할-요청 승인](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    승인 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인할 요청을 클릭하고 **승인**을 클릭하여 선택한 요청 승인 창을 엽니다.

    ![승인 옵션이 강조 표시 된 요청 승인 목록](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. **승인 이유** 상자에 이유를 입력합니다.

    ![승인 이유를 사용 하 여 선택한 요청 창 승인](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. **승인**을 클릭 합니다.

    상태 기호가 승인으로 업데이트됩니다.

    ![승인 단추를 클릭 한 후 선택한 요청 창 승인](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 클릭하고 **거부**를 클릭하여 선택한 요청 거부 창을 엽니다.

    ![거부 옵션이 강조 표시 된 요청 승인 목록](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. **거부 이유** 상자에 이유를 입력합니다.

    ![거부 이유를 사용 하 여 선택한 요청 창 거부](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. **거부**를 선택 합니다.

    상태 기호가 거부로 업데이트됩니다.

---

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management의 전자 메일 알림](pim-email-notifications.md)
- [Privileged Identity Management에서 Azure 리소스 역할에 대 한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
