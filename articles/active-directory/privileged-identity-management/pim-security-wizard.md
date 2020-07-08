---
title: PIM의 Azure AD 역할 보안 마법사-Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)을 사용하여 영구 권한 있는 Azure AD 역할 할당을 적격 상태로 변환하는 데 사용할 수 있는 보안 마법사를 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc7aed1cc79a8c08a7ff11382a1c7a51455d5c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743663"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Privileged Identity Management의 Azure AD 역할 보안 마법사

Azure Active Directory (Azure AD) 조직에서 Privileged Identity Management (PIM)을 처음 사용 하는 사용자는 시작 하기 위한 마법사가 제공 됩니다. 이 마법사를 사용 하면 권한 있는 id의 보안 위험을 이해 하 고 Privileged Identity Management를 사용 하 여 이러한 위험을 줄일 수 있습니다. 나중에 작업하려는 경우 마법사에서 기존 역할 할당을 변경할 필요가 없습니다.

> [!Important]
> 보안 마법사를 일시적으로 사용할 수 없습니다. 기다려 주셔서 감사합니다.

## <a name="wizard-overview"></a>마법사 개요

조직에서 Privileged Identity Management 사용을 시작 하기 전에 모든 역할 할당이 영구적으로 적용 됩니다. 사용자는 현재 해당 권한이 필요 하지 않은 경우에도 항상 이러한 역할에 포함 됩니다. 마법사의 첫 번째 단계에서는 권한이 높은 역할의 목록과 얼마나 많은 사용자가 현재 해당 역할에 있는지를 보여 줍니다. 특정 역할을 자세히 알아보면 익숙하지 않은 사용자에 대해 더 잘 알 수 있습니다.

마법사의 두 번째 단계는 관리자의 역할 할당을 변경할 기회를 제공합니다.  

> [!WARNING]
> 하나 이상의 전역 관리자와 회사 또는 학교 계정을 사용 하는 권한 있는 역할 관리자가 둘 이상 있어야 합니다 (Microsoft 계정). 권한 있는 역할 관리자가 하나만 있는 경우 해당 계정이 삭제 된 경우 조직에서 Privileged Identity Management를 관리할 수 없습니다.
> 또한 사용자에 게 Microsoft 계정 (즉, Skype 및 Outlook.com와 같은 Microsoft 서비스에 로그인 하는 데 사용 하는 계정)가 있는 경우 역할 할당을 영구적으로 유지 합니다. 해당 역할에 대해 multi-factor authentication을 활성화 해야 하는 경우 해당 사용자가 잠깁니다.

## <a name="run-the-wizard"></a>마법사 실행

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할** 을 선택한 다음, **마법사**를 선택 합니다.

    ![Azure AD 역할-마법사를 실행 하는 3 단계를 보여 주는 마법사 페이지](./media/pim-security-wizard/wizard-start.png)

1. **1 권한 역할 검색**을 선택 합니다.

1. 권한 있는 역할 목록을 검토하여 어떤 사용자가 영구 또는 적격인지 확인합니다.

    ![권한 있는 역할 검색-영구 및 적격 멤버를 표시 하는 역할 창](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. **다음** 을 선택 하 여 적격 할 사용자 또는 그룹을 선택 합니다.

    ![역할에 적합 한 멤버를 선택 하는 옵션을 사용 하 여 멤버를 적격 페이지로 변환 합니다.](./media/pim-security-wizard/convert-members-eligible.png)

1. 사용자 또는 그룹을 선택한 후 **다음**을 선택 합니다.

    ![변환 될 영구 역할 할당을 사용 하는 멤버를 보여 주는 변경 내용 검토 페이지](./media/pim-security-wizard/review-changes.png)

1. **확인** 을 선택 하 여 영구 할당을 적격으로 변환 합니다.

    변환이 완료되면 알림이 표시됩니다.

    ![변환 상태를 보여 주는 알림](./media/pim-security-wizard/notification-completion.png)

다른 권한 있는 역할 할당을 적격으로 변환하려면 마법사를 다시 실행하면 됩니다. 마법사 대신 Privileged Identity Management 인터페이스를 사용 하려면 [Privileged Identity Management에서 AZURE AD 역할 할당](pim-how-to-add-role-to-user.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [다른 관리자에 게 Privileged Identity Management을 관리할 수 있는 권한 부여](pim-how-to-give-access-to-pim.md)
