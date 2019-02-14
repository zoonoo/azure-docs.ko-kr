---
title: 외부 사용자를 초대하고 PIM에서 Azure 리소스 역할 할당 | Microsoft Docs
description: 외부 사용자를 초대하고 Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할을 할당하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90d0d3d3f484044a0ffbab7a3c24a76c40aa74c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208279"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>외부 사용자를 초대하고 PIM에서 Azure 리소스 역할 할당

Azure AD(Azure Activity Directory) B2B는 조직에서 아무 계정을 사용하여 외부 사용자 및 공급 업체와 공동 작업을 수행할 수 있게 해주는 Azure AD 내부의 기능 세트입니다. B2B와 Azure AD PIM(Privileged Identity Management)을 결합하면 규정 준수 및 거버넌스 요구 사항을 외부 사용자에게 지속적으로 적용할 수 있습니다. 예를 들어 외부 사용자가 있는 Azure 리소스에 이러한 PIM 기능을 사용하여 다음과 같은 일을 할 수 있습니다.

- 특정 Azure 리소스에 대한 액세스 할당
- Just-In-Time 액세스 사용
- 할당 기간 및 종료 날짜 지정
- 활성 할당 또는 활성화 시 MFA 요구
- 액세스 검토 수행
- 경고 및 감사 로그 활용

이 문서에서는 PIM을 사용하여 외부 사용자를 디렉터리에 초대하고 Azure 리소스에 대한 액세스를 관리하는 방법을 설명합니다.

## <a name="when-would-you-invite-external-users"></a>언제 외부 사용자를 초대하나요?

다음은 외부 사용자를 디렉터리에 초대하는 몇 가지 예제 시나리오입니다.

- 이메일 계정만 있는 외부 자영업자가 프로젝트에 대한 Azure 리소스에 액세스할 수 있도록 허용합니다.
- 온-프레미스 Active Directory Federation Services를 사용하는 대규모 조직의 외부 사용자가 비용 애플리케이션에 액세스할 수 있도록 허용합니다.
- 고객 조직에 속하지 않은 지원 엔지니어(예: Microsoft 지원)가 문제 해결을 위해 일시적으로 고객의 Azure 리소스에 액세스할 수 있도록 허용합니다.

## <a name="how-does-external-collaboration-using-b2b-work"></a>B2B를 사용한 외부 공동 작업은 어떤 방식으로 작동하나요?

B2B를 사용하면 디렉터리에 외부 사용자를 초대할 수 있습니다. 외부 사용자는 디렉터리에 있는 것으로 나타나지만, 해당 사용자와 연결된 자격 증명이 없습니다. 외부 사용자는 인증이 필요할 때마다 고객의 디렉터리가 아닌 외부 사용자의 홈 디렉터리에서 인증해야 합니다. 즉, 외부 사용자가 홈 디렉터리에 대한 액세스 권한을 잃게 되면 자동으로 고객의 디렉터리에 대한 액세스 권한도 잃게 됩니다. 예를 들어 외부 사용자가 조직을 떠나면 고객이 아무 조치를 취하지 않아도 외부 사용자는 디렉터리에서 공유되는 모든 리소스에 대한 액세스 권한을 잃게 됩니다. B2B에 대한 자세한 내용은 [Azure Active Directory B2B의 게스트 사용자 액세스란?](../b2b/what-is-b2b.md)을 참조하세요.

