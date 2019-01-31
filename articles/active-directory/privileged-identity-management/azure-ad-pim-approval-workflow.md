---
title: PIM에서 Azure AD 디렉터리 역할에 대한 요청 승인 또는 거부 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 디렉터리 역할에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
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
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 1c4b3819494ecb4c0897edeb08e2ce45d867b626
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179075"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>PIM에서 Azure AD 디렉터리 역할에 대한 요청 승인 또는 거부

Azure AD PIM(Privileged Identity Management)을 사용하면, 활성화에 대한 승인이 필요한 역할을 구성하고 하나 이상의 사용자 또는 그룹을 위임된 승인자로 선택할 수 있습니다. 이 문서의 단계에 따라 Azure AD 디렉터리 역할에 대한 요청을 승인하거나 거부합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

Azure AD 디렉터리 역할 요청이 승인 보류 중일 때는 위임된 승인자가 이메일 알림을 받게 됩니다. PIM에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **요청 승인**을 클릭합니다.

    ![PIM Azure AD 디렉터리 역할 - 역할](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

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
