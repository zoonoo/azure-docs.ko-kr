---
title: PIM에서 관리할 Azure 리소스 검색-Azure AD | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74022886"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Privileged Identity Management에서 관리할 Azure 리소스 검색

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)를 사용 하 여 Azure 리소스의 보호를 향상할 수 있습니다. 이는 이미 Privileged Identity Management를 사용 하 여 Azure AD 역할을 보호 하 고 프로덕션 리소스를 보호 하려는 관리 그룹 및 구독 소유자에 게 유용 합니다.

Azure 리소스에 대 한 Privileged Identity Management를 처음 설정 하는 경우 Privileged Identity Management를 사용 하 여 보호할 리소스를 검색 하 고 선택 해야 합니다. Privileged Identity Management로 관리할 수 있는 리소스의 수에는 제한이 없습니다. 그러나 가장 중요한 리소스, 즉 프로덕션 리소스부터 시작하는 것이 좋습니다.

## <a name="discover-resources"></a>리소스 검색

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 선택합니다.

    Azure 리소스에 대 한 Privileged Identity Management를 처음 사용 하는 경우 **리소스 검색** 페이지가 표시 됩니다.

    ![첫 번째 시간 환경에 대해 나열 된 리소스가 없는 리소스 창 검색](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    조직의 다른 관리자가 이미 Privileged Identity Management에서 Azure 리소스를 관리 하 고 있는 경우 현재 관리 중인 리소스 목록이 표시 됩니다.

    ![현재 관리 중인 리소스를 나열 하는 리소스 창 검색](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. 검색 환경을 시작 하려면 **리소스 검색** 을 선택 합니다.

    ![구독 및 관리 그룹과 같이 관리할 수 있는 리소스를 나열 하는 검색 창](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. **검색** 페이지에서 **리소스 상태 필터** 를 사용 하 고 **리소스 종류를 선택** 하 여 쓰기 권한이 있는 관리 그룹 또는 구독을 필터링 합니다. 처음에는 **모두**로 시작하는 것이 가장 쉬울 수 있습니다.

    Privileged Identity Management를 사용 하 여 관리 그룹 또는 구독 리소스만 검색 하 고 선택할 수 있습니다. Privileged Identity Management에서 관리 그룹 또는 구독을 관리 하는 경우 해당 자식 리소스를 관리할 수도 있습니다.

1. 관리 하려는 관리 되지 않는 리소스 옆에 있는 확인란을 선택 합니다.

1. **리소스 관리** 를 선택 하 여 선택한 리소스 관리를 시작 합니다.

    > [!NOTE]
    > 관리 그룹 또는 구독을 관리 한 후에는 관리 그룹 또는 구독을 관리할 수 없습니다. 이렇게 하면 다른 리소스 관리자가 Privileged Identity Management 설정을 제거할 수 없습니다.

    ![리소스를 선택 하 고 리소스 관리 옵션이 강조 표시 된 검색 창](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 관리를 위해 선택한 리소스의 온 보 딩을 확인 하는 메시지가 표시 되 면 **예**를 선택 합니다.

    ![관리를 위해 선택한 리소스를 등록 하는 메시지를 확인 하는 메시지](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
