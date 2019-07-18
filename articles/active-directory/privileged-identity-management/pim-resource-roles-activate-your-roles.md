---
title: PIM-Azure Active Directory에서에서 내 Azure 리소스 역할 활성화 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501653"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할 활성화

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)을 사용 하 여 Azure 리소스에 대 한 적격 역할 구성원이 미래의 날짜 및 시간에 대 한 활성화를 예약할 수 있습니다. 또한 최대(관리자로 구성됨) 내에서 특정 활성화 기간을 선택할 수도 있습니다.

이 문서는 PIM에서 해당 Azure 리소스 역할을 활성화해야 하는 구성원을 대상으로 합니다.

## <a name="activate-a-role"></a>역할 활성화

Azure 리소스 역할을 담당해야 하는 경우 PIM에서 **내 역할** 탐색 옵션을 사용하여 활성화를 요청할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다. 대시보드에 PIM 타일을 추가하는 방법에 대한 자세한 내용은 [PIM 사용](pim-getting-started.md)을 참조하세요.

1. **내 역할**을 클릭합니다.

    ![역할 활성화 수를 보여 주는 역할 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 클릭 **Azure 리소스 역할** 적격 Azure 리소스 역할의 목록을 볼 수 있습니다.

   ![내 역할-Azure 리소스 역할 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. **Azure 리소스 역할** 목록에서 활성화하려는 역할을 찾습니다.

    ![Azure 리소스 역할-적격 역할 목록](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. **활성화**를 클릭하여 활성화 창을 엽니다.

1. MFA(다단계 인증)가 필요한 역할인 경우 **진행하기 전에 ID 확인**을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![역할 활성화 전에 MFA 사용 하 여 id 확인](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **ID 확인**을 클릭하고 지침에 따라 추가 보안 확인을 제공합니다.

    ![예: PIN 코드 보안 확인을 제공 하려면 화면](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 축소된 범위를 지정하려면 **범위**를 클릭하여 리소스 필터 창을 엽니다.

    필요한 리소스에 대한 액세스만 요청하는 것이 좋습니다. 리소스 필터 창에서 액세스해야 하는 리소스 그룹 또는 리소스를 지정할 수 있습니다.

    ![활성화-리소스 범위 지정 필터 창](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다. 선택한 시간 후에 구성원이 활성화됩니다.

1. **이유** 상자에 활성화 요청의 이유를 입력합니다.

    ![범위, 시작 시간, 기간 및 이유를 사용 하 여 완료 된 활성화 창](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **활성화**를 클릭합니다.

    역할에 승인이 필요하지 않으면 활성화되고 활성 역할 목록에 추가됩니다. 역할을 사용 하려는 경우 다음 섹션의 단계를 수행 합니다.

    [역할을 활성화하는 데 승인이 필요](pim-resource-roles-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려주는 알림이 표시됩니다.

    ![활성화 요청을 보류 중 승인 알림](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>활성화 즉시 역할 사용

활성화 된 후 모든 지연 시 즉시 사용 하 여 Azure 리소스 역할을 활성화 한 후 다음이 단계를 수행 합니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. 클릭 **내 역할** 에 적합 한 목록을 보려면 Azure AD 역할 및 Azure 리소스 역할입니다.

1. 클릭 **Azure 리소스 역할**입니다.

1. 클릭 합니다 **활성 역할** 탭 합니다.

1. 역할 활성화 되 면 포털에서 로그 아웃 했다가 다시 로그인 합니다.

    역할 이제 데 사용할 수 있어야 합니다.

## <a name="view-the-status-of-your-requests"></a>요청 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. 클릭 **내 요청** 에 Azure AD 및 Azure 리소스 역할의 목록을 보려면 다음을 요청 합니다.

    ![내 요청 수-보류 중인 요청을 표시 하는 Azure 리소스 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 오른쪽으로 스크롤하여 **요청 상태** 열을 봅니다.

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

승인이 필요한 역할을 활성화할 필요가 없으면 언제든지 보류 중인 요청을 취소할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청**을 클릭합니다.

1. 취소하려는 역할에 대해 **취소** 링크를 클릭합니다.

    취소 클릭 하면 요청이 취소 됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

   ![취소 작업을 강조 표시를 사용 하 여 내 요청 목록](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>문제 해결

### <a name="permissions-are-not-granted-after-activating-a-role"></a>역할을 활성화 한 후에 권한이 부여 되지 않습니다.

PIM의 역할을 활성화 하면 권한 있는 역할을 필요로 하는 모든 포털 활성화 즉시 전파 되지 않을 수 있습니다. 경우에 따라 변경 내용이 전파 되는 경우에 변경 내용이 즉시 적용 되지 될 수 있습니다 웹 포털에서 캐싱. 활성화가 지연 되 면 무엇을 해야 다음과 같습니다.

1. Azure portal에서 로그 아웃 하 고 다시 로그인 합니다.

    Azure 리소스 역할을 활성화 하면 활성화 하는 단계 표시 됩니다. 모든 단계 완료 되 면 표시 됩니다는 **로그 아웃** 링크 합니다. 로그 아웃 하려면이 링크를 사용할 수 있습니다. 이 활성화 지연 시간에 대 한 대부분의 경우 해결 하십시오.

1. PIM에서 역할의 구성원으로 나열 되어 있는지를 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [PIM에서 내 Azure AD 역할 활성화](pim-how-to-activate-role.md)
