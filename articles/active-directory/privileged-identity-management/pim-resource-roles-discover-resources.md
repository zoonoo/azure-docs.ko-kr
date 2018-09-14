---
title: PIM에서 관리할 Azure 리소스 검색 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 관리할 Azure 리소스를 검색하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/30/2018
ms.author: rolyon
ms.openlocfilehash: d9a6ab49d619e487eee6fb13abe128cfc167b560
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306692"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>PIM에서 관리할 Azure 리소스 검색

Azure AD PIM(Privileged Identity Management)을 사용하여 Azure 리소스에 대한 보호를 향상시킬 수 있습니다. 이 내용은 이미 PIM을 사용하여 Azure AD 디렉터리 역할을 보호하는 조직, 프로덕션 리소스 보안을 유지하려는 관리 그룹 및 구독 소유자에게 유용할 수 있습니다.

먼저 Azure 리소스에 대한 PIM을 설정할 때 PIM에서 보호할 리소스를 검색하고 선택해야 합니다. PIM으로 관리할 수 있는 리소스 수에는 제한이 없습니다. 그러나 가장 중요한 리소스, 즉 프로덕션 리소스부터 시작하는 것이 좋습니다.

## <a name="discover-resources"></a>리소스 검색

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

    처음으로 Azure 리소스에 대해 PIM을 사용하는 경우 리소스 검색 창이 표시됩니다.

    ![리소스 검색 - 최초](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    조직의 다른 리소스나 디렉터리 관리자가 이미 PIM에서 Azure 리소스를 관리하고 있는 경우 현재 관리되는 리소스 목록이 표시됩니다.

    ![리소스 검색 창](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. **리소스 검색**을 클릭하여 검색 환경을 시작합니다.

    ![검색 창](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. 검색 창에서 **리소스 상태 필터** 및 **리소스 종류 선택**을 사용하여 쓰기 권한이 있는 관리 그룹 또는 구독을 필터링합니다. 처음에는 **모두**로 시작하는 것이 가장 쉬울 수 있습니다.

    PIM을 사용하여 관리할 관리 그룹 또는 구독 리소스만 검색하고 선택할 수 있습니다. PIM에서 관리 그룹 또는 구독을 관리하는 경우 해당 자식 리소스도 관리할 수 있습니다.

1. 관리하려는 관리되지 않는 리소스 옆에 확인 표시를 추가합니다.

    > [!NOTE]
    > 관리 그룹 또는 구독이 관리됨으로 설정되면 관리되지 않음이 될 수 없습니다. 따라서 다른 리소스 관리자가 PIM 설정을 제거하지 못합니다.

    ![검색 - 리소스 관리](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. **리소스 관리**를 클릭하여 선택한 리소스 관리를 시작합니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [PIM에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
