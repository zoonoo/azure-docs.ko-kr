---
title: PIM에서 Azure 리소스 역할에 대 한 액세스 검토 만들기-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure 리소스 역할에 대 한 액세스 검토를 만드는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 110a94c78427087f4ca5555f59055ab8e3bebcee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592668"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대 한 액세스 검토 만들기

직원의 권한 있는 Azure 리소스 역할에 대 한 액세스는 시간이 지남에 따라 변경 됩니다. 부실 역할 할당과 관련 된 위험을 줄이려면 액세스를 정기적으로 검토 해야 합니다. Azure Active Directory (Azure AD) Privileged Identity Management (PIM)를 사용 하 여 Azure 리소스 역할에 대 한 권한 있는 액세스에 대 한 액세스 검토를 만들 수 있습니다. 자동으로 발생 하는 되풀이 액세스 검토를 구성할 수도 있습니다. 이 문서에서는 하나 이상의 액세스 검토를 만드는 방법을 설명 합니다.

## <a name="prerequisite-role"></a>필수 역할

 액세스 검토를 만들려면 해당 리소스에 대 한 [소유자](../../role-based-access-control/built-in-roles.md#owner) 또는 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) Azure 역할에 할당 되어야 합니다.

## <a name="open-access-reviews"></a>액세스 검토 열기

1. 필수 역할 중 하나에 할당 된 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. 왼쪽 메뉴에서 **Azure 리소스** 를 선택 합니다.

1. 구독과 같이 관리 하려는 리소스를 선택 합니다.

1. 관리에서 **액세스 검토** 를 선택 합니다.

    ![Azure 리소스-모든 리뷰의 상태를 보여 주는 액세스 검토 목록](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. 새 액세스 검토를 만들려면 **새로 만들기** 를 클릭합니다.

1. 액세스 검토의 이름을 지정합니다. 필요한 경우 검토에 설명을 지정합니다. 이름 및 설명이 검토자에게 표시됩니다.

    ![액세스 검토 만들기 - 이름 및 설명 검토](./media/pim-resource-roles-start-access-review/name-description.png)

1. **시작 날짜** 를 설정합니다. 기본적으로 액세스 검토는 한 번 발생하고, 만들어진 시간에 시작되고, 1개월 후에 종료됩니다. 시작 날짜와 종료 날짜를 변경하여 액세스 검토를 나중에 시작하고 원하는 기간(일 수) 동안 지속할 수 있습니다.

    ![시작 날짜, 빈도, 기간, 종료, 횟수 및 종료 날짜](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. 액세스 검토를 반복하려면 **빈도** 설정을 **한 번** 에서 **매주**, **매월**, **분기마다**, **매년** 또는 **반년마다** 로 변경합니다. **기간** 슬라이더 또는 텍스트 상자를 사용하여 검토자의 입력에 대해 반복 시리즈의 각 검토가 열릴 일수를 정의합니다. 예를 들어 검토가 겹치는 상황을 방지하기 위해 월별 검토에 대해 설정할 수 있는 최대 기간은 27일입니다.

1. **종료** 설정을 사용하여 되풀이 액세스 검토 시리즈를 종료하는 방법을 지정합니다. 이 시리즈는 세 가지 방법으로 종료할 수 있습니다. 무기한으로, 특정 날짜까지, 또는 정의된 되풀이 횟수가 완료된 이후에 검토를 시작하도록 연속적으로 실행됩니다. 사용자, 다른 사용자 관리자 또는 다른 전역 관리자는 해당 날짜에 종료되도록 **설정** 에서 날짜를 변경하여 생성 후 시리즈를 중지할 수 있습니다.

1. **사용자** 섹션에서 멤버 자격을 검토할 역할을 하나 이상 선택합니다.

    ![역할 멤버 자격을 검토할 사용자 범위](./media/pim-resource-roles-start-access-review/users.png)

    > [!NOTE]
    > - 여기에서 선택한 역할에는 [영구적 역할 및 적격 역할](../privileged-identity-management/pim-how-to-add-role-to-user.md)이 모두 포함됩니다.
    > - 둘 이상의 역할을 선택하면 여러 액세스 검토가 생성됩니다. 예를 들어 5개의 역할을 선택하면 별도의 액세스 검토가 5개 생성됩니다.
    **Azure AD 역할** 의 액세스 검토를 만드는 경우 다음은 멤버 자격 검토 목록 예제를 보여 줍니다.

    ![선택할 수 있는 Azure AD 역할을 나열하는 멤버 자격 검토 창](./media/pim-resource-roles-start-access-review/review-membership.png)

    **Azure 리소스 역할** 의 액세스 검토를 만드는 경우 다음 이미지는 멤버 자격 검토 목록 예제를 보여 줍니다.

    ![선택할 수 있는 Azure 리소스 역할을 나열하는 멤버 자격 검토 창](./media/pim-resource-roles-start-access-review/review-membership-azure-resource-roles.png)

1. **검토자** 섹션에서 모든 사용자를 검토할 한 명 이상의 사용자를 선택합니다. 또는 구성원이 자신의 액세스 권한을 검토하도록 할 수도 있습니다.

    ![선택한 사용자 또는 멤버(자신)의 검토자 목록](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **선택한 사용자** - 액세스가 필요한 사용자를 알 수 없는 경우 이 옵션을 사용합니다. 이 옵션을 사용하여 리소스 소유자 또는 관리자 그룹에게 검토를 완료하도록 할당할 수 있습니다.
    - **멤버(자신)** - 사용자에게 자신의 역할 할당을 검토하게 하려면 이 옵션을 사용합니다. 
    - **관리자** –이 옵션을 사용 하 여 사용자의 관리자가 해당 역할 할당을 검토 하도록 합니다. 관리자를 선택 하면 대체 검토자를 지정 하는 옵션도 있습니다. 사용자가 디렉터리에 관리자를 지정 하지 않은 경우 대체 검토자에 게 사용자를 검토 하 라는 메시지가 표시 됩니다. 

### <a name="upon-completion-settings"></a>완료 시 설정

1. 검토가 완료된 후 수행할 작업을 지정하려면 **완료 시 설정** 섹션을 확장합니다.

    ![자동 응답 및 검토자가 응답하지 않을 경우에 대한 완료 시 설정](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. 거부된 사용자에 대한 액세스를 자동으로 제거하려면 **결과를 리소스에 자동 적용** 을 **사용** 으로 설정합니다. 검토가 완료될 때 결과를 수동으로 적용하려면 스위치를 **사용 안 함** 으로 설정합니다.

1. **검토자가 응답하지 않는 경우** 목록을 사용하여 검토 기간 내에 검토자가 검토하지 않은 사용자를 어떻게 할 것인지 지정합니다. 이 설정은 검토자가 수동으로 검토한 사용자에게 영향을 주지 않습니다. 최종 검토자의 결정이 [거부]이면 사용자의 액세스 권한이 제거됩니다.

    - **변경 없음** - 사용자의 액세스 권한을 그대로 유지
    - **액세스 권한 제거** - 사용자의 액세스 권한을 제거
    - **액세스 권한 승인** - 사용자의 액세스 권한을 승인
    - **권장 작업 수행** - 사용자의 지속적인 액세스를 거부할 것인지 아니면 승인할 것인지에 대한 시스템의 권장 사항을 수용

### <a name="advanced-settings"></a>고급 설정

1. 추가 설정을 지정하려면 **고급 설정** 섹션을 확장합니다.

    ![권장 사항 표시, 승인 이유, 메일 알림 및 미리 알림에 대한 고급 설정](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. **권장 사항 표시** 를 **사용** 으로 설정하면 사용자의 액세스 정보에 따라 검토자에게 시스템 권장 사항이 표시됩니다.

1. **승인 시 이유 필요** 를 **사용** 으로 설정하면 검토자가 승인 이유를 입력해야 합니다.

1. **메일 알림** 을 **사용** 으로 설정하면 Azure AD는 액세스 검토가 시작될 때 검토자에게 이메일 알림을 보내고 검토가 완료될 때 관리자에게 이메일 알림을 보냅니다.

1. **미리 알림** 을 **사용** 으로 설정하면 Azure AD는 검토를 완료하지 않은 검토자에게 진행 중인 액세스 검토에 대한 미리 알림을 보냅니다.
1. 검토자에 게 보내는 전자 메일의 내용은 검토 이름, 리소스 이름, 기한 등의 검토 세부 정보를 기반으로 자동 생성 됩니다. 추가 지침이 나 연락처 정보와 같은 추가 정보를 전달 하는 방법이 필요한 경우 할당 된 검토자에 게 전송 되는 초대 및 미리 알림 전자 메일에 포함 되는 **검토자의 추가 내용 전자 메일** 에 이러한 세부 정보를 지정할 수 있습니다. 아래 강조 표시 된 섹션은이 정보가 표시 되는 위치입니다.

    ![강조 표시를 사용 하 여 검토자에 게 보낸 전자 메일의 콘텐츠](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>액세스 검토 시작

액세스 검토에 대한 설정을 지정했으면 **시작** 을 클릭합니다. 액세스 검토는 상태 표시기를 사용 하 여 목록에 표시 됩니다.

![시작 된 검토의 상태를 보여 주는 액세스 검토 목록](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

검토가 시작되면 Azure AD에서 기본적으로 검토자에게 전자 메일을 보냅니다. Azure AD에서 전자 메일을 보내지 않도록 선택한 경우 검토자에게 완료할 때까지 대기 중인 액세스 검토가 있음을 알려야 합니다. [Azure 리소스 역할에](pim-resource-roles-perform-access-review.md)대 한 액세스를 검토 하는 방법에 대 한 지침을 표시할 수 있습니다.

## <a name="manage-the-access-review"></a>액세스 검토 관리

검토자가 액세스 검토의 **개요** 페이지에서 검토를 완료할 때 진행률을 추적할 수 있습니다. [검토가 완료](pim-resource-roles-complete-access-review.md)될 때까지 디렉터리에서 액세스 권한이 변경 되지 않습니다.

![검토 세부 정보를 표시 하는 액세스 검토 개요 페이지](./media/pim-resource-roles-start-access-review/access-review-overview.png)

일회성 검토 인 경우 액세스 검토 기간이 끝난 후 또는 관리자가 액세스 검토를 중지 한 후 [Azure 리소스 역할의 액세스 검토 완료](pim-resource-roles-complete-access-review.md) 의 단계에 따라 결과를 확인 하 고 적용 합니다.  

일련의 액세스 검토를 관리 하려면 액세스 검토로 이동 하 고 예정 된 검토에서 예정 된 항목을 찾아 종료 날짜를 편집 하거나 그에 따라 검토자를 추가/제거 합니다.

**완료 설정** 후의 선택에 따라 자동 적용은 검토의 종료 날짜 이후에 또는 수동으로 검토를 중지 한 후 실행 됩니다. 검토 상태는 **완료 됨** 에서 **적용** 하는 등의 중간 상태와 마지막으로 **적용** 된 상태 사이에서 변경 됩니다. 몇 분 안에 거부 된 사용자 (있는 경우)가 제거 되는 것을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스 역할에 대 한 액세스 검토](pim-resource-roles-perform-access-review.md)
- [Azure 리소스 역할에 대 한 액세스 검토 완료](pim-resource-roles-complete-access-review.md)
- [Azure AD 역할에 대 한 액세스 검토 만들기](pim-how-to-start-security-review.md)
