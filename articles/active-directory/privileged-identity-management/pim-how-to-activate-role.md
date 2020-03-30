---
title: PIM - Azure Active Directory에서 Azure AD 역할 활성화 | 마이크로 소프트 문서
description: PIM(Azure AD 권한 ID 관리)에서 Azure AD 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499163"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>PIM에서 내 Azure AD 역할 활성화

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.  

관리 역할에 대해 적격이 되었다면 권한이 필요한 작업을 수행해야 하는 경우 해당 역할을 활성화할 수 있다는 의미입니다. 예를 들어 경우에 따라 Office 365 기능을 관리하는 경우 조직의 권한 있는 역할 관리자는 해당 역할이 다른 서비스에도 또한 영향을 주므로 사용자를 영구적인 전역 관리자로 만들지 못할 수 있습니다. 대신 Exchange Online 관리자와 같은 Azure AD 역할에 대한 자격을 줍니다. 이러한 권한이 필요한 경우 해당 역할을 활성화하도록 요청하고 미리 결정된 기간 동안 제어할 수 있는 관리자 권한을 부여받습니다.

이 문서는 권한 있는 ID 관리에서 Azure AD 역할을 활성화해야 하는 관리자를 위한 것입니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

2019년 11월부터 권한 있는 Id Management의 Azure AD 역할 부분이 Azure 리소스 역할에 대한 환경과 일치하는 새 버전으로 업데이트됩니다. 이렇게 하면 [기존 API에 대한 변경](azure-ad-roles-features.md#api-changes)사항뿐만 아니라 추가 기능이 만들어집니다. 새 버전이 롤아웃되는 동안 이 문서에서 따르는 절차는 현재 가지고 있는 권한 있는 ID 관리 버전에 따라 다릅니다. 이 섹션의 단계에 따라 사용 중인 권한 ID 관리 버전을 확인합니다. 권한 있는 ID 관리 버전을 알고 나면 이 문서에서 해당 버전과 일치하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할을 사용하여 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. Azure **AD 권한 ID 관리**를 엽니다. 개요 페이지 상단에 배너가 있는 경우 이 문서의 **새 버전** 탭의 지침을 따릅니다. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[새 버전](#tab/new)

## <a name="activate-a-role"></a>역할 활성화

Azure AD 역할을 수임해야 하는 경우 권한 있는 ID 관리에서 **내 역할** 탐색 옵션을 사용하여 활성화를 요청할 수 있습니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다. 대시보드에 권한 ID 관리 타일을 추가하는 방법에 대한 자세한 내용은 [권한 있는 ID 관리 사용 시작을](pim-getting-started.md)참조하십시오.

1. **내 역할을**선택한 다음 **Azure AD 역할을** 선택하여 적격 Azure AD 역할 목록을 확인합니다.

    ![활성화할 수 있는 역할을 보여주는 역할 페이지](./media/pim-how-to-activate-role/my-roles.png)

1. Azure **AD 역할** 목록에서 활성화할 역할을 찾습니다.

    ![Azure AD 역할 - 적격 역할 목록](./media/pim-how-to-activate-role/activate-link.png)

1. 활성화창을 열려면 **활성화를** 선택합니다.

    ![Azure AD 역할 - 활성화 페이지에 기간 및 범위가 포함되어 있습니다.](./media/pim-how-to-activate-role/activate-page.png)

1. 다단계 인증이 필요한 역할은 **진행하기 전에 ID 확인**을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![역할 활성화 전에 MFA로 ID 확인](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **신원 확인을** 선택하고 지침에 따라 추가 보안 확인을 제공합니다.

    ![PIN 코드와 같은 보안 확인을 제공하는 화면](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 축소된 범위를 지정하려면 **범위를** 선택하여 필터 창을 엽니다. 필터 창에서 액세스해야 하는 Azure AD 리소스를 지정할 수 있습니다. 필요한 리소스에만 액세스를 요청하는 것이 좋습니다.

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다. Azure AD 역할은 선택한 시간 이후에 활성화됩니다.

1. **이유** 상자에 활성화 요청의 이유를 입력합니다.

1. **활성화**를 선택합니다.

    역할에 승인이 필요하지 않으면 활성화되고 활성 역할 목록에 추가됩니다. 역할을 사용하려면 다음 섹션의 단계를 따릅니다.

    ![범위, 시작 시간, 기간 및 이유가 있는 활성화 창 완료](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    [역할을 활성화하는 데 승인이 필요](pim-resource-roles-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려주는 알림이 표시됩니다.

    ![활성화 요청이 승인 알림 보류 중입니다.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>활성화 즉시 역할 사용

정품 인증 후 지연이 발생하는 경우 활성화한 후 다음 단계를 수행하여 Azure AD 역할을 즉시 사용합니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. 내 **역할을** 선택하여 적격 Azure AD 역할 및 Azure 리소스 역할 목록을 볼 수 있습니다.

1. **Azure AD 역할을**선택합니다.

1. 활성 **역할** 탭을 선택합니다.

1. 역할이 활성화되면 포털에서 로그아웃하고 다시 로그인합니다.

    이제 역할을 사용할 수 있습니다.

## <a name="view-the-status-of-your-requests"></a>요청 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청을** 선택하여 Azure AD 역할 및 Azure 리소스 역할 요청 목록을 볼 수 있습니다.

    ![내 요청 - 보류 중인 요청을 표시하는 Azure AD 페이지](./media/pim-how-to-activate-role/my-requests-page.png)

1. 오른쪽으로 스크롤하여 **요청 상태** 열을 봅니다.

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

승인이 필요한 역할을 활성화할 필요가 없으면 언제든지 보류 중인 요청을 취소할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청을**선택합니다.

1. 취소하려는 역할의 경우 **링크 취소를** 선택합니다.

    취소를 선택하면 요청이 취소됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

   ![취소 작업이 강조 표시된 내 요청 목록](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>문제 해결

### <a name="permissions-are-not-granted-after-activating-a-role"></a>역할을 활성화한 후 권한이 부여되지 않음

권한 있는 ID 관리에서 역할을 활성화하면 권한 있는 역할이 필요한 모든 포털에 활성화가 즉시 전파되지 않을 수 있습니다. 경우에 따라 변경 내용이 전파되더라도 포털의 웹 캐싱 때문에 변경 내용이 즉시 적용되지 않을 수 있습니다. 정품 인증이 지연되는 경우 다음과 같은 작업을 수행해야 합니다.

1. Azure Portal에서 로그아웃한 다음, 다시 로그인합니다.

    Azure AD 역할을 활성화하면 활성화 단계가 표시됩니다. 모든 단계가 완료되면 **로그아웃** 링크가 표시됩니다. 이 링크를 사용하여 로그아웃할 수 있습니다. 이렇게 하면 대부분의 경우 활성화 지연이 해결됩니다.

1. 권한 있는 ID 관리에서 역할의 구성원으로 나열되어 있는지 확인합니다.

# <a name="previous-version"></a>[이전 버전](#tab/previous)

## <a name="activate-a-role"></a>역할 활성화

Azure AD 역할을 수행해야 하는 경우 권한 있는 ID 관리에서 **내 역할** 탐색 옵션을 사용하여 활성화를 요청할 수 있습니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다. 대시보드에 권한 ID 관리 타일을 추가하는 방법에 대한 자세한 내용은 [권한 있는 ID 관리 사용 시작을](pim-getting-started.md)참조하십시오.

1. **Azure AD 역할**을 클릭합니다.

1. **내 역할을** 클릭하여 적격 Azure AD 역할 목록을 확인합니다.

    ![Azure AD 역할 - 적격 또는 활성 역할 목록을 표시하는 내 역할](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 활성화하려는 역할을 찾습니다.

    ![Azure AD 역할 - 활성화 링크를 표시하는 적격 역할 목록](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. **활성화**를 클릭하여 역할 활성화 세부 정보 창을 엽니다.

1. MFA(다단계 인증)가 필요한 역할인 경우 **진행하기 전에 ID 확인**을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![역할 활성화 전에 MFA로 ID 창 확인](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. **ID 확인**을 클릭하고 지침에 따라 추가 보안 확인을 제공합니다.

    ![연락 방법을 묻는 추가 보안 확인 페이지](./media/pim-how-to-activate-role/additional-security-verification.png)

1. **활성화**를 클릭하여 [활성화] 창을 엽니다.

    ![시작 시간, 기간, 티켓 및 이유를 지정하는 활성화 창](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다.

1. 활성화 기간을 지정합니다.

1. **활성화 이유** 상자에 활성화 요청의 이유를 입력합니다. 일부 역할은 문제 티켓 번호를 제공해야 합니다.

    ![사용자 지정 시작 시간, 기간, 티켓 및 이유가 있는 활성화 창 완료](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. **활성화**를 클릭합니다.

    역할에 승인이 필요하지 않으면 **활성화 상태를** 표시하는 활성화 상태 창이 나타납니다.

    ![활성화의 세 단계를 보여주는 활성화 상태 페이지](./media/pim-how-to-activate-role/activation-status.png)

    모든 단계가 완료되면 **로그아웃** 링크를 클릭하여 Azure 포털에서 로그아웃합니다. 포털에 다시 로그인하면 이제 역할을 사용할 수 있습니다.

    역할이 [활성화하기](./azure-ad-pim-approval-workflow.md) 위해 승인이 필요한 경우 브라우저의 오른쪽 상단에 요청이 승인 보류 중임을 알리는 Azure 알림이 표시됩니다.

## <a name="view-the-status-of-your-requests"></a>요청 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **내 요청**을 클릭하여 요청 목록을 봅니다.

    ![Azure AD 역할 - 내 요청 목록](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>역할 비활성화

역할이 활성화된 후 시간 제한(적격 기간)에 도달하면 자동으로 비활성화됩니다.

관리자 작업이 일찍 완료되면 Azure AD Privileged Identity Management에서 역할을 수동으로 비활성화할 수도 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **내 역할**을 클릭합니다.

1. **활성 역할**을 클릭하여 활성 역할 목록을 봅니다.

1. 사용하고 있는 역할을 찾은 다음, **비활성화**를 클릭합니다.

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

승인이 필요한 역할을 활성화할 필요가 없으면 언제든지 보류 중인 요청을 취소할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **내 요청**을 클릭합니다.

1. 취소하려는 역할에 대해 **취소** 단추를 클릭합니다.

    취소를 클릭하면 요청이 취소됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

   ![취소 버튼이 강조 표시된 내 요청 목록](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>문제 해결

### <a name="permissions-are-not-granted-after-activating-a-role"></a>역할을 활성화한 후 권한이 부여되지 않음

권한 있는 ID 관리에서 역할을 활성화하면 권한 있는 역할이 필요한 모든 포털에 활성화가 즉시 전파되지 않을 수 있습니다. 경우에 따라 변경 내용이 전파되더라도 포털의 웹 캐싱 때문에 변경 내용이 즉시 적용되지 않을 수 있습니다. 정품 인증이 지연되는 경우 다음과 같은 작업을 수행해야 합니다.

1. Azure Portal에서 로그아웃한 다음, 다시 로그인합니다.

    Azure AD 역할을 활성화하면 활성화 단계가 표시됩니다. 모든 단계가 완료되면 **로그아웃** 링크가 표시됩니다. 이 링크를 사용하여 로그아웃할 수 있습니다. 이렇게 하면 대부분의 경우 활성화 지연이 해결됩니다.

1. 권한 있는 ID 관리에서 역할의 구성원으로 나열되어 있는지 확인합니다.

 ---

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 역할 활성화](pim-how-to-activate-role.md)
