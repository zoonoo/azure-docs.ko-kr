---
title: PIM에서 내 Azure AD 역할 활성화-Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할을 활성화 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 6007762c897337170dec69c3486302aa62723480
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756297"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>PIM에서 내 Azure AD 역할 활성화

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.  

관리 역할에 대해 적격이 되었다면 권한이 필요한 작업을 수행해야 하는 경우 해당 역할을 활성화할 수 있다는 의미입니다. 예를 들어 경우에 따라 Office 365 기능을 관리하는 경우 조직의 권한 있는 역할 관리자는 해당 역할이 다른 서비스에도 또한 영향을 주므로 사용자를 영구적인 전역 관리자로 만들지 못할 수 있습니다. 대신 Exchange Online 관리자와 같은 Azure AD 역할에 대한 자격을 줍니다. 이러한 권한이 필요한 경우 해당 역할을 활성화하도록 요청하고 미리 결정된 기간 동안 제어할 수 있는 관리자 권한을 부여받습니다.

이 문서는 Privileged Identity Management에서 Azure AD 역할을 활성화 해야 하는 관리자를 위한 것입니다.

## <a name="activate-a-role"></a>역할 활성화

Azure AD 역할을 수행 해야 하는 경우 Privileged Identity Management의 **내 역할** 탐색 옵션을 사용 하 여 활성화를 요청할 수 있습니다.

1. [Azure portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다. 대시보드에 Privileged Identity Management 타일을 추가 하는 방법에 대 한 자세한 내용은 [Privileged Identity Management 사용 시작](pim-getting-started.md)을 참조 하세요.

1. **Azure AD 역할**을 클릭합니다.

1. **내 역할** 을 클릭 하 여 적합 한 Azure AD 역할의 목록을 확인 합니다.

    ![Azure AD 역할-적격 또는 활성 역할 목록을 표시 하는 내 역할](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 활성화하려는 역할을 찾습니다.

    ![Azure AD 역할-활성화 링크를 보여 주는 내 적격 역할 목록](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. **활성화**를 클릭하여 역할 활성화 세부 정보 창을 엽니다.

1. MFA(다단계 인증)가 필요한 역할인 경우 **진행하기 전에 ID 확인**을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![역할 활성화 전에 MFA를 사용 하 여 id 창 확인](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. **ID 확인**을 클릭하고 지침에 따라 추가 보안 확인을 제공합니다.

    ![사용자에 게 연락 하는 방법을 요청 하는 추가 보안 확인 페이지](./media/pim-how-to-activate-role/additional-security-verification.png)

1. **활성화**를 클릭하여 [활성화] 창을 엽니다.

    ![시작 시간, 기간, 티켓 및 이유를 지정 하는 활성화 창](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다.

1. 활성화 기간을 지정합니다.

1. **활성화 이유** 상자에 활성화 요청의 이유를 입력합니다. 일부 역할은 문제 티켓 번호를 제공해야 합니다.

    ![사용자 지정 시작 시간, 기간, 티켓 및 이유를 사용 하 여 완료 된 활성화 창](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. **활성화**를 클릭합니다.

    역할에 승인이 필요 하지 않은 경우 활성화 상태를 표시 하는 **활성화 상태** 창이 표시 됩니다.

    ![활성화의 세 단계를 보여 주는 활성화 상태 페이지](./media/pim-how-to-activate-role/activation-status.png)

    모든 단계가 완료 되 면 **로그 아웃** 링크를 클릭 하 여 Azure Portal에서 로그 아웃 합니다. 포털에 다시 로그인 하면 이제 역할을 사용할 수 있습니다.

    [역할을 활성화하는 데 승인이 필요](./azure-ad-pim-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려주는 알림이 표시됩니다.

    ![활성화 요청이 승인 보류 중입니다.](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>요청 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **내 요청**을 클릭하여 요청 목록을 봅니다.

    ![Azure AD 역할-내 요청 목록](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

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

    취소를 클릭 하면 요청이 취소 됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

   ![취소 단추가 강조 표시 된 내 요청 목록](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>문제 해결

### <a name="permissions-are-not-granted-after-activating-a-role"></a>역할을 활성화한 후 권한이 부여되지 않습니다.

Privileged Identity Management에서 역할을 활성화할 때 활성화는 권한 있는 역할이 필요한 모든 포털에 즉시 전파 되지 않을 수 있습니다. 경우에 따라 변경 내용이 전파되더라도 포털의 웹 캐싱 때문에 변경 내용이 즉시 적용되지 않을 수 있습니다. 정품 인증이 지연 되는 경우 다음 작업을 수행 해야 합니다.

1. Azure Portal에서 로그아웃한 다음, 다시 로그인합니다.

    Azure AD 역할을 활성화 하면 활성화 단계가 표시 됩니다. 모든 단계가 완료되면 **로그아웃** 링크가 표시됩니다. 이 링크를 사용 하 여 로그 아웃할 수 있습니다. 이렇게 하면 대부분의 경우 활성화 지연 시간이 해결 됩니다.

1. Privileged Identity Management에서 역할의 멤버로 나열 되어 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 내 Azure 리소스 역할 활성화](pim-resource-roles-activate-your-roles.md)
