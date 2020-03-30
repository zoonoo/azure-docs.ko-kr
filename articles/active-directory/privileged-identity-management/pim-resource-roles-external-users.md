---
title: PIM - Azure AD의 게스트에게 Azure 리소스 역할 할당 | 마이크로 소프트 문서
description: PIM(Azure AD 권한 ID 관리)에서 외부 게스트 사용자를 초대하고 Azure 리소스 역할을 할당하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021932"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>게스트 사용자를 초대하고 권한 있는 ID 관리에서 Azure 리소스 역할을 할당합니다.

Azure Active Directory(Azure AD) 게스트 사용자는 Azure AD 내의 B2B(비즈니스 간) 공동 작업 기능의 일부이므로 외부 게스트 사용자 및 공급업체를 Azure AD의 게스트로 관리할 수 있습니다. B2B 공동 작업을 Azure AD 권한 ID 관리(PIM)와 결합하면 규정 준수 및 거버넌스 요구 사항을 게스트로 확장할 수 있습니다. 예를 들어 게스트와 함께 Azure ID 작업에 이러한 권한 ID 관리 기능을 사용할 수 있습니다.

- 특정 Azure 리소스에 대한 액세스 할당
- Just-In-Time VM 액세스 사용
- 할당 기간 및 종료 날짜 지정
- 활성 할당 또는 활성화시 다단계 인증 필요
- 액세스 검토 수행
- 경고 및 감사 로그 활용

이 문서에서는 권한 있는 ID 관리를 사용하여 게스트를 조직에 초대하고 Azure 리소스에 대한 액세스를 관리하는 방법을 설명합니다.

## <a name="when-would-you-invite-guests"></a>언제 손님을 초대하시겠습니까?

다음은 조직에 게스트를 초대할 수 있는 몇 가지 예입니다.

- 이메일 계정만 있는 외부 자영업자가 프로젝트에 대한 Azure 리소스에 액세스할 수 있도록 허용합니다.
- 온-프레미스 Active Directory Federation Services를 사용하는 대규모 조직의 외부 사용자가 비용 애플리케이션에 액세스할 수 있도록 허용합니다.
- 고객 조직에 속하지 않은 지원 엔지니어(예: Microsoft 지원)가 문제 해결을 위해 일시적으로 고객의 Azure 리소스에 액세스할 수 있도록 허용합니다.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>B2B 게스트를 사용한 공동 작업은 어떻게 작동합니까?

B2B 공동 작업을 사용하는 경우 외부 사용자를 게스트로 조직에 초대할 수 있습니다. 게스트는 조직의 사용자로 관리할 수 있지만 게스트는 Azure AD 조직이 아닌 홈 조직에서 인증되어야 합니다. 즉, 게스트가 더 이상 홈 조직에 액세스할 수 없는 경우 조직에 대한 액세스 권한도 잃게 됩니다. 예를 들어 게스트가 조직을 떠나면 아무 것도 하지 않고도 Azure AD에서 공유한 리소스에 대한 액세스 권한이 자동으로 손실됩니다. B2B 공동 작업에 대한 자세한 내용은 [Azure Active Directory B2B의 게스트 사용자 액세스란 무엇입니까?](../b2b/what-is-b2b.md)