![B2B 및 외부 사용자](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>외부 공동 작업 설정 확인

디렉터리에 외부 사용자를 초대할 수 있도록, 외부 공동 작업 설정을 확인해야 합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. **Azure Active Directory** > **사용자 설정**을 클릭합니다.

1. **외부 공동 작업 설정 관리**를 클릭합니다.

    ![외부 공동 작업 설정](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. **관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음** 스위치를 **예**로 설정합니다.

## <a name="invite-an-external-user-and-assign-a-role"></a>외부 사용자를 초대하고 역할 할당

PIM을 사용하여 외부 사용자를 초대하고 멤버 사용자처럼 Azure 리소스 역할에 대한 자격을 부여할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 또는 [사용자 계정 관리자](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. **리소스 필터**를 사용하여 관리되는 리소스의 목록을 필터링합니다.

1. 리소스, 리소스 그룹, 구독, 관리 그룹 등 관리하려는 리소스를 클릭합니다.

    외부 사용자에게 필요한 범위까지만 설정해야 합니다.

1. 관리 아래에서 **역할**을 클릭하여 Azure 리소스에 대한 역할 목록을 표시합니다.

    ![Azure 리소스 역할](./media/pim-resource-roles-external-users/resources-roles.png)

1. 사용자에게 필요한 최소 역할을 클릭합니다.

    ![선택한 역할](./media/pim-resource-roles-external-users/selected-role.png)

1. [역할] 페이지에서 **멤버 추가**를 클릭하여 [새 할당] 창을 엽니다.

1. **멤버 또는 그룹 선택**을 클릭합니다.

    ![멤버 또는 그룹 선택](./media/pim-resource-roles-external-users/select-member-group.png)

1. 외부 사용자를 초대하려면 **초대**를 클릭합니다.

    ![게스트 초대](./media/pim-resource-roles-external-users/invite-guest.png)

1. 외부 사용자를 지정한 후 **초대**를 클릭합니다.

    외부 사용자를 선택한 멤버로 추가해야 합니다.

1. [멤버 또는 그룹 선택] 창에서 **선택**을 클릭합니다.

1. [멤버] 설정 창에서 할당 유형 및 기간을 선택합니다.

    ![멤버 자격 설정](./media/pim-resource-roles-external-users/membership-settings.png)

1. 할당을 완료하려면 **완료**, **추가**를 차례로 클릭합니다.

    외부 사용자 역할 할당이 역할 목록에 표시됩니다.

    ![외부 사용자에 대한 역할 할당](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>외부 사용자로 역할 활성화

외부 사용자는 먼저 Azure AD 디렉터리 초대를 수락하고 가능하다면 역할을 활성화해야 합니다.

1. 디렉터리 초대를 사용하여 이메일을 엽니다. 이메일은 다음과 비슷합니다.

    ![이메일 초대](./media/pim-resource-roles-external-users/email-invite.png)

1. 이메일에서 **시작** 링크를 클릭합니다.

1. 권한을 검토한 후 **수락**을 클릭합니다.

    ![권한 검토](./media/pim-resource-roles-external-users/invite-accept.png)

1. 사용 약관에 동의하고 로그인 상태를 유지할 것인지 지정하라는 메시지가 표시될 수 있습니다.

    Azure Portal이 열립니다. 역할에 대한 자격만 갖춘 경우 리소스에 액세스할 수 없습니다.

1. 역할을 활성화하려면 역할 활성화 링크를 사용하여 이메일을 엽니다. 이메일은 다음과 비슷합니다.

    ![이메일 초대](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. **역할 활성화**를 클릭하여 적합한 역할을 PIM에서 엽니다.

    ![내 역할 - 적격](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. [작업] 아래에서 **활성화** 링크를 클릭합니다.

    역할 설정에 따라 역할을 활성화하려면 일부 정보를 지정해야 합니다.

1. 역할에 대한 설정을 지정했으면 **활성화**를 클릭하여 역할을 활성화합니다.

    ![역할 활성화](./media/pim-resource-roles-external-users/activate-role.png)

    요청 승인에 관리자가 필요한 경우 외에는 지정된 리소스에 액세스할 수 있습니다.

## <a name="view-activity-for-an-external-user"></a>외부 사용자의 작업 보기

멤버 사용자와 마찬가지로, 감사 로그를 살펴보고 외부 사용자가 어떤 일을 하는지 추적할 수 있습니다.

1. 관리자로서 PIM을 열고 공유되는 리소스를 선택합니다.

1. **리소스 감사**를 클릭하여 해당 리소스에 대한 작업을 살펴봅니다. 다음은 리소스 그룹에 대한 작업의 예를 보여줍니다.

    ![리소스 감사](./media/pim-resource-roles-external-users/audit-resource.png)

1. 외부 사용자에 대한 작업을 보려면 **Azure Active Directory** > **사용자** > 외부 사용자를 클릭합니다.

1. **감사 로그**를 클릭하여 디렉터리에 대한 감사 로그를 살펴봅니다. 필요한 경우 필터를 지정할 수 있습니다.

    ![디렉터리 감사](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 디렉터리 역할 할당](pim-how-to-add-role-to-user.md)
- [Azure Active Directory B2B의 게스트 사용자 액세스란?](../b2b/what-is-b2b.md)
