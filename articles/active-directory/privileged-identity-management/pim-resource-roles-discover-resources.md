---
title: PIM-Azure Active Directory에서에서 관리 하는 Azure 리소스 검색 | Microsoft Docs
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
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9ca4862f129b2da23a1d1ad8bb0b1bd0a5078f
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476286"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>PIM에서 관리할 Azure 리소스 검색

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)을 사용 하 여 Azure 리소스의 보호를 높일 수 있습니다. 이미 Azure AD 역할을 보호 하기 위해 PIM을 사용 하는 조직 및 프로덕션 리소스 보호 하고자 하는 관리 그룹 및 구독 소유자에 게 유용 합니다.

먼저 Azure 리소스에 대한 PIM을 설정할 때 PIM에서 보호할 리소스를 검색하고 선택해야 합니다. PIM으로 관리할 수 있는 리소스 수에는 제한이 없습니다. 그러나 가장 중요한 리소스, 즉 프로덕션 리소스부터 시작하는 것이 좋습니다.

## <a name="discover-resources"></a>리소스 검색

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

    처음으로 Azure 리소스에 대해 PIM을 사용하는 경우 리소스 검색 창이 표시됩니다.

    ![첫 번째 타임 환경에 대해 나열 된 리소스가 없는 리소스 창 검색](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    조직의 다른 리소스나 디렉터리 관리자가 이미 PIM에서 Azure 리소스를 관리하고 있는 경우 현재 관리되는 리소스 목록이 표시됩니다.

    ![현재 관리 되는 리소스 창 목록은 리소스 검색](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. **리소스 검색**을 클릭하여 검색 환경을 시작합니다.

    ![구독 및 관리 그룹과 같은 관리할 수 있는 리소스를 나열 하는 검색 창](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. 검색 창에서 **리소스 상태 필터** 및 **리소스 종류 선택**을 사용하여 쓰기 권한이 있는 관리 그룹 또는 구독을 필터링합니다. 처음에는 **모두**로 시작하는 것이 가장 쉬울 수 있습니다.

    PIM을 사용하여 관리할 관리 그룹 또는 구독 리소스만 검색하고 선택할 수 있습니다. PIM에서 관리 그룹 또는 구독을 관리하는 경우 해당 자식 리소스도 관리할 수 있습니다.

1. 관리하려는 관리되지 않는 리소스 옆에 확인 표시를 추가합니다.

1. **리소스 관리**를 클릭하여 선택한 리소스 관리를 시작합니다.

    > [!NOTE]
    > 관리 그룹 또는 구독이 관리됨으로 설정되면 관리되지 않음이 될 수 없습니다. 따라서 다른 리소스 관리자가 PIM 설정을 제거하지 못합니다.

    ![선택한 리소스와 관리 리소스 옵션이 강조 표시 창 검색](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 관리하기로 선택한 리소스의 온보딩을 확인하라는 메시지가 표시되면 **예**를 클릭합니다.

    ![등록 관리를 위해 선택한 리소스를 확인 하는 메시지](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [PIM에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
