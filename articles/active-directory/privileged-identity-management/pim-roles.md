---
title: 권한 있는 ID 관리에서 관리할 수 없는 역할 - Azure Active Directory | 마이크로 소프트 문서
description: Azure AD PIM(Privileged Identity Management)에서 관리할 수 없는 역할을 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607532"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>권한 있는 ID 관리에서 관리할 수 없는 역할

Azure Active Directory(Azure AD) 권한 있는 ID 관리(PIM)를 사용하면 모든 [Azure AD 역할](../users-groups-roles/directory-assign-admin-roles.md) 및 모든 Azure 역할을 관리할 수 [있습니다.](../../role-based-access-control/built-in-roles.md) Azure 역할에는 관리 그룹, 구독, 리소스 그룹 및 리소스에 연결된 사용자 지정 역할도 포함될 수 있습니다. 그러나 관리할 수 없는 몇 가지 역할이 있습니다. 이 문서에서는 권한 있는 ID 관리에서 관리할 수 없는 역할에 대해 설명합니다.

## <a name="classic-subscription-administrator-roles"></a>클래식 구독 관리자 역할

권한 있는 ID 관리에서 다음과 같은 클래식 구독 관리자 역할을 관리할 수 없습니다.

- 계정 관리자
- 서비스 관리자
- 공동 관리자

클래식 구독 관리자 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure RBAC 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

## <a name="what-about-office-365-admin-roles"></a>Office 365 관리자 역할의 경우는 어떻습니까?

Azure AD 역할 및 관리자 포털 환경에서 Exchange 관리자 및 SharePoint 관리자와 같은 모든 Office365 역할을 지원하지만 Exchange RBAC 또는 SharePoint RBAC 내에서는 특정 역할을 지원하지 않습니다. 이러한 Office 365 서비스에 대한 자세한 내용은 [Office 365 관리자 역할](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)을 참조하세요.

> [!NOTE]
> SharePoint 관리자 역할과 Microsoft 보안 및 규정 준수 센터에 액세스하려는 모든 역할에 대한 적격 사용자는 해당 역할을 활성화한 후 최대 몇 시간까지 지연될 수 있습니다. 우리는 문제를 해결하기 위해 그 팀과 협력하고 있습니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [권한 있는 ID 관리에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
