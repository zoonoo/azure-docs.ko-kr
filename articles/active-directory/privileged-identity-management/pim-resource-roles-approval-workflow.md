---
title: 승인 또는 거부 PIM-Azure Active Directory에서에서 Azure 리소스 역할에 대 한 요청 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f645b7077ef43dc7eb4d70261b6b601b5e4af1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60288478"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부

사용 하 여 Azure Active Directory (Azure AD) PIM Privileged Identity Management ()를 정품 인증에 대 한 승인이 필요한 역할을 구성를 하나 이상의 사용자 또는 그룹으로 위임 된 승인자를 선택 합니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

이 문서의 단계에 따라 Azure 리소스 역할에 대한 요청을 승인하거나 거부합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

Azure 리소스 역할 요청이 승인 보류 중일 때는 위임된 승인자가 이메일 알림을 받게 됩니다. PIM에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **요청 승인**을 클릭합니다.

    ![Azure 리소스 - 요청 승인](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인하려는 요청을 찾아 클릭합니다. 승인 창이 나타납니다.

    ![승인 요청 창](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **근거** 상자에 이유를 입력합니다.

1. **승인**을 클릭합니다.

    승인이 포함된 알림이 표시됩니다.

    ![승인 알림](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>요청 거부

1. 거부하려는 요청을 찾아 클릭합니다. 승인 창이 나타납니다.

    ![승인 요청 창](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **근거** 상자에 이유를 입력합니다.

1. **거부**를 클릭합니다.

    거부가 포함된 알림이 표시됩니다.

## <a name="workflow-notifications"></a>워크플로 알림

워크플로 알림에 대한 일부 정보는 다음과 같습니다.

- 역할에 대한 요청이 해당 검토를 보류할 때 승인자 목록의 모든 멤버는 이메일을 통해 알림을 받습니다. 메일 알림에는 승인자가 승인 또는 거부할 수 있는 요청에 대한 직접 링크가 포함되어 있습니다.
- 요청은 승인 또는 거부 권한이 있는 승인자 목록의 첫번째 멤버가 해결합니다.
- 승인자가 요청에 응답하는 경우 승인자 목록의 모든 멤버에게 작업의 알림이 표시됩니다.
- 승인된 멤버가 해당 역할에서 활성화될 때 리소스 관리자에게 알림이 표시됩니다.

>[!Note]
>승인된 멤버를 활성화하지 않아야 한다고 판단하는 경우 리소스 관리자는 PIM의 활성 역할 할당을 제거할 수 있습니다. 리소스 관리자가 승인자 목록의 멤버가 아니면 보류 중인 요청의 알림을 받지 않지만 PIM에서 보류 중인 요청을 확인하여 모든 사용자의 보류 중인 요청을 확인하고 취소할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [PIM에서 이메일 알림](pim-email-notifications.md)
- [승인 또는 거부 PIM에서 Azure AD 역할에 대 한 요청](azure-ad-pim-approval-workflow.md)
