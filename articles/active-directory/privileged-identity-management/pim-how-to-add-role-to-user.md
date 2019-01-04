---
title: PIM에서 Azure AD 디렉터리 역할 할당 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 디렉터리 역할을 할당하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 10/30/2018
ms.author: rolyon
ms.openlocfilehash: 2b099e1377536b46229b75f25d04ab2c1beb5c11
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724947"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>PIM에서 Azure AD 디렉터리 역할 할당

Azure AD(Azure Active Directory)를 사용할 경우 전역 관리자는 **영구** 디렉터리 역할을 할당할 수 있습니다. 이러한 역할은 [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) 또는 [PowerShell 명령](/powershell/module/azuread#directory_roles)을 사용하여 할당할 수 있습니다.

Azure AD PIM(Privileged Identity Management) 서비스를 사용하여 권한 있는 역할 관리자는 영구 디렉터리 역할을 할당할 수도 있습니다. 또한 권한이 있는 역할 관리자는 사용자를 디렉터리 역할에 **적격** 사용자로 만들 수 있습니다. 적격인 관리자는 필요할 때 역할을 활성화할 수 있으며 작업을 완료하고 나면 권한이 만료됩니다. PIM을 사용하여 관리할 수 있는 역할에 대한 자세한 내용은 [PIM에서 관리할 수 있는 Azure AD 디렉터리 역할](pim-roles.md)을 참조하세요.

## <a name="make-a-user-eligible-for-a-role"></a>사용자를 역할에 적격 사용자로 지정

사용자를 Azure AD 디렉터리 역할에 대해 적격 사용자로 지정하려면 다음 단계를 따릅니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

    다른 관리자에게 PIM 관리를 위한 액세스 권한을 부여하는 방법에 대한 자세한 내용은 [다른 관리자에게 PIM을 관리하기 위한 액세스 권한 부여](pim-how-to-give-access-to-pim.md)를 참조하세요.

1. **Azure AD Privileged Identity Management**를 엽니다.

    Azure Portal에서 PIM을 아직 시작하지 않은 경우 [PIM 사용](pim-getting-started.md)으로 이동합니다.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **역할** 또는 **멤버**를 클릭합니다.

    ![Azure AD 디렉터리 역할](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **멤버 추가**를 클릭하여 관리되는 멤버 추가를 엽니다.

1. **역할 선택**을 클릭하고 관리하려는 역할을 클릭한 후 **선택**을 클릭합니다.

    ![역할 선택](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **멤버 선택**을 클릭하고 역할에 할당할 사용자를 선택한 후 **선택**을 클릭합니다.

    ![역할 선택](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 관리되는 멤버 추가에서 **확인**을 클릭하여 역할에 사용자를 추가합니다.

1. 역할 목록에서 방금 할당한 역할을 클릭하여 멤버 목록을 봅니다.

     역할이 할당되면 선택한 사용자가 멤버 목록에 해당 역할의 **적격** 사용자로 표시됩니다.

    ![역할의 적격 사용자](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 이제 사용자가 역할을 수행할 수 있으므로 [PIM에서 Azure AD 디렉터리 역할 활성화](pim-how-to-activate-role.md)의 지침에 따라 사용자가 역할을 수행할 수 있음을 알립니다.

    적격 관리자의 경우 활성화 중에 Azure MFA(Multi-factor Authentication)에 등록하라는 메시지가 표시됩니다. 사용자가 MFA에 등록할 수 없거나 Microsoft 계정(일반적으로 @outlook.com)을 사용하고 있는 경우 사용자 정보는 모든 역할에서 영구적으로 유지되도록 해야 합니다.

## <a name="make-a-role-assignment-permanent"></a>역할 할당을 영구적으로 지정

기본적으로 새 사용자만 디렉터리 역할에 적격입니다. 역할 할당을 영구적으로 지정하려면 다음 단계를 수행합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **구성원**을 클릭합니다.

    ![멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 영구적으로 유지하려는 **적격** 역할을 클릭합니다.

1. **추가**를 클릭하고 **영구 상태로 만들기**를 클릭합니다.

    ![역할 할당을 영구 상태로 만들기](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    이제 역할이 **영구**로 표시됩니다.

    ![영구로 변경된 멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>역할에서 사용자 제거

역할 할당에서 사용자를 제거할 수 있지만 영구 전역 관리자인 사용자가 최소 한 명은 항상 있어야 합니다. 사용자에게 해당 역할 할당이 여전히 필요한지 확실하지 않은 경우에는 [역할에 대한 액세스 권한을 검토하기 시작](pim-how-to-start-security-review.md)할 수 있습니다.

특정 사용자를 디렉터리 역할에서 제거하려면 다음 단계를 수행합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **구성원**을 클릭합니다.

    ![멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 제거할 역할 할당을 클릭합니다.

1. **추가**를 클릭한 후 **제거**를 클릭합니다.

    ![역할 제거](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 확인하라는 메시지에서 **예**를 클릭합니다.

    ![역할 제거](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    역할 할당이 제거됩니다.

## <a name="authorization-error-when-assigning-roles"></a>역할을 할당할 때 권한 부여 오류

최근에 구독에 대해 PIM을 활성화하고 사용자를 디렉터리 역할에 대해 적격 상태로 설정하려고 할 때 권한 부여 오류가 발생하는 경우 MS-PIM 서비스 주체에 아직 적절한 권한이 없기 때문일 수 있습니다. MS-PIM 서비스 주체에는 다른 사용자에게 역할을 할당하는 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할이 있어야 합니다. MS-PIM에 사용자 액세스 관리자 역할이 할당될 때까지 대기하는 대신 수동으로 할당할 수 있습니다.

다음의 단계를 따라 구독의 MS-PIM 서비스 주체에 사용자 액세스 관리자 역할을 할당합니다.

1. 글로벌 관리자 권한으로 Azure Portal에 로그인합니다.

1. **모든 서비스**, **구독**을 차례로 선택합니다.

1. 구독을 선택합니다.

1. **액세스 제어(IAM)** 를 선택합니다.

1. **역할 할당**을 선택하면 구독 범위의 현재 역할 할당 목록을 볼 수 있습니다.

   ![구독의 액세스 제어(IAM) 블레이드](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. **MS-PIM** 서비스 주체가 **사용자 액세스 관리자** 역할로 할당되었는지 여부를 확인합니다.

1. 할당되지 않은 경우 **역할 할당 추가**를 선택하여 **역할 할당 추가** 창을 엽니다.

1. **역할** 드롭다운 목록에서 **사용자 액세스 관리자** 역할을 선택합니다.

1. **선택** 목록에서 **MS-PIM** 서비스 주체를 찾고 선택합니다.

   ![MS-PIM에 대한 권한 추가](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. **저장**을 선택하여 역할을 할당합니다.

   몇 분 후 MS-PIM 서비스 주체가 구독 범위에서 사용자 액세스 관리자 역할로 할당됩니다.

   ![MS-PIM에 대한 사용자 액세스 관리자 역할](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 디렉터리 역할 설정 구성](pim-how-to-change-default-settings.md)
- [PIM에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
