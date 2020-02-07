---
title: PIM에서 Azure AD 역할 할당-Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할을 할당 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/05/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bec73ee7286ea831920fc20bd46cf43c2874081
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047094"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할 할당

전역 관리자는 Azure Active Directory (Azure AD)를 사용 하 여 **영구** Azure AD 관리자 역할 할당을 만들 수 있습니다. 이러한 역할은 [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) 또는 [PowerShell 명령](/powershell/module/azuread#directory_roles)을 사용하여 할당할 수 있습니다.

PIM (Azure AD Privileged Identity Management) 서비스를 사용 하면 권한 있는 역할 관리자가 영구 관리 역할 할당을 수행할 수도 있습니다. 또한 권한 있는 역할 관리자는 사용자에 게 Azure AD 관리자 역할에 대 한 **자격** 을 제공할 수 있습니다. 적격인 관리자는 필요할 때 역할을 활성화할 수 있으며 작업을 완료하고 나면 권한이 만료됩니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

11 월 2019부터 Privileged Identity Management의 Azure AD 역할 부분은 Azure 리소스 역할의 환경과 일치 하는 새 버전으로 업데이트 됩니다. 그러면 [기존 API에 대 한 변경](azure-ad-roles-features.md#api-changes)뿐만 아니라 추가 기능이 생성 됩니다. 새 버전이 롤아웃 되는 동안이 문서에서 수행 하는 절차는 현재 보유 하 고 있는 Privileged Identity Management 버전에 따라 달라 집니다. 이 섹션의 단계에 따라 Privileged Identity Management 버전을 확인 합니다. Privileged Identity Management 버전을 확인 한 후에는이 문서에서 해당 버전과 일치 하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할에 있는 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. **Azure AD Privileged Identity Management**를 엽니다. 개요 페이지의 맨 위에 배너가 있는 경우이 문서의 **새 버전** 탭에 있는 지침을 따르세요. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

    ![Azure AD 역할 새 버전](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="new-versiontabnew"></a>[새 버전](#tab/new)

## <a name="assign-a-role"></a>역할 할당

사용자에 게 Azure AD 관리자 역할에 대 한 자격을 부여 하려면 다음 단계를 수행 합니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원 인 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

    다른 관리자에 게 Privileged Identity Management 관리 권한을 부여 하는 방법에 대 한 자세한 내용은 [다른 관리자에 게 Privileged Identity Management를 관리할](pim-how-to-give-access-to-pim.md)수 있는 권한 부여를 참조 하세요.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **역할** 을 선택 하 여 Azure AD 사용 권한에 대 한 역할 목록을 표시 합니다.

    ![Azure AD 역할](./media/pim-how-to-add-role-to-user/roles-list.png)

1. **구성원 추가** 를 선택 하 여 **새 할당** 페이지를 엽니다.

1. 역할 **선택을 선택 하 여** 역할 선택 페이지를 엽니다.

    ![새 할당 창](./media/pim-how-to-add-role-to-user/select-role.png)

1. 할당하려는 역할을 클릭한 다음, **선택**을 클릭합니다.

1. 역할에 할당 하려는 멤버를 선택한 다음 **선택**을 선택 합니다.

1. **멤버 자격 설정** 창의 **할당 유형** 목록에서 **적격** 또는 **활성**을 선택 합니다.

    - **적격** 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 멤버가 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 영구적으로 적격 하거나 영구적으로 할당 된 할당 인 경우 **영구적** 으로 확인란을 선택 합니다.

    역할 설정에 따라 이 확인란이 나타나지 않거나 수정 가능하지 않을 수 있습니다.

1. 특정 할당 기간을 지정하려면 확인란의 선택을 취소하고 시작 및/또는 종료 날짜/시간 상자를 수정합니다. 완료 되 면 **완료**를 선택 합니다.

    ![멤버 자격 설정 - 날짜 및 시간](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. 새 역할 할당을 만들려면 **추가**를 선택 합니다. 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. 관리 하려는 리소스 (예: 구독 또는 관리 그룹)를 선택 합니다.

1. 관리 아래에서 **역할** 을 선택 하 여 Azure 리소스에 대 한 역할 목록을 표시 합니다.

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. **업데이트** 또는 **제거**를 선택하여 역할 할당을 업데이트하거나 제거합니다.

    역할 할당을 확장 하는 방법에 대 한 자세한 내용은 [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)을 참조 하세요.

# <a name="previous-versiontabprevious"></a>[이전 버전](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>사용자를 역할에 적격 사용자로 지정

사용자에 게 Azure AD 관리자 역할에 대 한 자격을 부여 하려면 다음 단계를 수행 합니다.

1. **역할** 또는 **멤버**를 선택 합니다.

    ![Azure AD 역할](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **관리 되는 멤버 추가**를 열려면 **구성원 추가** 를 선택 합니다.

1. **역할 선택**을 선택 하 고 관리 하려는 역할을 선택한 다음 **선택**을 선택 합니다.

    ![역할 선택](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **구성원 선택**을 선택 하 고 역할에 할당 하려는 사용자를 선택한 다음 **선택**을 선택 합니다.

    ![역할 선택](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. **관리 되는 멤버 추가**에서 **확인** 을 선택 하 여 역할에 사용자를 추가 합니다.

1. 역할 목록에서 방금 할당 한 역할을 선택 하 여 멤버 목록을 표시 합니다.

     역할이 할당되면 선택한 사용자가 멤버 목록에 해당 역할의 **적격** 사용자로 표시됩니다.

    ![역할의 적격 사용자](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 이제 사용자에 게 역할에 대 한 자격이 있으므로 [Privileged Identity Management에서 내 AZURE AD 역할 활성화](pim-how-to-activate-role.md)의 지침에 따라 사용자가 해당 역할을 활성화할 수 있음을 알고 있어야 합니다.

    적격 관리자의 경우 활성화 중에 Azure MFA(Multi-factor Authentication)에 등록하라는 메시지가 표시됩니다. 사용자가 MFA에 등록 하거나 Microsoft 계정 (예: @outlook.com)를 사용 하는 경우 모든 해당 역할에서 영구적으로 설정 해야 합니다.

## <a name="make-a-role-assignment-permanent"></a>역할 할당을 영구적으로 지정

기본적으로 새 사용자는 Azure AD 관리자 역할에만 *적합* 합니다. 역할 할당을 영구적으로 지정하려면 다음 단계를 수행합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **구성원**을 선택합니다.

    ![멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 영구적으로 만들려는 **적격** 역할을 선택 합니다.

1. **자세히** 를 선택한 다음, **perm 만들기**를 선택 합니다.

    ![역할 할당을 영구 상태로 만들기](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    이제 역할이 **영구**로 표시됩니다.

    ![영구로 변경된 멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>역할에서 사용자를 제거합니다.

역할 할당에서 사용자를 제거할 수 있지만 항상 영구 전역 관리자 인 사용자가 하나 이상 있는지 확인 해야 합니다. 사용자에게 해당 역할 할당이 여전히 필요한지 확실하지 않은 경우에는 [역할에 대한 액세스 권한을 검토하기 시작](pim-how-to-start-security-review.md)할 수 있습니다.

Azure AD 관리자 역할에서 특정 사용자를 제거 하려면 다음 단계를 수행 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **구성원**을 선택합니다.

    ![멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 제거할 역할 할당을 선택 합니다.

1. **자세히** 를 선택 하 고 **제거**를 선택 합니다.

    ![역할 제거](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 확인 메시지가 표시 되 면 **예**를 선택 합니다.

    ![역할 제거](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    역할 할당이 제거됩니다.

## <a name="authorization-error-when-assigning-roles"></a>역할을 할당할 때 권한 부여 오류

최근 구독에 대 한 Privileged Identity Management를 사용 하도록 설정 했 고 사용자에 게 Azure AD 관리자 역할에 대 한 자격을 부여할 때 권한 부여 오류가 발생 하는 경우,이는 MS-PIM 서비스 주체가 아직 적절 한 권한을가지고 있지 않기 때문일 수 있습니다. 다른 사용자에 게 역할을 할당 하려면 MS-PIM 서비스 사용자에 게 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할이 있어야 합니다. MS-PIM에 사용자 액세스 관리자 역할이 할당될 때까지 대기하는 대신 수동으로 할당할 수 있습니다.

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

   ![역할 할당 추가 창-MS-PIM 서비스 사용자에 대 한 권한 추가](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. **저장**을 선택하여 역할을 할당합니다.

   몇 분 후 MS-PIM 서비스 주체가 구독 범위에서 사용자 액세스 관리자 역할로 할당됩니다.

   ![MS-PIM 서비스 사용자에 대 한 사용자 액세스 관리자 역할 할당을 보여 주는 액세스 제어 페이지](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 관리자 역할 설정 구성](pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)