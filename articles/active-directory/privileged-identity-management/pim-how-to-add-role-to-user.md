---
title: PIM - Azure Active Directory에서 Azure AD 역할 할당 | 마이크로 소프트 문서
description: AZURE AD 권한 ID 관리(PIM)에서 Azure AD 역할을 할당하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253274"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure AD 역할 할당

Azure Active Directory(Azure AD)를 사용하면 전역 관리자가 **영구적인** Azure AD 관리자 역할 할당을 만들 수 있습니다. 이러한 역할은 [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) 또는 [PowerShell 명령](/powershell/module/azuread#directory_roles)을 사용하여 할당할 수 있습니다.

또한 AZURE AD 권한 ID 관리(PIM) 서비스를 사용하면 권한 있는 역할 관리자가 영구 관리자 역할 할당을 수행할 수 있습니다. 또한 권한 있는 역할 관리자는 사용자가 Azure AD 관리자 역할을 **받을 수** 있도록 할 수 있습니다. 적격인 관리자는 필요할 때 역할을 활성화할 수 있으며 작업을 완료하고 나면 권한이 만료됩니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

2019년 11월부터 권한 있는 Id Management의 Azure AD 역할 부분이 Azure 리소스 역할에 대한 환경과 일치하는 새 버전으로 업데이트됩니다. 이렇게 하면 [기존 API에 대한 변경](azure-ad-roles-features.md#api-changes)사항뿐만 아니라 추가 기능이 만들어집니다. 새 버전이 롤아웃되는 동안 이 문서에서 따르는 절차는 현재 가지고 있는 권한 있는 ID 관리 버전에 따라 다릅니다. 이 섹션의 단계에 따라 사용 중인 권한 ID 관리 버전을 확인합니다. 권한 있는 ID 관리 버전을 알고 나면 이 문서에서 해당 버전과 일치하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할에 있는 사용자와 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. Azure **AD 권한 ID 관리**를 엽니다. 개요 페이지 상단에 배너가 있는 경우 이 문서의 **새 버전** 탭의 지침을 따릅니다. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[새 버전](#tab/new)

## <a name="assign-a-role"></a>역할 할당

다음 단계에 따라 사용자가 Azure AD 관리자 역할을 받을 수 있도록 합니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자와 Azure [포털에](https://portal.azure.com/) 로그인합니다.

    권한 있는 ID 관리를 관리 하기 위해 다른 관리자 액세스 권한을 부여 하는 방법에 대 한 자세한 내용은 [권한 있는 ID 관리를 관리 하는 다른 관리자에 대 한 권한 부여 액세스](pim-how-to-give-access-to-pim.md)참조.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. Azure AD 사용 권한의 역할 목록을 보려면 **역할을** 선택합니다.

    ![Azure AD 역할](./media/pim-how-to-add-role-to-user/roles-list.png)

1. **새 과제** 페이지를 열려면 **멤버 추가를** 선택합니다.

1. **역할 선택을** 선택하여 역할 선택 페이지를 엽니다.

    ![새 할당 창](./media/pim-how-to-add-role-to-user/select-role.png)

1. 할당하려는 역할을 클릭한 다음, **선택**을 클릭합니다.

1. 역할에 할당할 멤버를 선택한 다음 **선택 을**선택합니다.

1. **멤버 자격 설정** 창의 과제 **유형** 목록에서 **적격** 또는 **활성**을 선택합니다.

    - **적격** 할당은 역할을 사용하기 위한 작업을 수행하기 위해 역할의 구성원이 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당은 멤버가 역할을 사용하기 위해 어떤 작업도 수행할 필요가 없습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 할당이 영구적이어야 하는 경우(영구적으로 자격이 있거나 영구적으로 할당됨) **영구** 확인란을 선택합니다.

    역할 설정에 따라 이 확인란이 나타나지 않거나 수정 가능하지 않을 수 있습니다.

1. 특정 할당 기간을 지정하려면 확인란의 선택을 취소하고 시작 및/또는 종료 날짜/시간 상자를 수정합니다. 완료되면 **완료를 선택합니다.**

    ![멤버 자격 설정 - 날짜 및 시간](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 새 역할 할당을 만들려면 **추가**를 선택합니다. 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. Azure AD의 역할 목록을 보려면 **역할을** 선택합니다.

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. **업데이트** 또는 **제거**를 선택하여 역할 할당을 업데이트하거나 제거합니다.

# <a name="previous-version"></a>[이전 버전](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>사용자를 역할에 적격 사용자로 지정

다음 단계에 따라 사용자가 Azure AD 관리자 역할을 받을 수 있도록 합니다.

1. **역할** 또는 **구성원을**선택합니다.

    ![Azure AD 역할](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **관리되는 멤버 추가를**열려면 **멤버 추가를** 선택합니다.

1. **역할 선택을**선택하고 관리할 역할을 선택한 다음 을 **선택합니다.**

    ![역할 선택](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **구성원 선택을**선택하고 역할에 할당할 사용자를 선택한 다음 을 **선택합니다.**

    ![역할 선택](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. **관리되는 구성원 추가에서** **확인을** 선택하여 역할에 사용자를 추가합니다.

1. 역할 목록에서 방금 지정한 역할을 선택하여 구성원 목록을 확인합니다.

     역할이 할당되면 선택한 사용자가 구성원 목록에 해당 역할에 대한 **적격으로** 표시됩니다.

    ![역할의 적격 사용자](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 이제 사용자가 역할에 대한 자격이 있으므로 [권한 있는 ID 관리에서 내 Azure AD 역할 활성화의 지침에](pim-how-to-activate-role.md)따라 해당 역할을 활성화할 수 있음을 알려주십시오.

    적격 관리자의 경우 활성화 중에 Azure MFA(Multi-factor Authentication)에 등록하라는 메시지가 표시됩니다. 사용자가 MFA에 등록할 수 없거나 Microsoft 계정(예:)을 @outlook.com사용하는 경우 모든 역할에서 영구적으로 만들어야 합니다.

## <a name="make-a-role-assignment-permanent"></a>역할 할당을 영구적으로 지정

기본적으로 새 사용자는 Azure AD 관리자 역할만 *받을 수* 있습니다. 역할 할당을 영구적으로 지정하려면 다음 단계를 수행합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. **구성원**을 선택합니다.

    ![멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 영구적으로 만들 수 있는 **적격** 역할을 선택합니다.

1. **더 보기를** 선택한 다음 **파마 만들기를**선택합니다.

    ![역할 할당을 영구 상태로 만들기](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    이제 역할이 **영구**로 표시됩니다.

    ![영구로 변경된 멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>역할에서 사용자 제거

역할 할당에서 사용자를 제거할 수 있지만 항상 영구 글로벌 관리자인 사용자가 한 명 이상 있는지 확인합니다. 사용자에게 해당 역할 할당이 여전히 필요한지 확실하지 않은 경우에는 [역할에 대한 액세스 권한을 검토하기 시작](pim-how-to-start-security-review.md)할 수 있습니다.

다음 단계에 따라 Azure AD 관리자 역할에서 특정 사용자를 제거합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. **구성원**을 선택합니다.

    ![멤버 목록](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 제거할 역할 할당을 선택합니다.

1. **더 보기를** 선택한 다음 **에서 제거를**선택합니다.

    ![역할 제거](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 확인을 요청하는 메시지에서 **예**를 선택합니다.

    ![역할 제거](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    역할 할당이 제거됩니다.

## <a name="authorization-error-when-assigning-roles"></a>역할을 할당할 때 권한 부여 오류

최근에 구독에 대해 권한 있는 ID 관리를 사용하도록 설정하고 Azure AD 관리자 역할을 사용할 수 있도록 할 때 권한 부여 오류가 발생하면 MS-PIM 서비스 주체가 아직 적절한 사용 권한이 없기 때문일 수 있습니다. MS-PIM 서비스 주체는 다른 사람에게 역할을 할당할 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할이 있어야 합니다. MS-PIM에 사용자 액세스 관리자 역할이 할당될 때까지 대기하는 대신 수동으로 할당할 수 있습니다.

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

   ![역할 할당 창 추가 - MS-PIM 서비스 주체에 대한 사용 권한 추가](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. **저장**을 선택하여 역할을 할당합니다.

   몇 분 후 MS-PIM 서비스 주체가 구독 범위에서 사용자 액세스 관리자 역할로 할당됩니다.

   ![MS-PIM 서비스 주체에 대한 사용자 액세스 관리자 역할 할당을 보여주는 액세스 제어 페이지](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 관리자 역할 설정 구성](pim-how-to-change-default-settings.md)
- [권한 있는 ID 관리에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)