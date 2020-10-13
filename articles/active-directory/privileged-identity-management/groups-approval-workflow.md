---
title: Privileged Identity Management에서 그룹 구성원 및 소유자에 대 한 활성화 요청 승인-Azure AD
description: Azure AD Privileged Identity Management (PIM)에서 역할 할당 가능 그룹에 대 한 요청을 승인 하거나 거부 하는 방법에 대해 알아봅니다.
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536990"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>권한 있는 액세스 그룹 구성원 및 소유자에 대 한 활성화 요청 승인 (미리 보기)

Azure Active Directory (Azure AD)에서 Privileged Identity Management (PIM)를 사용 하 여 활성화에 대 한 승인이 필요 하도록 권한 있는 액세스 그룹 구성원 및 소유자를 구성 하 고 Azure AD 조직에서 위임 된 승인자로 사용자 또는 그룹을 선택할 수 있습니다. 권한 있는 역할 관리자에 대 한 작업을 줄이려면 각 그룹에 대해 둘 이상의 승인자를 선택 하는 것이 좋습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

이 문서의 단계에 따라 Azure 리소스 역할에 대한 요청을 승인하거나 거부합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

Azure 리소스 역할 요청이 승인 보류 중일 때는 위임된 승인자가 이메일 알림을 받게 됩니다. Privileged Identity Management에서 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **요청 승인**을 선택 합니다.

    ![요청 승인-검토 요청을 표시 하는 Azure 리소스 페이지](./media/groups-approval-workflow/groups-select-request.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인 하려는 요청을 찾아 선택 하 고 **승인**을 선택 합니다.

    !["승인" 및 "확인" 단추가 강조 표시 된 "요청 승인" 페이지를 보여 주는 스크린샷](./media/groups-approval-workflow/groups-confirm-approval.png)

1. **근거** 상자에 비즈니스 근거를 입력 합니다.

1. **확인**을 선택합니다. Azure 알림은 사용자의 승인에 의해 생성 됩니다.

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 찾아 선택 하 고 **거부**를 선택 합니다.

    ![요청 승인-세부 정보 및 근거 상자를 사용 하 여 승인 또는 거부 창](./media/groups-approval-workflow/groups-confirm-denial.png)

1. **근거** 상자에 비즈니스 근거를 입력 합니다.

1. **확인**을 선택합니다. Azure 알림은 거부에 의해 생성 됩니다.

## <a name="workflow-notifications"></a>워크플로 알림

워크플로 알림에 대한 일부 정보는 다음과 같습니다.

- 그룹 할당에 대 한 요청이 검토를 보류 중인 경우 승인자는 전자 메일을 통해 알림 메시지를 받습니다. 메일 알림에는 승인자가 승인 또는 거부할 수 있는 요청에 대한 직접 링크가 포함되어 있습니다.
- 요청은 승인 또는 거부 하는 첫 번째 승인자에 의해 확인 됩니다.
- 승인자가 요청에 응답 하면 모든 승인자에 게 해당 작업에 대 한 알림이 표시 됩니다.

>[!Note]
>승인 된 사용자가 활성 상태가 아니어야 한다고 생각 하는 관리자는 Privileged Identity Management에서 활성 그룹 할당을 제거할 수 있습니다. 리소스 관리자가 승인자가 아니면 보류 중인 요청에 대 한 알림이 표시 되지 않지만 Privileged Identity Management에서 보류 중인 요청을 확인 하 여 모든 사용자에 대해 보류 중인 요청을 확인 하 고 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 그룹 할당 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management의 전자 메일 알림](pim-email-notifications.md)
- [Privileged Identity Management의 그룹 할당에 대 한 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
