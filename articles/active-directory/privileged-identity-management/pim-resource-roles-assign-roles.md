---
title: Privileged Identity Management에서 Azure 리소스 역할 할당-Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할을 할당하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895955"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할 할당

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)는 기본 제공 Azure 리소스 역할 뿐만 아니라 사용자 지정 역할을 관리할 수 있습니다 (다음을 포함 하지만이에 국한 되지 않음).

- 소유자
- 사용자 액세스 관리자
- 기여자
- 보안 관리자
- 보안 관리자

> [!NOTE]
> 소유자 또는 사용자 액세스 관리자 구독 역할에 할당 된 그룹의 사용자 또는 멤버와 Azure AD에서 구독 관리를 사용 하도록 설정 하는 Azure AD 전역 관리자는 기본적으로 리소스 관리자 권한을 가집니다. 이러한 관리자는 Azure 리소스에 대 한 Privileged Identity Management 사용 하 여 역할을 할당 하 고, 역할 설정을 구성 하 고, 액세스를 검토할 수 있습니다 사용자는 리소스 관리자 권한이 없는 리소스에 대 한 Privileged Identity Management를 관리할 수 없습니다. [Azure 리소스에 대한 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 목록을 참조하세요.

## <a name="assign-a-role"></a>역할 할당

사용자를 Azure 리소스 역할에 대해 적격 사용자로 지정하려면 다음 단계를 따릅니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원 인 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

    다른 관리자에 게 Privileged Identity Management 관리 권한을 부여 하는 방법에 대 한 자세한 내용은 [다른 관리자에 게 Privileged Identity Management를 관리할](pim-how-to-give-access-to-pim.md)수 있는 권한 부여를 참조 하세요.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. **리소스 필터**를 사용하여 관리되는 리소스의 목록을 필터링합니다.

    ![관리할 Azure 리소스 목록](./media/pim-resource-roles-assign-roles/resources-list.png)

1. 관리 하려는 리소스 (예: 구독 또는 관리 그룹)를 선택 합니다.

1. 관리 아래에서 **역할** 을 선택 하 여 Azure 리소스에 대 한 역할 목록을 표시 합니다.

    ![Azure 리소스 역할](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **구성원 추가** 를 선택 하 여 새 할당 창을 엽니다.

1. 역할 **선택을 선택 하 여** 역할 선택 창을 엽니다.

    ![새 할당 창](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 할당하려는 역할을 클릭한 다음, **선택**을 클릭합니다.

    멤버 선택 또는 그룹 창이 열립니다.

1. 역할에 할당 하려는 구성원 또는 그룹을 선택한 다음 **선택**을 클릭 합니다.

    ![멤버 또는 그룹 선택 창](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    멤버 자격 설정 창이 열립니다.

1. **할당 유형** 목록에서 **적격** 또는 **활성**을 선택합니다.

    ![멤버 자격 설정 창](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure 리소스에 대 한 Privileged Identity Management는 다음과 같은 두 가지 고유한 할당 유형을 제공 합니다.

    - **적격** 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 멤버가 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 영구적으로 적격 하거나 영구적으로 할당 된 할당 인 경우 **영구적** 으로 확인란을 선택 합니다.

    역할 설정에 따라 이 확인란이 나타나지 않거나 수정 가능하지 않을 수 있습니다.

1. 특정 할당 기간을 지정하려면 확인란의 선택을 취소하고 시작 및/또는 종료 날짜/시간 상자를 수정합니다.

    ![멤버 자격 설정 - 날짜 및 시간](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. 완료 되 면 **완료**를 선택 합니다.

    ![새 할당 - 추가](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. 새 역할 할당을 만들려면 **추가**를 선택 합니다. 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 관리 하려는 리소스 (예: 구독 또는 관리 그룹)를 선택 합니다.

1. 관리 아래에서 **역할** 을 선택 하 여 Azure 리소스에 대 한 역할 목록을 표시 합니다.

    ![Azure 리소스 역할 - 역할 선택](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. **업데이트** 또는 **제거**를 선택하여 역할 할당을 업데이트하거나 제거합니다.

    역할 할당을 확장 하는 방법에 대 한 자세한 내용은 [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
