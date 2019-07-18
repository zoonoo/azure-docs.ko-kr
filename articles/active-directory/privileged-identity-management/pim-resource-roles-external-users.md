---
title: 게스트를 초대 하 고 PIM-Azure Active Directory에서에서 Azure 리소스 역할 할당 | Microsoft Docs
description: 외부 게스트 사용자 초대 하 여 Azure AD Privileged Identity Management (PIM)에서 Azure 리소스 역할을 할당 하는 방법에 알아봅니다.
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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07476c9f5db64a5d107a493022fa3548fe0dae4c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476349"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>게스트 사용자를 초대 하 고 PIM에서 Azure 리소스 역할 할당

Azure Active Directory (Azure AD)-비즈니스 (b2b)는 조직 외부 게스트 (게스트) 사용자 및 모든 계정을 사용 하 여 공급 업체와 공동 작업을 수행할 수 있도록 Azure AD 내에서 기능 집합. B2B를 사용 하 여 Azure AD Privileged Identity Management (PIM)와 결합 하면 게스트에 규정 준수 및 거 버 넌 스 요구를 적용할 계속 수 있습니다. 예를 들어 게스트를 사용 하 여 Azure id 작업에 대 한 이러한 PIM 기능을 사용할 수 있습니다.

- 특정 Azure 리소스에 대한 액세스 할당
- Just-In-Time 액세스 사용
- 할당 기간 및 종료 날짜 지정
- 활성 할당 또는 활성화 시 MFA 요구
- 액세스 검토 수행
- 경고 및 감사 로그 활용

이 문서에서는 조직에 게스트를 초대 Privileged Identity Management를 사용 하 여 Azure 리소스에 대 한 액세스를 관리 하는 방법을 설명 합니다.

## <a name="when-would-you-invite-guests"></a>게스트를 초대할 수는 경우는?

조직에 게스트를 초대할 수 있습니다 하는 경우 몇 가지 예제 시나리오 다음과 같습니다.

- 이메일 계정만 있는 외부 자영업자가 프로젝트에 대한 Azure 리소스에 액세스할 수 있도록 허용합니다.
- 온-프레미스 Active Directory Federation Services를 사용하는 대규모 조직의 외부 사용자가 비용 애플리케이션에 액세스할 수 있도록 허용합니다.
- 고객 조직에 속하지 않은 지원 엔지니어(예: Microsoft 지원)가 문제 해결을 위해 일시적으로 고객의 Azure 리소스에 액세스할 수 있도록 허용합니다.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>공동 작업은 어떻게 작업을 게스트 B2B를 사용 하 여?

B2B 공동 작업을 사용 하면 게스트로 조직 외부 사용자를 초대할 수 있습니다. 게스트 조직에 나타나지만 게스트 연결 된 모든 자격 증명이 없습니다. 게스트에 인증할 때마다 홈 조직에서 및 조직에 없는 인증 되어야 합니다. 이 경우 게스트는 홈 조직에 대 한 액세스를 더 이상, 또한 잃게 액세스 조직에 의미 합니다. 예를 들어 게스트 조직을 퇴사, 하는 경우 자동으로 잃게 액세스 모든 리소스를 공유 하 고 Azure AD에서 아무 작업도 수행 하지 않아도 됩니다. B2B에 대한 자세한 내용은 [Azure Active Directory B2B의 게스트 사용자 액세스란?](../b2b/what-is-b2b.md)을 참조하세요.

