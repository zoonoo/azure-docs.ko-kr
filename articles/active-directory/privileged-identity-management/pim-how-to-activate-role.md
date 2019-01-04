---
title: PIM에서 Azure AD 디렉터리 역할 활성화 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 디렉터리 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 11/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 534714accb504e4ce487950fef028ab675c46a87
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496399"
---
# <a name="activate-my-azure-ad-directory-roles-in-pim"></a>PIM에서 Azure AD 디렉터리 역할 활성화

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.  

관리 역할에 대해 적격이 되었다면 권한이 필요한 작업을 수행해야 하는 경우 해당 역할을 활성화할 수 있다는 의미입니다. 예를 들어 경우에 따라 Office 365 기능을 관리하는 경우 조직의 권한 있는 역할 관리자는 해당 역할이 다른 서비스에도 또한 영향을 주므로 사용자를 영구적인 전역 관리자로 만들지 못할 수 있습니다. 대신 Exchange Online 관리자와 같은 Azure AD 역할에 대한 자격을 줍니다. 이러한 권한이 필요한 경우 해당 역할을 활성화하도록 요청하고 미리 결정된 기간 동안 제어할 수 있는 관리자 권한을 부여받습니다.

이 문서는 PIM에서 Azure AD 디렉터리 역할을 활성화해야 하는 관리자를 대상으로 합니다.

## <a name="activate-a-role"></a>역할 활성화

Azure AD 디렉터리 역할을 담당해야 하는 경우 PIM에서 **내 역할** 탐색 옵션을 사용하여 활성화를 요청할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다. 대시보드에 PIM 타일을 추가하는 방법에 대한 자세한 내용은 [PIM 사용](pim-getting-started.md)을 참조하세요.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **내 역할**을 클릭하여 Azure AD 적격 디렉터리 역할 목록을 확인합니다.

    ![Azure AD 디렉터리 역할 - 내 역할](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 활성화하려는 역할을 찾습니다.

    ![Azure AD 디렉터리 역할 - 내 역할 목록](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. **활성화**를 클릭하여 역할 활성화 세부 정보 창을 엽니다.

1. MFA(다단계 인증)가 필요한 역할인 경우 **진행하기 전에 ID 확인**을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![역할 활성화 전에 MFA를 통해 확인](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. **ID 확인**을 클릭하고 지침에 따라 추가 보안 확인을 제공합니다.

    ![추가 보안 확인](./media/pim-how-to-activate-role/additional-security-verification.png)

1. **활성화**를 클릭하여 [활성화] 창을 엽니다.

    ![활성화 창](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다.

1. 활성화 기간을 지정합니다.

1. **활성화 이유** 상자에 활성화 요청의 이유를 입력합니다. 일부 역할은 문제 티켓 번호를 제공해야 합니다.

    ![완료된 활성화 창](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. **활성화**를 클릭합니다.

    역할에 승인이 필요하지 않으면 활성화되고 활성 역할 목록에 추가됩니다. 역할을 즉시 사용하려면 다음 섹션의 단계를 따릅니다.

    [역할을 활성화하는 데 승인이 필요](./azure-ad-pim-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려주는 알림이 표시됩니다.

    ![보류 중인 요청 알림](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>활성화 즉시 역할 사용

PIM에서 역할을 활성화하는 경우 원하는 관리 포털에 액세스하거나 특정 관리 워크로드 내에서 함수를 수행하려면 적어도 10분이 소요됩니다. 권한을 강제로 업데이트하려면 다음 단계의 설명대로 **애플리케이션 액세스** 페이지를 사용합니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **응용 프로그램 액세스** 페이지를 클릭합니다.

    ![PIM 애플리케이션 액세스](./media/pim-how-to-activate-role/pim-application-access.png)

1. **Azure Active Directory** 링크를 클릭하여 **모든 사용자** 페이지에서 포털을 다시 엽니다.

    이 링크를 클릭하면 현재 토큰이 무효화되고 업데이트된 권한이 포함된 새 토큰을 Azure Portal에서 확보하도록 강제 적용됩니다.

## <a name="view-the-status-of-your-requests"></a>요청 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **내 요청**을 클릭하여 요청 목록을 봅니다.

    ![Azure AD 디렉터리 역할 - 내 요청](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>역할 비활성화

역할이 활성화된 후 시간 제한(적격 기간)에 도달하면 자동으로 비활성화됩니다.

관리자 작업이 일찍 완료되면 Azure AD Privileged Identity Management에서 역할을 수동으로 비활성화할 수도 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **내 역할**을 클릭합니다.

1. **활성 역할**을 클릭하여 활성 역할 목록을 봅니다.

1. 사용하고 있는 역할을 찾은 다음, **비활성화**를 클릭합니다.

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

승인이 필요한 역할을 활성화할 필요가 없으면 언제든지 보류 중인 요청을 취소할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **Azure AD 디렉터리 역할**을 클릭합니다.

1. **내 요청**을 클릭합니다.

1. 취소하려는 역할에 대해 **취소** 단추를 클릭합니다.

    [취소]를 클릭하면 해당 요청이 취소됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

   ![보류 중인 요청 취소](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>문제 해결

### <a name="permissions-not-granted-after-activating-a-role"></a>역할을 활성화한 후 권한이 부여되지 않음

PIM에서 역할을 활성화하는 경우 원하는 관리 포털에 액세스하거나 특정 관리 워크로드 내에서 함수를 수행하려면 적어도 10분이 소요됩니다. 권한을 강제로 업데이트하려면 앞의 [활성화 즉시 역할 사용](#use-a-role-immediately-after-activation)의 설명대로 **애플리케이션 액세스** 페이지를 사용합니다.

추가 문제 해결 단계는 [관리자 권한 문제 해결](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 활성화](pim-resource-roles-activate-your-roles.md)
