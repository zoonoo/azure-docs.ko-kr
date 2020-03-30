---
title: PIM - Azure AD에서 Azure 리소스 역할 활성화 | 마이크로 소프트 문서
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023143"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 내 Azure 리소스 역할 활성화

PIM(권한 있는 ID 관리)을 사용하여 Azure 리소스에 대한 적격 역할 구성원이 향후 날짜 및 시간에 대한 활성화를 예약할 수 있도록 허용합니다. 또한 최대(관리자로 구성됨) 내에서 특정 활성화 기간을 선택할 수도 있습니다.

이 문서는 권한 있는 ID 관리에서 Azure 리소스 역할을 활성화해야 하는 구성원을 위한 것입니다.

## <a name="activate-a-role"></a>역할 활성화

Azure 리소스 역할을 수행해야 하는 경우 권한 있는 ID 관리에서 **내 역할** 탐색 옵션을 사용하여 활성화를 요청할 수 있습니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다. 대시보드에 권한 ID 관리 타일을 추가하는 방법에 대한 자세한 내용은 [권한 있는 ID 관리 사용 시작을](pim-getting-started.md)참조하십시오.

1. **내 역할을**선택합니다.

    ![활성화할 수 있는 역할을 보여주는 역할 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. **Azure 리소스 역할을** 선택하여 적격 Azure 리소스 역할 목록을 확인합니다.

   ![내 역할 - Azure 리소스 역할 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. **Azure 리소스 역할** 목록에서 활성화하려는 역할을 찾습니다.

    ![Azure 리소스 역할 - 적격 역할 목록](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 활성화창을 열려면 **활성화를** 선택합니다.

1. 다단계 인증이 필요한 역할은 **진행하기 전에 ID 확인**을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![역할 활성화 전에 MFA로 ID 확인](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **신원 확인을** 선택하고 지침에 따라 추가 보안 확인을 제공합니다.

    ![PIN 코드와 같은 보안 확인을 제공하는 화면](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 축소된 범위를 지정하려면 **범위를** 선택하여 리소스 필터 창을 엽니다.

    필요한 리소스에 대한 액세스만 요청하는 것이 좋습니다. 리소스 필터 창에서 액세스해야 하는 리소스 그룹 또는 리소스를 지정할 수 있습니다.

    ![활성화 - 범위를 지정하는 리소스 필터 창](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다. 선택한 시간 후에 구성원이 활성화됩니다.

1. **이유** 상자에 활성화 요청의 이유를 입력합니다.

    ![범위, 시작 시간, 기간 및 이유가 있는 활성화 창 완료](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **활성화**를 선택합니다.

    역할에 승인이 필요하지 않으면 활성화되고 활성 역할 목록에 추가됩니다. 역할을 사용하려면 다음 섹션의 단계를 따릅니다.

    [역할을 활성화하는 데 승인이 필요](pim-resource-roles-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려주는 알림이 표시됩니다.

    ![활성화 요청이 승인 알림 보류 중입니다.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>활성화 즉시 역할 사용

정품 인증 후 지연이 발생하는 경우 활성화한 후 다음 단계를 수행하여 Azure 리소스 역할을 즉시 사용합니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. 내 **역할을** 선택하여 적격 Azure AD 역할 및 Azure 리소스 역할 목록을 볼 수 있습니다.

1. **Azure 리소스 역할을**선택합니다.

1. 활성 **역할** 탭을 선택합니다.

1. 역할이 활성화되면 포털에서 로그아웃하고 다시 로그인합니다.

    이제 역할을 사용할 수 있습니다.

## <a name="view-the-status-of-your-requests"></a>요청 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청을** 선택하여 Azure AD 역할 및 Azure 리소스 역할 요청 목록을 볼 수 있습니다.

    ![내 요청 - 보류 중인 요청을 표시하는 Azure 리소스 페이지](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

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

    Azure 리소스 역할을 활성화하면 활성화 단계가 표시됩니다. 모든 단계가 완료되면 **로그아웃** 링크가 표시됩니다. 이 링크를 사용하여 로그아웃할 수 있습니다. 이렇게 하면 대부분의 경우 활성화 지연이 해결됩니다.

1. 권한 있는 ID 관리에서 역할의 구성원으로 나열되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [권한 있는 ID 관리에서 Azure AD 역할 활성화](pim-how-to-activate-role.md)