![어떻게 게스트 사용자 디렉터리에 나타나지만 해당 홈 디렉터리에 인증 된 보여 주는 다이어그램](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>게스트 공동 작업 설정 확인

조직에 게스트를 초대할 수 있는지, 공동 작업 게스트 설정을 확인 해야 합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. **Azure Active Directory** > **사용자 설정**을 클릭합니다.

1. **외부 공동 작업 설정 관리**를 클릭합니다.

    ![사용 권한, 초대 및 공동 작업 제한 설정을 표시 하는 외부 공동 작업 설정 페이지](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. **관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음** 스위치를 **예**로 설정합니다.

## <a name="invite-a-guest-and-assign-a-role"></a>게스트 초대 하 고 역할 할당

PIM을 사용 하 여 게스트를 초대할 수 있으며 멤버 사용자와 마찬가지로 Azure 리소스 역할에 대 한 자격을 부여할 수도 있습니다.

1. 에 로그인 [Azure portal](https://portal.azure.com/) 의 구성원 인 사용자와는 [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 또는 [사용자 관리자](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) 역할입니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. **리소스 필터**를 사용하여 관리되는 리소스의 목록을 필터링합니다.

1. 리소스, 리소스 그룹, 구독, 관리 그룹 등 관리하려는 리소스를 클릭합니다.

    어떤 게스트 하기만 하려면 범위를 설정 해야 합니다.

1. 관리 아래에서 **역할**을 클릭하여 Azure 리소스에 대한 역할 목록을 표시합니다.

    ![Azure 리소스 역할에는 활성 및 적격 된 사용자의 수를 보여주는 목록](./media/pim-resource-roles-external-users/resources-roles.png)

1. 사용자에게 필요한 최소 역할을 클릭합니다.

    ![해당 역할의 현재 멤버를 나열 하는 역할 페이지 선택](./media/pim-resource-roles-external-users/selected-role.png)

1. [역할] 페이지에서 **멤버 추가**를 클릭하여 [새 할당] 창을 엽니다.

1. **멤버 또는 그룹 선택**을 클릭합니다.

    ![새 할당-멤버를 선택 하거나 사용자 및 그룹 초대 옵션 함께 나열 된 테이블 창 그룹](./media/pim-resource-roles-external-users/select-member-group.png)

1. 게스트를 초대 하려면 클릭 **초대**합니다.

    ![초대 전자 메일 주소를 입력 하 고 개인 메시지를 지정 하는 상자를 사용 하 여 게스트 페이지](./media/pim-resource-roles-external-users/invite-guest.png)

1. 사용자가 게스트를 선택한 후 클릭 **초대**합니다.

    게스트는 선택한 구성원으로 추가 되어야 합니다.

1. 에 **멤버 또는 그룹 선택** 창 클릭 **선택**합니다.

1. 에 **멤버 자격 설정** 창, 할당 유형 및 기간을 선택 합니다.

    ![새 할당이 할당 형식 지정, 시작 날짜 및 종료 날짜에 대 한 옵션을 사용 하 여 멤버 자격 설정 페이지](./media/pim-resource-roles-external-users/membership-settings.png)

1. 할당을 완료하려면 **완료**, **추가**를 차례로 클릭합니다.

    게스트 역할 할당이 역할 목록에 표시 됩니다.

    ![적격으로 게스트를 나열 하는 역할 페이지](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>게스트로 역할 활성화

먼저 외부 사용자를 Azure AD 조직에 초대를 수락할 수 있는 사용자의 역할을 활성화 해야 합니다.

1. 전자 메일 초대를 사용 하 여 엽니다. 이메일은 다음과 비슷합니다.

    ![디렉터리 이름, 개인 메시지 및 시작 링크를 사용 하 여 전자 메일 초대](./media/pim-resource-roles-external-users/email-invite.png)

1. 이메일에서 **시작** 링크를 클릭합니다.

1. 권한을 검토한 후 **수락**을 클릭합니다.

    ![검토 하려는 조직은 검토 하는 사용 권한 목록 사용 하 여 브라우저에서 사용 권한 페이지](./media/pim-resource-roles-external-users/invite-accept.png)

1. 사용 약관을 동의 하 고 로그인 상태를 유지할 것인지 여부를 지정 하 라는 메시지가 표시 될 수 있습니다.

    Azure Portal이 열립니다. 역할에 대한 자격만 갖춘 경우 리소스에 액세스할 수 없습니다.

1. 역할을 활성화하려면 역할 활성화 링크를 사용하여 이메일을 엽니다. 이메일은 다음과 비슷합니다.

    ![PIM 활성화 역할 링크를 사용 하 여 역할에 대 한 적격 해당 나타내는에서 전자 메일 메시지](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. **역할 활성화**를 클릭하여 적합한 역할을 PIM에서 엽니다.

    ![적격 역할을 나열 하는 PIM의 역할 페이지](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. [작업] 아래에서 **활성화** 링크를 클릭합니다.

    역할 설정에 따라 역할을 활성화하려면 일부 정보를 지정해야 합니다.

1. 역할에 대한 설정을 지정했으면 **활성화**를 클릭하여 역할을 활성화합니다.

    ![페이지 목록 범위 및 시작 시간, 기간 및 이유를 지정 하는 옵션을 활성화 합니다.](./media/pim-resource-roles-external-users/activate-role.png)

    요청 승인에 관리자가 필요한 경우 외에는 지정된 리소스에 액세스할 수 있습니다.

## <a name="view-activity-for-a-guest"></a>게스트에 대 한 활동 보기

멤버 사용자와 마찬가지로 추적 하기 위해 게스트 수행 하는 감사 로그를 볼 수 있습니다.

1. 관리자로서 PIM을 열고 공유되는 리소스를 선택합니다.

1. **리소스 감사**를 클릭하여 해당 리소스에 대한 작업을 살펴봅니다. 다음은 리소스 그룹에 대한 작업의 예를 보여줍니다.

    ![Azure 리소스-리소스 감사와 시간, 요청자, 작업 목록 페이지](./media/pim-resource-roles-external-users/audit-resource.png)

1. 게스트에 대 한 작업을 보려면 클릭 **Azure Active Directory** > **사용자** > 게스트 이름입니다.

1. 클릭 **감사 로그** 조직에 대 한 감사 로그를 확인 합니다. 필요한 경우 필터를 지정할 수 있습니다.

    ![디렉터리 감사 로그 목록 날짜, 실행자, 대상 및 작업](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 관리자 역할 할당](pim-how-to-add-role-to-user.md)
- [Azure Active Directory B2B의 게스트 사용자 액세스란?](../b2b/what-is-b2b.md)
