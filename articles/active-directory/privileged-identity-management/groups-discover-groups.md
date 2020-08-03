---
title: PIM에서 관리할 역할 할당 가능한 그룹 검색-Azure AD | Microsoft Docs
description: Privileged Identity Management (PIM)에서 권한 있는 액세스 그룹으로 관리할 역할 할당 가능 그룹을 검색 하는 방법에 대해 알아봅니다.
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
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506005"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>Privileged Identity Management에서 관리할 권한 있는 액세스 그룹 검색 (미리 보기)

Azure AD (Azure Active Directory)에서 클라우드 그룹에 Azure AD 기본 제공 역할을 할당 하 여 역할 할당의 관리를 간소화할 수 있습니다. 이제 PIM (Privileged Identity Management)을 사용 하 여 이러한 그룹의 멤버 자격 또는 소유권에 대 한 자격을 할당 하 고, Azure AD 역할을 보호 하 고, 액세스를 보호할 수 있습니다. Privileged Identity Management에서 Azure AD 역할 할당 가능 그룹을 권한 있는 액세스 그룹으로 관리 하려면 먼저 해당 그룹을 검색 하 여 PIM에서 관리 하는 상태로 만들어야 합니다.

## <a name="discover-resources"></a>리소스 검색

1. 권한 있는 역할 관리자 역할 권한을 사용 하 여 [AZURE AD에 로그인](https://aad.portal.azure.com) 합니다.
1. Azure AD에서 역할 할당 가능 그룹을 만듭니다. Privileged Identity Management를 사용 하 여 검색 하 고 관리 하려면 그룹의 소유자 여야 합니다.
1. **Privileged Identity Management**를 엽니다.
1. **권한 있는 액세스 (미리 보기)** 를 선택 합니다.

    ![첫 번째 시간 환경에 대 한 그룹 검색 명령](./media/groups-discover-groups/groups-discover-groups.png)

1. **검색 그룹**을 선택 합니다.
1. 그룹 이름으로 검색 합니다.
1. 그룹을 선택 하 고 그룹 **관리** 를 선택 하 여 PIM 관리에서 가져옵니다.

    ![첫 번째 시간 환경에 대해 나열 된 리소스가 없는 그룹 검색](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > 권한 있는 액세스 그룹을 관리 한 후에는 관리를 수행할 수 없습니다. 이렇게 하면 다른 리소스 관리자가 Privileged Identity Management 설정을 제거할 수 없습니다.

1. 관리를 위해 선택한 리소스의 온 보 딩을 확인 하는 메시지가 표시 되 면 **예**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 권한 있는 액세스 그룹 할당 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 권한 있는 액세스 그룹 할당](pim-resource-roles-assign-roles.md)
