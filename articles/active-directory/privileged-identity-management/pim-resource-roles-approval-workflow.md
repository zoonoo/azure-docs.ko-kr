---
title: PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부 | Microsoft Docs
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
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b7b362cf711cc6424d1eb3daa19701ee22249741
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151518"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부

Azure AD PIM(Privileged Identity Management)을 사용하면, 활성화에 대한 승인이 필요한 역할을 구성하고 하나 이상의 사용자 또는 그룹을 위임된 승인자로 선택할 수 있습니다. 이 문서의 단계에 따라 Azure 리소스 역할에 대한 요청을 승인하거나 거부합니다.

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
- [PIM에서 Azure AD 디렉터리 역할에 대한 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
