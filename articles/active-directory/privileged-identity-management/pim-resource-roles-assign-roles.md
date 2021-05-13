---
title: Privileged Identity Management에서 Azure 리소스 역할 할당 - Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할을 할당하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 011b50449f0a02b128bc000535f93e1809fd9777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673884"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할 할당

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)는 사용자 지정 역할 뿐만 아니라 기본 제공 Azure 리소스 역할을 관리할 수 있습니다(다음을 포함하되 국한되지 않음).

- 소유자
- 사용자 액세스 관리자
- 참가자
- 보안 관리자
- 보안 관리자

> [!NOTE]
> 소유자 또는 사용자 액세스 관리자 구독 역할에 할당된 사용자 또는 그룹 멤버와 Azure AD에서 구독 관리를 사용하도록 설정한 Azure AD 전역 관리자에게는 기본적으로 리소스 관리자 권한이 있습니다. 이러한 관리자는 Azure 리소스용 Privileged Identity Management를 사용하여 역할을 할당하고, 역할 설정을 구성하며, 액세스를 검토할 수 있습니다. 사용자는 리소스 관리자 권한이 없으면 리소스에 대한 Privileged Identity Management를 관리할 수 없습니다. [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)의 목록을 봅니다.

## <a name="assign-a-role"></a>역할 할당

사용자를 Azure 리소스 역할에 대해 적격 사용자로 지정하려면 다음 단계를 따릅니다.

1. 소유자 또는 사용자 액세스 관리자 역할 권한을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

    다른 관리자에게 Privileged Identity Management를 관리하기 위한 액세스 권한을 부여하는 방법에 대한 자세한 내용은 [ 관리를 위해 다른 관리자에게 액세스 권한 부여](pim-how-to-give-access-to-pim.md)를 참조하세요.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure 리소스** 를 선택합니다.

1. 리소스 필터를 사용하여 원하는 관리되는 리소스를 찾을 수 있습니다.

    ![관리할 Azure 리소스 목록](./media/pim-resource-roles-assign-roles/resources-list.png)

1. 관리하려는 리소스를 선택하여 리소스 개요 페이지를 엽니다.

1. **관리** 아래에서 **역할** 을 선택하여 Azure 리소스에 대한 역할 목록을 표시합니다.

    ![Azure 리소스 역할](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **할당 추가** 를 선택하여 **할당 추가** 창을 엽니다.

1. **역할 선택** 을 선택하여 **역할 선택** 페이지를 엽니다.

    ![새 할당 창](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 할당하려는 역할을 클릭한 다음, **선택** 을 클릭합니다.

    **Select a member or group**(멤버 또는 그룹 선택) 창이 열립니다.

1. 역할에 할당하려는 멤버 또는 그룹을 선택하고 **선택** 을 클릭합니다.

    ![멤버 또는 그룹 선택 창](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. **설정** 탭의 **할당 유형** 목록에서 **적격** 또는 **할당** 을 선택합니다.

    ![멤버 자격 설정 창](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure 리소스를 위한 Privileged Identity Management는 두 개의 고유한 할당 형식을 제공합니다.

    - **적격** 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 구성원이 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 특정 할당 기간을 지정하려면 시작 날짜 및 종료 날짜와 시간을 변경합니다.

1. 작업을 마쳤으면 **할당하기** 를 선택합니다.

1. 새 역할 할당이 만들어지면 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure 리소스** 를 선택합니다.

1. 관리하려는 리소스를 선택하여 개요 페이지를 엽니다.

1. **관리** 아래에서 **역할** 을 선택하여 Azure 리소스에 대한 역할 목록을 표시합니다.

    ![Azure 리소스 역할 - 역할 선택](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. **업데이트** 또는 **제거** 를 선택하여 역할 할당을 업데이트하거나 제거합니다.

    역할 할당을 확장하는 방법에 대한 내용은 [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 할당 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
