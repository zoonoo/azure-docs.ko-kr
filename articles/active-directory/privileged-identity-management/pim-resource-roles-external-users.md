---
title: PIM에서 게스트에게 Azure 리소스 역할 할당 - Azure AD | Microsoft Docs
description: 외부 게스트 사용자를 초대하고 Azure AD Privileged Identity Management에서 Azure 리소스 역할을 할당하는 방법에 대해 알아보세요.
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4e2e18f3bb9d1c972d805a60493897d605921e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010642"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>게스트 사용자 초대 및 Privileged Identity Management에서 Azure 리소스 역할 할당

Azure Active Directory(Azure AD) 게스트 사용자는 Azure AD 내의 B2B(Business-to-Business) 협업 기능의 일부로 외부 게스트 사용자 및 공급업체를 게스트로 관리할 수 있습니다. B2B 협업과 Azure AD Privileged Identity Management를 결합하여 규정 준수 및 거버넌스 요구사항을 게스트에게 확장하여 적용할 수 있습니다. 예를 들어 게스트와 다음과 같은 Privileged Identity Management 기능을 Azure ID 작업에 사용할 수 있습니다.

- 특정 Azure 리소스에 대한 액세스 할당
- Just-In-Time VM 액세스 사용
- 할당 기간 및 종료 날짜 지정
- 활성 할당 또는 활성화에 대한 다단계 인증 필요
- 액세스 검토 수행
- 경고 및 감사 로그 활용

이 문서에서는 Privileged Identity Management를 사용하여 조직에 게스트를 초대하고 Azure 리소스에 대한 액세스를 관리하는 방법에 대해 설명합니다.

## <a name="when-would-you-invite-guests"></a>언제 게스트를 초대하나요?

다음은 조직에 게스트를 초대할 수 있는 몇 가지 경우입니다.

- 이메일 계정만 있는 외부 자영업자가 프로젝트에 대한 Azure 리소스에 액세스할 수 있도록 허용합니다.
- 온-프레미스 Active Directory Federation Services를 사용하는 대규모 조직의 외부 사용자가 비용 애플리케이션에 액세스할 수 있도록 허용합니다.
- 고객 조직에 속하지 않은 지원 엔지니어(예: Microsoft 지원)가 문제 해결을 위해 일시적으로 고객의 Azure 리소스에 액세스할 수 있도록 허용합니다.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>B2B 게스트를 사용한 협업은 어떤 방식으로 작동하나요?

B2B 협업을 사용하는 경우 외부 사용자를 조직에 게스트로 초대할 수 있습니다. 게스트는 조직에서 사용자로 관리될 수 있지만 Azure AD 조직이 아닌 홈 조직에서 인증되어야 합니다. 즉, 게스트가 자신의 홈 조직에 액세스할 수 없는 경우 해당 조직에도 액세스할 수 없습니다. 예를 들어, 게스트가 조직을 떠나면 아무런 조치를 취하지 않아도 Azure AD에서 공유된 모든 리소스에 대한 액세스 권한을 자동으로 잃게 됩니다. B2B 협업에 대한 자세한 내용은 [Azure Active Directory B2B에서 게스트 사용자 엑세스란?](../external-identities/what-is-b2b.md)을 참조하세요.

