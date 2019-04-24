---
title: 승인 또는 거부 PIM-Azure Active Directory에서에서 Azure AD 역할에 대 한 요청 | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할에 대 한 요청을 승인 하거나 거부 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
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
ms.openlocfilehash: 47e9033b33c70a72a1685696f75a442e88eed033
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60289772"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>승인 또는 거부 PIM에서 Azure AD 역할에 대 한 요청

사용 하 여 Azure Active Directory (Azure AD) PIM Privileged Identity Management ()를 정품 인증에 대 한 승인이 필요한 역할을 구성를 하나 이상의 사용자 또는 그룹으로 위임 된 승인자를 선택 합니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

이 문서에서는 Azure AD 역할에 대 한 요청을 승인 하거나 거부의 단계를 수행 합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

위임 된 승인자를 받게 전자 메일 알림을 Azure AD 역할 요청 승인이 보류 중인 경우. PIM에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **요청 승인**을 클릭합니다.

    ![PIM Azure AD 역할-역할](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    승인 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인할 요청을 클릭하고 **승인**을 클릭하여 선택한 요청 승인 창을 엽니다.

    ![PIM 승인 요청 목록](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. **승인 이유** 상자에 이유를 입력합니다.

    ![PIM 선택한 요청 승인](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. **승인**을 클릭합니다.

    상태 기호가 승인으로 업데이트됩니다.

    ![PIM 선택한 요청 승인](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 클릭하고 **거부**를 클릭하여 선택한 요청 거부 창을 엽니다.

    ![PIM 승인 요청 목록](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. **거부 이유** 상자에 이유를 입력합니다.

    ![PIM 선택한 요청 거부](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. **거부**를 클릭합니다.

    상태 기호가 거부로 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 이메일 알림](pim-email-notifications.md)
- [PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
