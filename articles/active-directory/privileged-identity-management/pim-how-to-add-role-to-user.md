---
title: Privileged Identity Management에서 Azure AD 역할 할당-Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할을 할당 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809206"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할 할당

전역 관리자는 Azure Active Directory (Azure AD)를 사용 하 여 **영구** Azure AD 관리자 역할 할당을 만들 수 있습니다. 이러한 역할은 [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) 또는 [PowerShell 명령](/powershell/module/azuread#directory_roles)을 사용하여 할당할 수 있습니다.

PIM (Azure AD Privileged Identity Management) 서비스를 사용 하면 권한 있는 역할 관리자가 영구 관리 역할 할당을 수행할 수도 있습니다. 또한 권한 있는 역할 관리자는 사용자에 게 Azure AD 관리자 역할에 대 한 **자격** 을 제공할 수 있습니다. 적격인 관리자는 필요할 때 역할을 활성화할 수 있으며 작업을 완료하고 나면 권한이 만료됩니다.

## <a name="make-a-user-eligible-for-a-role"></a>사용자를 역할에 적격 사용자로 지정

사용자에 게 Azure AD 관리자 역할에 대 한 자격을 부여 하려면 다음 단계를 수행 합니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

    다른 관리자에 게 Privileged Identity Management 관리 권한을 부여 하는 방법에 대 한 자세한 내용은 [다른 관리자에 게 Privileged Identity Management를 관리할](pim-how-to-give-access-to-pim.md)수 있는 권한 부여를 참조 하세요.

1. **Azure AD Privileged Identity Management**를 엽니다.

    아직 Azure Portal에서 Privileged Identity Management를 시작 하지 않은 경우 [Privileged Identity Management 사용 시작](pim-getting-started.md)으로 이동 합니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **역할** 또는 **멤버**를 선택 합니다.

    ![역할 및 멤버 메뉴 옵션이 강조 표시 된 Azure AD 역할](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 관리 되는 멤버 추가를 열려면 **구성원 추가** 를 선택 합니다.

1. **역할 선택**을 선택 하 고 관리 하려는 역할을 선택한 다음 **선택**을 선택 합니다.

    ![Azure AD 역할을 나열 하는 역할 창 선택](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **구성원 선택**을 선택 하 고 역할에 할당 하려는 사용자를 선택한 다음 **선택**을 선택 합니다.

    ![사용자를 선택할 수 있는 구성원 창 선택](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 관리 되는 멤버 추가에서 **확인** 을 선택 하 여 역할에 사용자를 추가 합니다.

1. 역할 목록에서 방금 할당 한 역할을 선택 하 여 멤버 목록을 표시 합니다.

     역할이 할당되면 선택한 사용자가 멤버 목록에 해당 역할의 **적격** 사용자로 표시됩니다.

    ![역할의 멤버는 해당 활성화 상태와 함께 나열 됩니다.](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 이제 사용자에 게 역할에 대 한 자격이 있으므로 [Privileged Identity Management에서 내 AZURE AD 역할 활성화](pim-how-to-activate-role.md)의 지침에 따라 사용자가 해당 역할을 활성화할 수 있음을 알고 있어야 합니다.

    적격 관리자의 경우 활성화 중에 Azure MFA(Multi-factor Authentication)에 등록하라는 메시지가 표시됩니다. 사용자가 MFA에 등록할 수 없거나 Microsoft 계정(일반적으로 @outlook.com)을 사용하고 있는 경우 사용자 정보는 모든 역할에서 영구적으로 유지되도록 해야 합니다.

## <a name="make-a-role-assignment-permanent"></a>역할 할당을 영구적으로 지정

기본적으로 새 사용자는 Azure AD 관리자 역할에만 적합 합니다. 역할 할당을 영구적으로 지정하려면 다음 단계를 수행합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **구성원**을 선택합니다.

    ![Azure AD 역할-역할 및 활성화 상태를 보여 주는 구성원 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 영구적으로 만들려는 **적격** 역할을 선택 합니다.

1. **자세히** 를 선택한 다음, **perm 만들기**를 선택 합니다.

    ![추가 메뉴 옵션이 열려 있는 역할에 적합 한 사용자를 나열 하는 창](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    이제 역할이 **영구**로 표시됩니다.

    ![현재 영구적 인 역할 및 활성화 상태를 보여 주는 멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>역할에서 사용자 제거

역할 할당에서 사용자를 제거할 수 있지만 영구 전역 관리자인 사용자가 최소 한 명은 항상 있어야 합니다. 사용자에게 해당 역할 할당이 여전히 필요한지 확실하지 않은 경우에는 [역할에 대한 액세스 권한을 검토하기 시작](pim-how-to-start-security-review.md)할 수 있습니다.

Azure AD 관리자 역할에서 특정 사용자를 제거 하려면 다음 단계를 수행 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **구성원**을 선택합니다.

    ![Azure AD 역할-역할 및 활성화 통계를 보여 주는 멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 제거할 역할 할당을 선택 합니다.

1. **자세히** 를 선택 하 고 **제거**를 선택 합니다.

    ![더 많은 메뉴 옵션이 열려 있는 영구 역할이 있는 사용자를 나열 하는 창](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 작업을 확인 하 라는 메시지가 표시 되 면 **예**를 선택 합니다.

    ![역할에서 구성원을 제거할지 묻는 메시지](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    역할 할당이 제거됩니다.

## <a name="authorization-error-when-assigning-roles"></a>역할을 할당할 때 권한 부여 오류

시나리오: Azure 리소스에 대 한 활성 소유자 또는 사용자 액세스 관리자는 Privileged Identity Management 내에서 리소스를 볼 수 있지만 적격 할당을 수행 하거나 역할 할당 목록을 보는 등의 작업은 수행할 수 없습니다. 리소스 개요 페이지. 이러한 작업으로 인해 권한 부여 오류가 발생 합니다.

역할을 할당 하려면 azure AD 관리 역할이 아닌 azure 리소스 액세스에 대 한 Azure 역할 기반 액세스 제어에서 [사용자 액세스 관리자 역할](../../role-based-access-control/built-in-roles.md#user-access-administrator) 을 할당 받아야 합니다. MS-PIM에 사용자 액세스 관리자 역할이 할당될 때까지 대기하는 대신 수동으로 할당할 수 있습니다.

다음 단계에서는 구독에 대 한 사용자 액세스 관리자 역할을 MS-PIM 서비스 주체에 할당 합니다.

1. Azure AD 조직에서 전역 관리자로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

1. **모든 서비스**, **구독**을 차례로 선택합니다.

1. 구독을 선택합니다.

1. **액세스 제어(IAM)** 를 선택합니다.

1. **역할 할당**을 선택하면 구독 범위의 현재 역할 할당 목록을 볼 수 있습니다.

   ![구독의 액세스 제어(IAM) 블레이드](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. **MS-PIM** 서비스 주체가 **사용자 액세스 관리자** 역할로 할당되었는지 여부를 확인합니다.

1. 할당되지 않은 경우 **역할 할당 추가**를 선택하여 **역할 할당 추가** 창을 엽니다.

1. **역할** 드롭다운 목록에서 **사용자 액세스 관리자** 역할을 선택합니다.

1. **선택** 목록에서 **MS-PIM** 서비스 주체를 찾고 선택합니다.

   ![역할 할당 추가 창-MS-PIM 서비스 사용자에 대 한 권한 추가](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. **저장**을 선택하여 역할을 할당합니다.

   몇 분 후 MS-PIM 서비스 주체가 구독 범위에서 사용자 액세스 관리자 역할로 할당됩니다.

   ![MS-PIM에 대 한 사용자 액세스 관리자 역할 할당을 보여 주는 액세스 제어 (IAM) 블레이드](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 관리자 역할 설정 구성](pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
