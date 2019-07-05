---
title: PIM-Azure Active Directory에서에서 내 Azure AD 역할 활성화 | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할을 활성화 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b3e02222580da67a4eeb4159e4fc5dc3b9a98f
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501682"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>PIM에서 내 Azure AD 역할 활성화

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.  

관리 역할에 대해 적격이 되었다면 권한이 필요한 작업을 수행해야 하는 경우 해당 역할을 활성화할 수 있다는 의미입니다. 예를 들어 경우에 따라 Office 365 기능을 관리하는 경우 조직의 권한 있는 역할 관리자는 해당 역할이 다른 서비스에도 또한 영향을 주므로 사용자를 영구적인 전역 관리자로 만들지 못할 수 있습니다. 대신 Exchange Online 관리자와 같은 Azure AD 역할에 대한 자격을 줍니다. 이러한 권한이 필요한 경우 해당 역할을 활성화하도록 요청하고 미리 결정된 기간 동안 제어할 수 있는 관리자 권한을 부여받습니다.

이 문서는 해당 Azure AD PIM의 역할을 활성화 해야 하는 관리자입니다.

## <a name="activate-a-role"></a>역할 활성화

Azure AD 역할에서 수행 해야 할 때 사용 하 여 활성화를 요청할 수 있습니다 합니다 **내 역할** PIM의 탐색 옵션입니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다. 대시보드에 PIM 타일을 추가하는 방법에 대한 자세한 내용은 [PIM 사용](pim-getting-started.md)을 참조하세요.

1. **Azure AD 역할**을 클릭합니다.

1. 클릭 **내 역할** 에 적합 한 목록을 보려면 Azure AD 역할입니다.

    ![Azure AD 역할-적격 또는 활성 역할 목록 표시 내 역할](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 활성화하려는 역할을 찾습니다.

    ![Azure AD 역할-내 적격 역할 목록이 표시 활성화 링크](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. **활성화**를 클릭하여 역할 활성화 세부 정보 창을 엽니다.

1. MFA(다단계 인증)가 필요한 역할인 경우 **진행하기 전에 ID 확인**을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![내 역할 활성화 전에 MFA 사용 하 여 id 창 확인](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. **ID 확인**을 클릭하고 지침에 따라 추가 보안 확인을 제공합니다.

    ![사용자에 게 연락 하는 방법을 요청 하는 추가 보안 인증 페이지](./media/pim-how-to-activate-role/additional-security-verification.png)

1. **활성화**를 클릭하여 [활성화] 창을 엽니다.

    ![활성화 창 시작 시간, 기간, 티켓 및 이유 지정](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다.

1. 활성화 기간을 지정합니다.

1. **활성화 이유** 상자에 활성화 요청의 이유를 입력합니다. 일부 역할은 문제 티켓 번호를 제공해야 합니다.

    ![사용자 지정을 사용 하 여 완료 된 정품 인증 창 시작 시간, 기간, 티켓 및 이유](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. **활성화**를 클릭합니다.

    역할에 승인이 필요 하지 않은 경우는 **활성화 상태** 활성화 상태를 보여 주는 창이 나타납니다.

    ![정품 인증 세 가지 단계를 보여 주는 활성화 상태 페이지](./media/pim-how-to-activate-role/activation-status.png)

    모든 단계 완료 되 면 클릭 합니다 **로그 아웃** Azure portal에서 로그 아웃 링크 합니다. 포털에 다시 로그인 할 경우 역할을 이제 사용할 수 있습니다.

    [역할을 활성화하는 데 승인이 필요](./azure-ad-pim-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려주는 알림이 표시됩니다.

    ![활성화 요청을 보류 중 승인 알림](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

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

    취소 클릭 하면 요청이 취소 됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

   ![취소 단추를 강조 표시를 사용 하 여 내 요청 목록](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>문제 해결

### <a name="permissions-are-not-granted-after-activating-a-role"></a>역할을 활성화 한 후에 권한이 부여 되지 않습니다.

PIM의 역할을 활성화 하면 권한 있는 역할을 필요로 하는 모든 포털 활성화 즉시 전파 되지 않을 수 있습니다. 경우에 따라 변경 내용이 전파 되는 경우에 변경 내용이 즉시 적용 되지 될 수 있습니다 웹 포털에서 캐싱. 활성화가 지연 되 면 무엇을 해야 다음과 같습니다.

1. Azure portal에서 로그 아웃 하 고 다시 로그인 합니다.

    Azure AD 역할을 활성화 하면 활성화 하는 단계 표시 됩니다. 모든 단계 완료 되 면 표시 됩니다는 **로그 아웃** 링크 합니다. 로그 아웃 하려면이 링크를 사용할 수 있습니다. 이 활성화 지연 시간에 대 한 대부분의 경우 해결 하십시오.

1. PIM에서 역할의 구성원으로 나열 되어 있는지를 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 활성화](pim-resource-roles-activate-your-roles.md)
