---
title: 역할이 PIM-Azure Active Directory에서에서 관리할 수 없습니다 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 관리할 수 없는 역할을 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5fb632ee5fd9c18bde7443e81fe2ef6e5335e4
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579549"
---
# <a name="roles-you-cannot-manage-in-pim"></a>PIM에서 관리할 수 없는 역할

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)을 사용 하면 모든를 관리할 수 있습니다 [Azure AD 역할](../users-groups-roles/directory-assign-admin-roles.md) all [Azure 리소스 역할](../../role-based-access-control/built-in-roles.md)입니다. 이러한 역할에는 관리 그룹, 구독, 리소스 그룹 및 리소스에 연결된 사용자 지정 역할이 포함됩니다. 그러나 관리할 수 없는 몇 가지 역할이 있습니다. 이 문서에서는 PIM에서 관리할 수 없는 역할을 설명합니다.

## <a name="classic-subscription-administrator-roles"></a>클래식 구독 관리자 역할

PIM에서 다음 클래식 구독 관리자 역할을 관리할 수 없습니다.

- 계정 관리자
- 서비스 관리자
- 공동 관리자

클래식 구독 관리자 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure RBAC 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

## <a name="what-about-office-365-admin-roles"></a>Office 365 관리자 역할의 경우는 어떻습니까?

Exchange 관리자 및 SharePoint 관리자를 제외한 Exchange Online 또는 SharePoint Online 내의 역할은 Azure AD에서 표현되지 않으므로 PIM에서 관리할 수 없습니다. 이러한 Office 365 서비스에 대한 자세한 내용은 [Office 365 관리자 역할](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)을 참조하세요.

> [!NOTE]
> SharePoint 관리자는 SharePoint Online 관리 센터를 통해 SharePoint Online에 대한 관리 액세스 권한을 보유하고, SharePoint Online에서 거의 모든 태스크를 수행할 수 있습니다. 적격 사용자는 PIM에서 정품 인증을 한 후 SharePoint 내에서 이 역할을 사용할 때 지연이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [PIM에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
