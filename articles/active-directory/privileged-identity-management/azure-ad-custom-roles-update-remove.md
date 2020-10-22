---
title: Azure AD 사용자 지정 역할 업데이트 또는 제거-PIM (Privileged Identity Management)
description: PIM(Privileged Identity Management)에서 Azure AD 사용자 지정 역할 할당을 업데이트 또는 제거하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a35442dd8af1cd4acf22de453c8d10460e1e39f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371531"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Privileged Identity Management에서 할당된 Azure AD 사용자 지정 역할 업데이트 또는 제거

이 문서에서는 PIM(Privileged Identity Management)을 사용하여 Azure AD(Azure Active Directory) 관리 환경에서 애플리케이션 관리를 위해 만든 사용자 지정 역할에 대한 Just-In-Time 및 시간 제한 할당을 업데이트하거나 제거하는 방법에 대해 설명합니다. 

- Azure AD에서 애플리케이션 관리를 위임하기 위한 사용자 지정 역할을 만드는 방법에 대한 자세한 내용은 [Azure Active Directory의 사용자 지정 관리자 역할(미리 보기)](../roles/custom-overview.md)을 참조하세요. 
- Privileged Identity Management를 아직 사용하지 않은 경우 [Privileged Identity Management 사용 시작](pim-getting-started.md)에서 자세한 정보를 확인하세요.

> [!NOTE]
> 미리 보기 기간 중에는 Azure AD 사용자 지정 역할이 기본 제공 디렉터리 역할과 통합되지 않습니다. 이 기능이 일반 공급되면 기본 제공 역할 환경에서 역할 관리가 수행됩니다. 다음 배너가 표시 되는 경우 이러한 역할은 [기본 제공 역할 환경에서](pim-how-to-add-role-to-user.md) 관리 해야 하며이 문서는 적용 되지 않습니다.
>
> [![Azure AD > Privileged Identity Management를 선택 합니다.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>할당 업데이트 또는 제거

기존 사용자 지정 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. 권한 있는 역할 관리자 역할에 할당된 사용자 계정을 사용하여 Azure Portal에서 [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)에 로그인합니다.
1. **Azure AD 사용자 지정 역할(미리 보기)** 을 선택합니다.

    ![적격 역할 할당을 표시하기 위한 Azure AD 사용자 지정 역할 미리 보기 선택](./media/azure-ad-custom-roles-assign/view-custom.png)

1. **역할**을 선택하여 Azure AD 애플리케이션에 대한 사용자 지정 역할의 **할당** 목록을 표시합니다.

    ![적격 역할 할당 목록을 표시하기 위한 역할 선택](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. 업데이트 또는 제거하려는 역할을 선택합니다.
1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.
1. **업데이트** 또는 **제거**를 선택하여 역할 할당을 업데이트하거나 제거합니다.

    ![적격 역할 할당에서 제거 또는 업데이트 선택](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD 사용자 지정 역할 활성화](azure-ad-custom-roles-assign.md)
- [Azure AD 사용자 지정 역할 할당](azure-ad-custom-roles-assign.md)
- [Azure AD 사용자 지정 역할 할당 구성](azure-ad-custom-roles-configure.md)
