---
title: 권한 있는 ID 관리 - Azure Active Directory에서 Azure 리소스 역할 할당 | 마이크로 소프트 문서
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266560"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure 리소스 역할 할당

Azure Active Directory(Azure AD) 권한 있는 ID 관리(PIM)는 기본 제공 Azure 리소스 역할뿐만 아니라 사용자 지정 역할(이에 국한되지 않음)을 관리할 수 있습니다.

- 소유자
- 사용자 액세스 관리자
- 참가자
- 보안 관리자
- 보안 관리자

> [!NOTE]
> 소유자 또는 사용자 액세스 관리자 구독 역할에 할당된 그룹의 사용자 또는 구성원과 Azure AD에서 구독 관리를 사용하도록 설정하는 Azure AD Global 관리자에는 기본적으로 리소스 관리자 권한이 있습니다. 이러한 관리자는 Azure 리소스에 대한 권한 있는 ID 관리를 사용하여 역할을 할당하고, 역할 설정을 구성하고, 액세스를 검토할 수 있습니다. 사용자는 리소스 관리자 권한 없이 리소스에 대한 권한 있는 ID 관리를 관리할 수 없습니다. [Azure 리소스에 대한 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 목록을 참조하세요.

## <a name="assign-a-role"></a>역할 할당

사용자를 Azure 리소스 역할에 대해 적격 사용자로 지정하려면 다음 단계를 따릅니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자와 Azure [포털에](https://portal.azure.com/) 로그인합니다.

    권한 있는 ID 관리를 관리 하기 위해 다른 관리자 액세스 권한을 부여 하는 방법에 대 한 자세한 내용은 [권한 있는 ID 관리를 관리 하는 다른 관리자에 대 한 권한 부여 액세스](pim-how-to-give-access-to-pim.md)참조.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. **리소스 필터**를 사용하여 관리되는 리소스의 목록을 필터링합니다.

    ![관리할 Azure 리소스 목록](./media/pim-resource-roles-assign-roles/resources-list.png)

1. 구독 또는 관리 그룹과 같이 관리할 리소스를 선택합니다.

1. 관리에서 **역할을** 선택하여 Azure 리소스의 역할 목록을 확인합니다.

    ![Azure 리소스 역할](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. 새 할당 창을 열려면 **멤버 추가를** 선택합니다.

1. **역할 선택을** 선택하여 역할 선택 창을 엽니다.

    ![새 할당 창](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 할당하려는 역할을 클릭한 다음, **선택**을 클릭합니다.

    멤버 선택 또는 그룹 창이 열립니다.

1. 역할에 할당할 구성원 또는 그룹을 선택한 다음 **선택**을 클릭합니다.

    ![멤버 또는 그룹 선택 창](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    멤버 자격 설정 창이 열립니다.

1. **할당 유형** 목록에서 **적격** 또는 **활성**을 선택합니다.

    ![멤버 자격 설정 창](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure 리소스에 대한 권한 있는 ID 관리는 두 가지 고유한 할당 유형을 제공합니다.

    - **적격** 할당은 역할을 사용하기 위한 작업을 수행하기 위해 역할의 구성원이 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당은 멤버가 역할을 사용하기 위해 어떤 작업도 수행할 필요가 없습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 할당이 영구적이어야 하는 경우(영구적으로 자격이 있거나 영구적으로 할당됨) **영구** 확인란을 선택합니다.

    역할 설정에 따라 이 확인란이 나타나지 않거나 수정 가능하지 않을 수 있습니다.

1. 특정 할당 기간을 지정하려면 확인란의 선택을 취소하고 시작 및/또는 종료 날짜/시간 상자를 수정합니다.

    ![멤버 자격 설정 - 날짜 및 시간](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. 완료되면 **완료를 선택합니다.**

    ![새 할당 - 추가](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. 새 역할 할당을 만들려면 **추가**를 선택합니다. 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 구독 또는 관리 그룹과 같이 관리할 리소스를 선택합니다.

1. 관리에서 **역할을** 선택하여 Azure 리소스의 역할 목록을 확인합니다.

    ![Azure 리소스 역할 - 역할 선택](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. **업데이트** 또는 **제거**를 선택하여 역할 할당을 업데이트하거나 제거합니다.

    역할 할당 확장에 대한 자세한 내용은 [권한 있는 ID 관리에서 Azure 리소스 역할 확장 또는 갱신을](pim-resource-roles-renew-extend.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [권한 있는 ID 관리에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [권한 있는 ID 관리에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
