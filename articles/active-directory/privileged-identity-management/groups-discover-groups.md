---
title: 권한 있는 액세스 그룹으로 역할 할당 가능 그룹 관리-Azure AD | Microsoft Docs
description: Privileged Identity Management (PIM)에서 권한 있는 액세스 그룹으로 관리할 역할 할당 가능 그룹을 등록 하는 데 동의 합니다.
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
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869537"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Privileged Identity Management에서 권한 있는 액세스 그룹 관리 (미리 보기)

Azure AD (Azure Active Directory)에서 클라우드 그룹에 Azure AD 기본 제공 역할을 할당 하 여 역할 할당을 관리 하는 방법을 단순화할 수 있습니다. Azure AD 역할을 보호 하 고 액세스를 보호 하기 위해 이제 PIM (Privileged Identity Management)을 사용 하 여 이러한 그룹의 멤버 또는 소유자에 대 한 just-in-time 액세스를 관리할 수 있습니다. Privileged Identity Management에서 Azure AD 역할 할당 가능 그룹을 권한 있는 액세스 그룹으로 관리 하려면 PIM의 관리 대상으로 가져와야 합니다.

## <a name="identify-groups-to-manage"></a>관리할 그룹 식별

[Azure Active Directory에서 역할 할당 가능 그룹 만들기](../users-groups-roles/roles-groups-create-eligible.md)에 설명 된 대로 Azure AD에서 역할 할당 가능한 그룹을 만들 수 있습니다. Privileged Identity Management에서 관리 하는 그룹의 소유자 여야 합니다.

1. 권한 있는 역할 관리자 권한으로 [AZURE AD에 로그인](https://aad.portal.azure.com) 합니다.
1. **그룹** 을 선택 하 고 관리 하려는 역할 할당 가능 그룹을 선택 합니다. 목록을 검색 하거나 필터링 할 수 있습니다.

    ![PIM에서 관리할 역할 할당 가능 그룹 찾기](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. 그룹을 열고 권한 있는 **액세스 (미리 보기)** 를 선택 합니다.

    ![Privileged Identity Management 환경 열기](./media/groups-discover-groups/groups-discover-groups.png)

1. 아직 그룹을 PIM에서 관리 하 고 있지 않은 경우 관리에 동의 하려면 **권한 있는 액세스 사용** 을 선택 합니다. 전역 관리자 또는 그룹 소유자만이 동의를 제공할 수 있습니다.

    ![필요한 경우 Privileged Identity Management에서 그룹을 관리 하는 데 동의 합니다.](./media/groups-discover-groups/consent-page.png)

1. PIM에서 할당 관리를 시작 합니다.

    ![Privileged Identity Management에서 할당 관리](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 권한 있는 액세스 그룹을 관리 한 후에는 관리를 수행할 수 없습니다. 이렇게 하면 다른 관리자가 Privileged Identity Management 설정을 제거할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 권한 있는 액세스 그룹 할당 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 권한 있는 액세스 그룹 할당](pim-resource-roles-assign-roles.md)
