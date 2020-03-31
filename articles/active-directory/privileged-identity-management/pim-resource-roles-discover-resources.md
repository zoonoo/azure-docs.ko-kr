---
title: PIM - Azure AD에서 관리할 Azure 리소스 검색 | 마이크로 소프트 문서
description: Azure AD PIM(Privileged Identity Management)에서 관리할 Azure 리소스를 검색하는 방법을 알아봅니다.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022886"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>권한 있는 ID 관리에서 관리할 Azure 리소스 검색

Azure Active Directory(Azure AD) 권한 있는 ID 관리(PIM)를 사용하여 Azure 리소스의 보호를 개선할 수 있습니다. 이는 이미 권한 있는 ID 관리를 사용하여 Azure AD 역할을 보호하는 조직과 프로덕션 리소스를 보호하려는 관리 그룹 및 구독 소유자에게 유용합니다.

Azure 리소스에 대한 권한 ID 관리를 처음 설정할 때 권한 있는 ID 관리를 사용하여 보호할 리소스를 검색하고 선택해야 합니다. 권한 있는 ID 관리를 사용하여 관리할 수 있는 리소스 수에는 제한이 없습니다. 그러나 가장 중요한 리소스, 즉 프로덕션 리소스부터 시작하는 것이 좋습니다.

## <a name="discover-resources"></a>리소스 검색

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

    Azure 리소스에 대한 권한 ID 관리를 처음 사용하는 경우 **리소스 검색** 페이지가 표시됩니다.

    ![처음 경험에 대해 나열된 리소스가 없는 리소스 창 검색](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    조직의 다른 관리자가 권한 있는 ID 관리에서 Azure 리소스를 이미 관리하고 있는 경우 현재 관리 중인 리소스 목록이 표시됩니다.

    ![현재 관리 중인 리소스 창 검색](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. **리소스 검색을** 선택하여 검색 환경을 시작합니다.

    ![구독 및 관리 그룹과 같이 관리할 수 있는 리소스를 검색 창으로 나열합니다.](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. **검색** 페이지에서 리소스 **상태 필터** 및 리소스 **유형 선택을** 사용하여 쓰기 권한이 있는 관리 그룹 또는 구독을 필터링합니다. 처음에는 **모두**로 시작하는 것이 가장 쉬울 수 있습니다.

    권한 있는 ID 관리를 사용하여 관리하기 위해 관리 그룹 또는 구독 리소스만 검색하고 선택할 수 있습니다. 권한 있는 ID 관리에서 관리 그룹 또는 구독을 관리하는 경우 해당 하위 리소스를 관리할 수도 있습니다.

1. 관리하지 않는 리소스 옆의 확인란을 선택합니다.

1. 선택한 리소스 관리를 시작하려면 **리소스 관리를** 선택합니다.

    > [!NOTE]
    > 관리 그룹 또는 구독이 관리되면 관리되지 않습니다. 이렇게 하면 다른 리소스 관리자가 권한 있는 ID 관리 설정을 제거할 수 없습니다.

    ![리소스가 선택되고 리소스 관리 옵션이 강조 표시된 검색 창](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 관리를 위해 선택한 리소스의 온보딩을 확인하는 메시지가 표시되면 **예**를 선택합니다.

    ![관리를 위해 선택한 리소스를 온보딩하도록 확인하는 메시지](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [권한 있는 ID 관리에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
