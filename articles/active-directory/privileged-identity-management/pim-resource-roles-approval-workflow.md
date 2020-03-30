---
title: PIM - Azure AD에서 Azure 리소스 역할에 대한 요청 승인 | 마이크로 소프트 문서
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021980"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure 리소스 역할에 대한 요청을 승인 하거나 거부합니다.

Azure Active Directory(Azure AD)에서 PIM(권한 있는 ID 관리)을 사용하면 활성화에 대한 승인을 요구하는 역할을 구성하고 Azure AD 조직의 사용자 또는 그룹을 위임된 승인자로 선택할 수 있습니다. 권한 있는 역할 관리자의 워크로드를 줄이려면 각 역할에 대해 두 명 이상의 승인자를 선택하는 것이 좋습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

이 문서의 단계에 따라 Azure 리소스 역할에 대한 요청을 승인하거나 거부합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

Azure 리소스 역할 요청이 승인 보류 중일 때는 위임된 승인자가 이메일 알림을 받게 됩니다. 권한 있는 ID 관리에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **요청 승인을**선택합니다.

    ![요청 승인 - 검토 요청을 표시하는 Azure 리소스 페이지](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인할 요청을 찾아 선택합니다. 승인 또는 거부 페이지가 나타납니다.

    ![요청 승인 - 세부 정보 및 자리 맞추기 상자로 창 승인 또는 거부](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **[자리 맞추기]** 상자에 비즈니스 근거를 입력합니다.

1. **을 선택합니다.** 승인에 대한 Azure 알림을 받게 됩니다.

    ![요청이 승인된 것으로 표시된 알림 승인](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

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
- 리소스 관리자는 승인된 사용자가 해당 역할에 활성화되면 알림을 받습니다.

>[!Note]
>승인된 사용자가 활성 상태가 되어서는 안 된다고 생각하는 리소스 관리자는 권한 있는 ID 관리에서 활성 역할 할당을 제거할 수 있습니다. 리소스 관리자는 승인자가 아닌 보류 중인 요청에 대한 알림을 받지 않지만 권한 있는 ID 관리에서 보류 중인 요청을 확인하여 모든 사용자에 대해 보류 중인 요청을 보고 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [권한 있는 ID 관리의 전자 메일 알림](pim-email-notifications.md)
- [권한 있는 ID 관리에서 Azure AD 역할에 대한 요청을 승인 하거나 거부합니다.](azure-ad-pim-approval-workflow.md)
