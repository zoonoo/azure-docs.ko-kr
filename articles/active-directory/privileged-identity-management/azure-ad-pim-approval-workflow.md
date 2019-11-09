---
title: PIM에서 Azure AD 역할에 대 한 요청 승인 또는 거부 | Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할에 대 한 요청을 승인 하거나 거부 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f663f7daad19e77dcc1cc95a6a324e881d92b28
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847122"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대 한 요청 승인 또는 거부

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)를 사용 하 여 활성화에 대 한 승인이 필요 하도록 역할을 구성 하 고 하나 이상의 사용자 또는 그룹을 위임 된 승인자로 선택할 수 있습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

이 문서의 단계에 따라 Azure AD 역할에 대 한 요청을 승인 하거나 거부 합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

위임 승인자는 Azure AD 역할 요청이 승인 보류 중인 경우 전자 메일 알림을 받게 됩니다. Privileged Identity Management에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **요청 승인**을 클릭합니다.

    ![Azure AD 역할-요청 승인](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    승인 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인할 요청을 클릭하고 **승인**을 클릭하여 선택한 요청 승인 창을 엽니다.

    ![승인 옵션이 강조 표시 된 요청 승인 목록](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. **승인 이유** 상자에 이유를 입력합니다.

    ![승인 이유를 사용 하 여 선택한 요청 창 승인](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. **승인**을 클릭합니다.

    상태 기호가 승인으로 업데이트됩니다.

    ![승인 단추를 클릭 한 후 선택한 요청 창 승인](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 클릭하고 **거부**를 클릭하여 선택한 요청 거부 창을 엽니다.

    ![거부 옵션이 강조 표시 된 요청 승인 목록](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. **거부 이유** 상자에 이유를 입력합니다.

    ![거부 이유를 사용 하 여 선택한 요청 창 거부](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. **거부**를 클릭합니다.

    상태 기호가 거부로 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management의 전자 메일 알림](pim-email-notifications.md)
- [Privileged Identity Management에서 Azure 리소스 역할에 대 한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
