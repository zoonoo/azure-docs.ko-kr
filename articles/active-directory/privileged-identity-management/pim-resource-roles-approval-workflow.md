---
title: PIM에서 Azure 리소스 역할에 대 한 요청 승인-Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021980"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대 한 요청 승인 또는 거부

Azure Active Directory (Azure AD)에서 Privileged Identity Management (PIM)를 사용 하 여 활성화에 대 한 승인이 필요 하도록 역할을 구성 하 고 Azure AD 조직에서 위임 된 승인자로 사용자 또는 그룹을 선택할 수 있습니다. 권한 있는 역할 관리자에 대 한 작업을 줄이려면 각 역할에 대해 둘 이상의 승인자를 선택 하는 것이 좋습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

이 문서의 단계에 따라 Azure 리소스 역할에 대한 요청을 승인하거나 거부합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

Azure 리소스 역할 요청이 승인 보류 중일 때는 위임된 승인자가 이메일 알림을 받게 됩니다. Privileged Identity Management에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **요청 승인**을 선택 합니다.

    ![요청 승인-검토 요청을 표시 하는 Azure 리소스 페이지](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인 하려는 요청을 찾아 선택 합니다. 승인 또는 거부 페이지가 표시 됩니다.

    ![요청 승인-세부 정보 및 근거 상자를 사용 하 여 승인 또는 거부 창](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **근거** 상자에 비즈니스 근거를 입력 합니다.

1. **승인**을 선택 합니다. 승인에 대 한 Azure 알림을 받게 됩니다.

    ![요청이 승인 되었음을 보여 주는 승인 알림](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 찾아 선택 합니다. 승인 또는 거부 페이지가 표시 됩니다.

    ![요청 승인-세부 정보 및 근거 상자를 사용 하 여 승인 또는 거부 창](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **근거** 상자에 비즈니스 근거를 입력 합니다.

1. **거부**를 선택 합니다. 거부가 포함된 알림이 표시됩니다.

## <a name="workflow-notifications"></a>워크플로 알림

워크플로 알림에 대한 일부 정보는 다음과 같습니다.

- 승인자는 역할에 대 한 요청이 검토를 보류 중인 경우 전자 메일로 알립니다. 메일 알림에는 승인자를 승인하거나 거부할 수 있는 요청에 대한 직접 링크가 포함됩니다.
- 요청은 승인 또는 거부 하는 첫 번째 승인자에 의해 확인 됩니다.
- 승인자가 요청에 응답 하면 모든 승인자에 게 해당 작업에 대 한 알림이 표시 됩니다.
- 승인 된 사용자가 해당 역할에서 활성화 되 면 리소스 관리자에 게 알림이 제공 됩니다.

>[!Note]
>승인 된 사용자가 활성 상태가 아니어야 한다고 생각 하는 리소스 관리자는 Privileged Identity Management에서 활성 역할 할당을 제거할 수 있습니다. 리소스 관리자가 승인자가 아니면 보류 중인 요청에 대 한 알림이 표시 되지 않지만 Privileged Identity Management에서 보류 중인 요청을 확인 하 여 모든 사용자에 대해 보류 중인 요청을 확인 하 고 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management의 전자 메일 알림](pim-email-notifications.md)
- [Privileged Identity Management에서 Azure AD 역할에 대 한 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
