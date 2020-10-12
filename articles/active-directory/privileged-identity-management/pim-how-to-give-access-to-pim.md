---
title: PIM을 관리 하기 위한 액세스 권한 부여-Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)을 관리하기 위해 다른 관리 기관에 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f8991d5ba9efb8e3223dd44a8d037acf2de849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009565"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Privileged Identity Management에 대 한 액세스 위임

PIM (Privileged Identity Management)에 대 한 액세스를 위임 하기 위해 전역 관리자는 권한 있는 역할 관리자 역할에 다른 사용자를 할당할 수 있습니다. 기본적으로 보안 관리자 및 보안 읽기 권한자는 Privileged Identity Management에 대 한 읽기 전용 액세스 권한을 갖습니다. Privileged Identity Management에 대 한 액세스 권한을 부여 하기 위해 첫 번째 사용자는 **권한 있는 역할 관리자** 역할에 다른 사용자를 할당할 수 있습니다. 권한 있는 역할 관리자 역할은 Azure AD 역할만 관리 하는 데 필요 합니다. 권한 있는 역할 관리자 권한은 Azure 리소스에 대 한 설정을 관리 하는 데 필요 하지 않습니다.

> [!NOTE]
> Privileged Identity Management를 관리 하려면 Azure Multi-Factor Authentication 필요 합니다. Microsoft 계정은 Azure Multi-Factor Authentication에 등록할 수 없으므로 Microsoft 계정를 사용 하 여 로그인 하는 사용자는 Privileged Identity Management에 액세스할 수 없습니다.

한 명의 사용자가 잠긴 경우 또는 해당 계정이 삭제된 경우 최소한 두 명의 사용자가 항상 권한 있는 역할 관리자 역할에 있어야 합니다.

## <a name="delegate-access-to-manage-pim"></a>PIM을 관리 하기 위한 액세스 권한 위임

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure AD에서 **Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **역할**을 선택합니다.

    ![Azure AD 역할-역할 Privileged Identity Management](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. 권한 있는 **역할 관리자** 역할을 선택 하 여 멤버 페이지를 엽니다.

    ![권한 있는 역할 관리자 - 멤버](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. **구성원 추가** 를 선택 하 여 **관리 되는 멤버 추가** 창을 엽니다.

1. 멤버 **선택** 을 선택 하 여 **멤버 선택** 창을 엽니다.

    ![권한 있는 역할 관리자 - 멤버 선택](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. 멤버를 선택한 다음 **선택**을 클릭합니다.

1. **확인** 을 선택 하 여 멤버를 권한 있는 **역할 관리자** 역할에 적합 하 게 만듭니다.

    Privileged Identity Management에서 새 역할을 다른 사용자에 게 할당 하면 해당 역할을 활성화할 수 있는 **자격이** 자동으로 구성 됩니다.

1. 멤버를 영구적으로 설정 하려면 권한 있는 역할 관리자 멤버 목록에서 사용자를 선택 합니다.

1. **자세히** 를 선택 하 고 **영구적으로 설정** 하 여 할당을 영구적으로 만듭니다.

    ![권한 있는 역할 관리자 - 영구 상태로 만들기](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. [Privileged Identity Management 사용을 시작](pim-getting-started.md)하기 위한 링크를 사용자에 게 보냅니다.

## <a name="remove-access-to-manage-pim"></a>PIM을 관리하기 위한 액세스 권한 제거

권한 있는 역할 관리자 역할에서 사용자를 제거하기 전에 항상 두 명 이상의 사용자가 이 역할에 할당되어 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **역할**을 선택합니다.

1. 권한 있는 **역할 관리자** 역할을 선택 하 여 멤버 페이지를 엽니다.

1. 제거 하려는 사용자 옆의 확인란을 선택 하 고 **구성원 제거**를 선택 합니다.

    ![권한 있는 역할 관리자 - 멤버 제거](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. 역할에서 구성원을 제거할 것인지 확인 하는 메시지가 표시 되 면 **예**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management 사용 시작](pim-getting-started.md)
