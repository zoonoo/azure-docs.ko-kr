---
title: PIM에서 Azure 리소스 역할 활성화-Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f38ef7db114705392bd1d3dc6f9a4562a809e20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86023880"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 내 Azure 리소스 역할 활성화

PIM (Privileged Identity Management)을 사용 하 여 Azure 리소스에 대 한 적격 역할 구성원이 미래의 날짜 및 시간에 대 한 활성화를 예약할 수 있습니다. 또한 최대(관리자로 구성됨) 내에서 특정 활성화 기간을 선택할 수도 있습니다.

이 문서는 Privileged Identity Management에서 Azure 리소스 역할을 활성화 해야 하는 멤버를 위한 것입니다.

## <a name="activate-a-role"></a>역할 활성화

Azure 리소스 역할을 수행 해야 하는 경우 Privileged Identity Management의 **내 역할** 탐색 옵션을 사용 하 여 활성화를 요청할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다. 대시보드에 Privileged Identity Management 타일을 추가 하는 방법에 대 한 자세한 내용은 [Privileged Identity Management 사용 시작](pim-getting-started.md)을 참조 하세요.

1. **내 역할**을 선택 합니다.

    ![활성화할 수 있는 역할을 보여 주는 내 역할 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. **Azure 리소스 역할** 을 선택 하 여 적합 한 azure 리소스 역할의 목록을 확인 합니다.

   ![내 역할-Azure 리소스 역할 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. **Azure 리소스 역할** 목록에서 활성화하려는 역할을 찾습니다.

    ![Azure 리소스 역할-적격 역할 목록](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. **활성화** 를 선택 하 여 활성화 페이지를 엽니다.

     ![범위, 시작 시간, 기간 및 이유가 포함 된 열려 있는 활성화 창](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. 다단계 인증이 필요한 역할은 **진행하기 전에 ID 확인**을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![역할 활성화 전에 MFA를 사용 하 여 id 확인](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **내 Id 확인** 을 선택 하 고 지침에 따라 추가 보안 확인을 제공 합니다.

    ![PIN 코드와 같은 보안 확인을 제공 하는 화면](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 축소 된 범위를 지정 하려는 경우 **범위** 를 선택 하 여 리소스 필터 창을 엽니다.

    필요한 리소스에 대한 액세스만 요청하는 것이 좋습니다. 리소스 필터 창에서 액세스해야 하는 리소스 그룹 또는 리소스를 지정할 수 있습니다.

    ![범위를 지정 하는 리소스 필터 창 활성화](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다. 선택한 시간 후에 구성원이 활성화됩니다.

1. **이유** 상자에 활성화 요청의 이유를 입력합니다.

    ![범위, 시작 시간, 기간 및 이유를 사용 하 여 활성화 창을 완료 했습니다.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **활성화**를 선택합니다.

    [역할을 활성화하는 데 승인이 필요](pim-resource-roles-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려주는 알림이 표시됩니다.

    ![활성화 요청이 승인 보류 중입니다.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>요청 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청** 을 선택 하 여 azure AD 역할 및 azure 리소스 역할 요청 목록을 표시 합니다.

    ![내 요청-보류 중인 요청을 표시 하는 Azure 리소스 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 오른쪽으로 스크롤하여 **요청 상태** 열을 봅니다.

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

승인이 필요한 역할을 활성화할 필요가 없으면 언제든지 보류 중인 요청을 취소할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청**을 선택 합니다.

1. 취소 하려는 역할에 대해 **취소** 링크를 선택 합니다.

    취소를 선택 하면 요청이 취소 됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

   ![취소 작업이 강조 표시 된 내 요청 목록](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>문제 해결

### <a name="permissions-are-not-granted-after-activating-a-role"></a>역할을 활성화한 후 권한이 부여되지 않음

Privileged Identity Management에서 역할을 활성화할 때 활성화는 권한 있는 역할이 필요한 모든 포털에 즉시 전파 되지 않을 수 있습니다. 경우에 따라 변경 내용이 전파되더라도 포털의 웹 캐싱 때문에 변경 내용이 즉시 적용되지 않을 수 있습니다. 정품 인증이 지연 되는 경우 다음 작업을 수행 해야 합니다.

1. Azure Portal에서 로그아웃한 다음, 다시 로그인합니다.
1. Privileged Identity Management에서 역할의 멤버로 나열 되어 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management에서 내 Azure AD 역할 활성화](pim-how-to-activate-role.md)
