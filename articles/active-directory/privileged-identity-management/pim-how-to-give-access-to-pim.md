---
title: PIM - Azure Active Directory를 관리하기 위한 액세스 권한 부여 | 마이크로 소프트 문서
description: Azure AD PIM(Privileged Identity Management)을 관리하기 위해 다른 관리 기관에 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022124"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>권한 있는 ID 관리를 관리 하기 위해 다른 관리자에 대 한 액세스 권한을 부여 합니다.

조직에 대한 권한 ID 관리(PIM)를 활성화하는 전역 관리자는 역할 할당 및 권한 있는 ID 관리에 대한 액세스를 자동으로 받습니다. 하지만 Azure Active Directory(Azure AD) 조직의 다른 누구도 기본적으로 쓰기 액세스 권한을 얻지 못합니다. 다른 글로벌 관리자, 보안 관리자 및 보안 독자는 권한 있는 ID 관리에 대한 읽기 전용 액세스 권한을 갖습니다. 권한 있는 ID 관리에 대한 액세스 권한을 부여하기 위해 첫 번째 사용자는 **권한 있는 역할 관리자** 역할에 다른 사용자를 할당할 수 있습니다.

> [!NOTE]
> 권한 있는 ID 관리를 관리하려면 Azure 다단계 인증이 필요합니다. Microsoft 계정은 Azure 다단계 인증에 등록할 수 없으므로 Microsoft 계정으로 로그인한 사용자는 권한 있는 ID 관리에 액세스할 수 없습니다.

한 명의 사용자가 잠긴 경우 또는 해당 계정이 삭제된 경우 최소한 두 명의 사용자가 항상 권한 있는 역할 관리자 역할에 있어야 합니다.

## <a name="grant-access-to-manage-pim"></a>PIM을 관리하기 위한 액세스 권한 부여

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure AD에서 **권한 있는 ID 관리를**엽니다.

1. **Azure AD 역할을**선택합니다.

1. **역할**을 선택합니다.

    ![권한 있는 ID 관리 Azure AD 역할 - 역할](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. 권한 **있는 역할 관리자** 역할을 선택하여 구성원 페이지를 엽니다.

    ![권한 있는 역할 관리자 - 멤버](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. **멤버 추가를** 선택하여 관리되는 멤버 추가 창을 엽니다.

1. **멤버 선택을** 선택하여 멤버 선택 창을 엽니다.

    ![권한 있는 역할 관리자 - 멤버 선택](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. 멤버를 선택한 다음 **선택**을 클릭합니다.

1. 멤버가 권한 있는 역할 **관리자** 역할을 받을 수 있도록 **하려면 확인을** 선택합니다.

    권한 있는 ID 관리의 다른 사람에게 새 역할을 할당하면 해당 역할이 자동으로 활성화될 수 있는 **자격으로** 구성됩니다.

1. 구성원을 영구적으로 만들려면 권한 있는 역할 관리자 구성원 목록에서 사용자를 선택합니다.

1. **더 보기를** 선택한 다음 **영구적으로 지정하여** 할당을 영구적으로 만듭니다.

    ![권한 있는 역할 관리자 - 영구 상태로 만들기](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. 사용자에게 권한 있는 [ID 관리를 사용하여 시작에](pim-getting-started.md)대한 링크를 보냅니다.

## <a name="remove-access-to-manage-pim"></a>PIM을 관리하기 위한 액세스 권한 제거

권한 있는 역할 관리자 역할에서 사용자를 제거하기 전에 항상 두 명 이상의 사용자가 이 역할에 할당되어 있는지 확인합니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

1. **역할**을 선택합니다.

1. 권한 **있는 역할 관리자** 역할을 선택하여 구성원 페이지를 엽니다.

1. 제거할 사용자 옆에 있는 확인란을 선택한 다음 **구성원 제거를**선택합니다.

    ![권한 있는 역할 관리자 - 멤버 제거](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. 역할에서 멤버를 제거할지 확인하라는 메시지가 표시되면 **예**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management 사용 시작](pim-getting-started.md)