![홈 디렉토리에서 게스트 사용자를 인증하는 방법을 보여주는 다이어그램](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>게스트 협업 설정 확인

조직에 게스트를 초대할 수 있도록 게스트 협업 설정을 확인해야 합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. **Azure Active Directory**  > **사용자 설정** 선택

1. **외부 협업 설정 관리** 선택

    ![권한, 초대 및 협업 제한사항 설정이 표시되는 외부 협업 설정 페이지](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. **게스트 초대자 역할의 관리자 및 사용자** 가 스위치를 **예** 로 설정할 수 있는지 확인하세요.

## <a name="invite-a-guest-and-assign-a-role"></a>게스트 초대 및 역할 할당

Privileged Identity Management를 사용하여 게스트를 초대하고 Azure 리소스 역할에 대한 자격을 갖도록 만들 수 있습니다.

1. [Privileged Role Administrator](../roles/permissions-reference.md#privileged-role-administrator) 또는 [User Administrator](../roles/permissions-reference.md#user-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure 리소스** 를 선택합니다.

1. **리소스 필터** 를 사용하여 관리되는 리소스의 목록을 필터링합니다.

1. 관리할 리소스(예: 리소스, 리소스 그룹, 구독 또는 관리 그룹)를 선택합니다.

    게스트에게 필요한 범위까지만 설정해야 합니다.

1. 관리에서 **역할** 을 선택하여 Azure 리소스에 대한 역할 목록을 확인합니다.

    ![활성 및 자격 있는 사용자 수를 보여주는 Azure 리소스 역할 목록](./media/pim-resource-roles-external-users/resources-roles.png)

1. 사용자에게 필요한 최소 역할을 선택합니다.

    ![해당 역할의 현재 구성원을 표시하는 선택된 역할 페이지](./media/pim-resource-roles-external-users/selected-role.png)

1. 역할 페이지에서 **구성원 추가** 를 선택하여 새 할당 창을 여세요.

1. **멤버 또는 그룹 선택** 을 클릭합니다.

    ![새 할당 - 초대 옵션과 사용자 및 그룹을 나열하는 구성원 또는 그룹 창 선택](./media/pim-resource-roles-external-users/select-member-group.png)

1. 게스트를 초대하려면 **초대** 를 클릭하세요.

    ![상자가 있는 게스트 페이지를 초대하여 이메일 주소를 입력하고 개인 메시지를 지정하세요.](./media/pim-resource-roles-external-users/invite-guest.png)

1. 게스트를 선택한 후 **초대** 를 클릭하세요.

    게스트는 선택한 구성원으로 추가되어야 합니다.

1. **구성원 또는 그룹 선택** 창에서 **선택** 을 클릭하세요.

1. **구성원 자격 설정** 창에서 할당 유형 및 기간을 선택하세요.

    ![새 할당 - 할당 유형, 시작 및 종료 날짜 지정 옵션을 포함하는 구성원 설정 페이지](./media/pim-resource-roles-external-users/membership-settings.png)

1. 할당을 완료하려면 **완료** 와 **추가** 를 차례로 선택하세요.

    게스트 역할 할당이 역할 목록에 나타납니다.

    ![자격 있는 게스트를 표시하는 역할 페이지](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>게스트 역할 활성화

외부 사용자인 경우 Azure AD 조직의 게스트 초대를 수락하고 역할 할당을 활성화해야 합니다.

1. 초대가 포함된 이메일을 여세요. 이메일은 다음과 비슷합니다.

    ![디렉터리 이름, 개인 메시지 및 시작 링크가 포함된 이메일 초대](./media/pim-resource-roles-external-users/email-invite.png)

1. 이메일에서 **시작** 링크를 선택합니다.

1. 권한을 검토한 후 **수락** 을 클릭합니다.

    ![조직에서 검토하려는 권한 목록을 사용하여 브라우저의 사용 권한 페이지를 검토하세요.](./media/pim-resource-roles-external-users/invite-accept.png)

1. 사용 약관에 동의하고 로그인 상태 유지 여부를 지정하라는 메시지가 표시될 수 있습니다. Azure Portal에서 역할에 대한 *자격이 있는* 경우 리소스에 대한 액세스 권한이 아직 없습니다.

1. 역할 할당을 활성화하려면 역할 활성화 링크가 있는 이메일을 여세요. 이메일은 다음과 비슷합니다.

    ![역할에 대한 자격이 있다는 것을 보여주는 역할 활성화 링크 포함 이메일](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. **역할 활성화** 를 선택하여 Privileged Identity Managemen에서 자격이 있는 역할을 여세요.

    ![자격 있는 역할을 표시하는 Privileged Identity Management의 나의 역할 페이지](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 작업에서 **활성화** 링크를 선택하세요.

    역할 설정에 따라 역할을 활성화하려면 일부 정보를 지정해야 합니다.

1. 역할에 대한 설정을 지정했으면 **활성화** 를 클릭하여 역할을 활성화합니다.

    ![페이지 목록 범위 및 옵션을 활성화하여 시작 시간, 기간 및 이유 지정](./media/pim-resource-roles-external-users/activate-role.png)

    요청 승인에 관리자가 필요한 경우 외에는 지정된 리소스에 액세스할 수 있습니다.

## <a name="view-activity-for-a-guest"></a>게스트 활동 보기

감사 로그를 확인하여 게스트가 수행하는 작업을 추적할 수 있습니다.

1. 관리자는 Privileged Identity Management를 열고 공유된 리소스를 선택하세요.

1. 해당 리소스에 대한 작업을 보려면 **리소스 감사** 를 선택하세요. 다음은 리소스 그룹에 대한 작업의 예를 보여줍니다.

    ![Azure 리소스 - 시간, 요청자 및 작업을 표시하는 리소스 감사 페이지](./media/pim-resource-roles-external-users/audit-resource.png)

1. 게스트 작업을 보려면 **Azure Active Directory**  > **사용자** >  *게스트 이름* 을 선택하세요.

1. 조직의 감사 로그를 보려면 **감사 로그** 를 선택하세요. 필요한 경우 필터를 지정할 수 있습니다.

    ![날짜, 대상, 개시자 및 작업을 표시하는 디렉터리 감사 로그](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 관리자 역할 할당](pim-how-to-add-role-to-user.md)
- [Azure ADB2B 협업에서 게스트 사용자 액세스란 무엇인가요?](../external-identities/what-is-b2b.md)