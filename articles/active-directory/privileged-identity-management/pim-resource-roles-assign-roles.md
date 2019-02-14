---
title: PIM에서 Azure 리소스 역할 할당 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할을 할당하는 방법을 알아봅니다.
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
ms.date: 08/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c92387c14cd0b82ec26acb411609c794c2601e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171267"
---
# <a name="assign-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할 할당

Azure AD PIM에서는 기본 제공 Azure 리소스 역할뿐만 아니라 사용자 지정 역할(다음을 포함하지만 이에 국한되지 않음)도 관리할 수 있습니다.

- 소유자
- 사용자 액세스 관리자
- 참가자
- 보안 관리자
- 보안 관리자 등

>[!NOTE]
소유자 또는 사용자 액세스 관리자 역할에 할당된 사용자 또는 그룹 구성원 및 Azure AD에서 구독 관리를 사용할 수 있는 전역 관리자는 리소스 관리자입니다. 이러한 관리자는 Azure 리소스용 PIM을 사용하여 역할을 할당하고, 역할 설정을 구성하며, 액세스를 검토할 수 있습니다. [Azure 리소스에 대한 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 목록을 참조하세요.

## <a name="assign-a-role"></a>역할 할당

사용자를 Azure 리소스 역할에 대해 적격 사용자로 지정하려면 다음 단계를 따릅니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

    다른 관리자에게 PIM 관리를 위한 액세스 권한을 부여하는 방법에 대한 자세한 내용은 [다른 관리자에게 PIM을 관리하기 위한 액세스 권한 부여](pim-how-to-give-access-to-pim.md)를 참조하세요.

1. **Azure AD Privileged Identity Management**를 엽니다.

    Azure Portal에서 PIM을 아직 시작하지 않은 경우 [PIM 사용](pim-getting-started.md)으로 이동합니다.

1. **Azure 리소스**를 클릭합니다.

1. **리소스 필터**를 사용하여 관리되는 리소스의 목록을 필터링합니다.

    ![관리할 Azure 리소스 목록](./media/pim-resource-roles-assign-roles/resources-list.png)

1. 관리하려는 리소스(예: 구독 또는 관리 그룹)를 클릭합니다.

1. 관리 아래에서 **역할**을 클릭하여 Azure 리소스에 대한 역할 목록을 표시합니다.

    ![Azure 리소스 역할](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **멤버 추가**를 클릭하여 새 할당 창을 엽니다.

1. **역할 선택**을 클릭하여 역할 창을 엽니다.

    ![새 할당 창](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 할당하려는 역할을 클릭하고 **선택**을 클릭합니다.

    멤버 선택 또는 그룹 창이 열립니다.

1. 역할에 할당하려는 멤버 또는 그룹을 클릭하고 **선택**을 클릭합니다.

    ![멤버 또는 그룹 선택 창](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    멤버 자격 설정 창이 열립니다.

1. **할당 유형** 목록에서 **적격** 또는 **활성**을 선택합니다.

    ![멤버 자격 설정 창](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure 리소스에서 PIM은 두 개의 고유한 할당 형식을 제공합니다.

    - **적격** 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 멤버가 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 할당을 영구적으로 지정하려면(영구적으로 적격 또는 영구적으로 할당됨) **영구적으로** 확인란을 선택합니다.

    역할 설정에 따라 이 확인란이 나타나지 않거나 수정 가능하지 않을 수 있습니다.

1. 특정 할당 기간을 지정하려면 확인란의 선택을 취소하고 시작 및/또는 종료 날짜/시간 상자를 수정합니다.

    ![멤버 자격 설정 - 날짜 및 시간](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. 여기까지 마쳤으면 **완료**를 클릭합니다.

    ![새 할당 - 추가](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. 새 역할 할당을 만들려면 **추가**를 클릭합니다. 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 관리하려는 리소스(예: 구독 또는 관리 그룹)를 클릭합니다.

1. 관리 아래에서 **역할**을 클릭하여 Azure 리소스에 대한 역할 목록을 표시합니다.

    ![Azure 리소스 역할 - 역할 선택](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 업데이트 또는 제거하려는 역할을 클릭합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. **업데이트** 또는 **제거**를 클릭하여 역할 할당을 업데이트하거나 제거합니다.

    역할 할당을 확장하는 방법에 대한 내용은 [PIM에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [PIM에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [PIM에서 Azure AD 디렉터리 역할 할당](pim-how-to-add-role-to-user.md)