![게스트 사용자가 홈 디렉터리에서 인증되는 방법을 보여주는 다이어그램](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>게스트 공동 작업 설정 확인

조직에 게스트를 초대하려면 게스트 공동작업 설정을 확인해야 합니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. **Azure Active 디렉터리** > **사용자 설정을**선택합니다.

1. **외부 공동 작업 설정 관리를 선택합니다.**

    ![권한, 초대 및 공동 작업 제한 설정을 표시하는 외부 공동 작업 설정 페이지](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. **게스트 초대자 역할의 관리자와 사용자가** 스위치를 초대할 수 있는지 **확인합니다.**

## <a name="invite-a-guest-and-assign-a-role"></a>게스트 를 초대하고 역할 할당

권한 있는 ID 관리를 사용하여 게스트를 초대하고 Azure 리소스 역할을 사용할 수 있도록 할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 또는 사용자 [관리자](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) 역할의 구성원인 사용자를 사용하여 [Azure 포털에](https://portal.azure.com/) 로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure 리소스**를 선택합니다.

1. **리소스 필터**를 사용하여 관리되는 리소스의 목록을 필터링합니다.

1. 리소스, 리소스 그룹, 구독 또는 관리 그룹과 같이 관리할 리소스를 선택합니다.

    게스트가 필요한 범위로만 범위를 설정해야 합니다.

1. 관리에서 **역할을** 선택하여 Azure 리소스의 역할 목록을 확인합니다.

    ![활성 및 적격 사용자 수를 보여 주는 Azure 리소스 역할 목록](./media/pim-resource-roles-external-users/resources-roles.png)

1. 사용자에게 필요한 최소 역할을 선택합니다.

    ![해당 역할의 현재 구성원을 나열하는 선택된 역할 페이지](./media/pim-resource-roles-external-users/selected-role.png)

1. 역할 페이지에서 멤버 **추가를** 선택하여 새 과제 창을 엽니다.

1. **멤버 또는 그룹 선택**을 클릭합니다.

    ![새 과제 - 초대 옵션과 함께 사용자 및 그룹을 나열하는 구성원 또는 그룹 창 선택](./media/pim-resource-roles-external-users/select-member-group.png)

1. 게스트를 초대하려면 **초대를**클릭합니다.

    ![상자가 있는 게스트 페이지를 초대하여 이메일 주소를 입력하고 개인 메시지를 지정합니다.](./media/pim-resource-roles-external-users/invite-guest.png)

1. 게스트를 선택한 후 **초대를**클릭합니다.

    게스트를 선택한 멤버로 추가해야 합니다.

1. 구성원 **또는 그룹 선택** 창에서 선택 **을**클릭합니다.

1. 멤버 **자격 설정** 창에서 할당 유형 및 기간을 선택합니다.

    ![새 과제 - 과제 유형, 시작 날짜 및 종료 날짜를 지정하는 옵션이 있는 멤버십 설정 페이지](./media/pim-resource-roles-external-users/membership-settings.png)

1. 과제를 완료하려면 완료를 선택한 다음 **추가**를 **선택합니다.**

    게스트 역할 할당이 역할 목록에 나타납니다.

    ![게스트를 적격으로 나열하는 역할 페이지](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>게스트역할 활성화

외부 사용자인 경우 Azure AD 조직의 게스트로 초대를 수락하고 역할 할당을 활성화할 수 있어야 합니다.

1. 초대장이 있는 이메일을 엽니다. 이메일은 다음과 비슷합니다.

    ![디렉터리 이름, 개인 메시지 및 시작 하기 링크가 있는 이메일 초대](./media/pim-resource-roles-external-users/email-invite.png)

1. 이메일에서 **시작** 하기 링크를 선택 합니다.

1. 권한을 검토한 후 **수락**을 클릭합니다.

    ![조직에서 검토할 권한 목록이 있는 브라우저의 사용 권한 페이지를 검토합니다.](./media/pim-resource-roles-external-users/invite-accept.png)

1. 사용 약관에 동의하고 로그인 상태를 유지할지 여부를 지정하라는 메시지가 표시될 수 있습니다. Azure 포털에서 역할을 받을 *자격이* 있는 경우 아직 리소스에 액세스할 수 없습니다.

1. 역할 할당을 활성화하려면 활성화 역할 링크가 있는 전자 메일을 엽니다. 이메일은 다음과 비슷합니다.

    ![역할 활성화 링크가 있는 역할에 대한 자격이 있음을 나타내는 이메일](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. **역할 활성화를** 선택하여 권한 있는 ID 관리에서 적격 한 역할을 엽니다.

    ![권한 있는 ID 관리의 내 역할 페이지에 적격 역할이 나열되어 있습니다.](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 작업 에서 **활성화** 링크를 선택합니다.

    역할 설정에 따라 역할을 활성화하려면 일부 정보를 지정해야 합니다.

1. 역할에 대한 설정을 지정했으면 **활성화**를 클릭하여 역할을 활성화합니다.

    ![시작 시간, 기간 및 이유를 지정하는 페이지 목록 범위 및 옵션을 활성화합니다.](./media/pim-resource-roles-external-users/activate-role.png)

    요청 승인에 관리자가 필요한 경우 외에는 지정된 리소스에 액세스할 수 있습니다.

## <a name="view-activity-for-a-guest"></a>게스트의 활동 보기

감사 로그를 보고 게스트가 수행하는 작업을 추적할 수 있습니다.

1. 관리자는 권한 있는 ID 관리를 열고 공유된 리소스를 선택합니다.

1. **해당 리소스의** 활동을 보려면 리소스 감사를 선택합니다. 다음은 리소스 그룹에 대한 작업의 예를 보여줍니다.

    ![Azure 리소스 - 시간, 요청자 및 작업을 나열하는 리소스 감사 페이지](./media/pim-resource-roles-external-users/audit-resource.png)

1. 게스트의 활동을 보려면 Azure **Active Directory** > **사용자** > *게스트 이름을*선택합니다.

1. **감사 로그를** 선택하여 조직의 감사 로그를 확인합니다. 필요한 경우 필터를 지정할 수 있습니다.

    ![디렉터리 감사 로그 나열 날짜, 대상, 시작 및 활동](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 관리자 역할 할당](pim-how-to-add-role-to-user.md)
- [Azure AD B2B 공동 작업의 게스트 사용자 액세스란 무엇입니까?](../b2b/what-is-b2b.md)
