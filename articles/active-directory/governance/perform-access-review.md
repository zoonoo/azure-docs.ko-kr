---
title: 액세스 검토에서 응용 프로그램 & 그룹에 대 한 액세스 검토-Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 구성원 또는 응용 프로그램 액세스에 대 한 액세스를 검토 하는 방법에 대해 알아봅니다.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e03ba5e7a4a24ee3114946230f78a64ea3d42cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265684"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 및 응용 프로그램에 대 한 액세스 검토

Azure ad (Azure Active Directory)는 기업에서 azure ad 액세스 검토 라는 기능을 사용 하 여 Azure AD 및 기타 Microsoft Online Services의 그룹 및 응용 프로그램에 대 한 액세스를 관리 하는 방법을 간소화 합니다. 이 문서에서는 지정 된 검토자가 그룹 구성원 또는 응용 프로그램에 대 한 액세스 권한이 있는 사용자에 대 한 액세스 검토를 수행 하는 방법에 대해 설명 합니다. 검토 하려면 패키지에 대 한 액세스 [검토 AZURE AD 자격 관리에서 액세스 패키지의 액세스 검토](entitlement-management-access-reviews-review-access.md) 를 참조 하세요.

## <a name="perform-access-review-using-my-apps"></a>내 앱을 사용 하 여 액세스 검토 수행

알림 전자 메일에서 또는 사이트로 직접 이동 하 여 액세스 검토 프로세스를 시작할 수 있습니다.

- **전자 메일**:

>[!IMPORTANT]
> 전자 메일을 받을 때 지연이 발생할 수 있으며, 일부 경우에는 최대 24 시간이 걸릴 수 있습니다. 받는 azure-noreply@microsoft.com 사람 목록에를 추가 하 여 모든 전자 메일을 수신 하 고 있는지 확인 합니다.

1. Microsoft에서 액세스를 검토 하도록 요청 하는 전자 메일을 찾습니다. 그룹에 대 한 액세스를 검토 하는 예제 메일은 다음과 같습니다.

    ![그룹에 대 한 액세스를 검토 하는 Microsoft의 예제 전자 메일을 보여 주는 스크린샷](./media/perform-access-review/access-review-email.png)

1. **검토 시작** 링크를 클릭 하 여 액세스 검토를 엽니다.

- **전자 메일이 없는 경우**다음 단계를 수행 하 여 보류 중인 액세스 검토를 찾을 수 있습니다.

    1. 에서 내 앱 포털에 로그인 [https://myapps.microsoft.com](https://myapps.microsoft.com) 합니다.

        ![사용 권한이 있는 앱을 나열 하는 내 앱 포털](./media/perform-access-review/myapps-access-panel.png)

    1. 페이지의 오른쪽 위 모서리에서 사용자 이름 및 기본 조직 옆의 사용자를 클릭 합니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

    1. **액세스 검토** 타일을 클릭 하 여 보류 중인 액세스 검토 목록을 표시 합니다.

        > [!NOTE]
        > **액세스 검토** 타일이 표시 되지 않는 경우 해당 조직에 대해 수행할 액세스 검토가 없으며 지금은 아무런 조치도 필요 하지 않습니다.

        ![앱 및 그룹에 대 한 보류 중인 액세스 검토 목록을 보여 주는 스크린샷](./media/perform-access-review/access-reviews-list.png)

    1. 수행 하려는 액세스 검토에 대 한 **검토 시작** 링크를 클릭 합니다.

액세스 검토를 열면 액세스를 검토 해야 하는 사용자의 이름이 표시 됩니다.

사용자가 자신의 액세스를 검토 하는 것으로 요청 하는 경우 페이지가 다르게 표시 됩니다. 자세한 내용은 [그룹 또는 응용 프로그램에 대 한 직접 액세스 검토](review-your-access.md)를 참조 하세요.

![검토할 사용자를 나열 하는 액세스 검토 열기](./media/perform-access-review/perform-access-review.png)

액세스를 승인 하거나 거부할 수 있는 두 가지 방법이 있습니다.

- 각 사용자 요청에 대 한 적절 한 작업을 선택 하 여 하나 이상의 사용자에 대 한 액세스를 승인 하거나 거부할 수 있습니다.
- 시스템 권장 사항을 수락할 수 있습니다.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대 한 액세스 승인 또는 거부

1. 사용자 목록을 검토 하 고 사용자의 지속적인 액세스를 승인 또는 거부할지 결정 합니다.

    - 단일 사용자에 대 한 액세스를 승인 하거나 거부 하려면 해당 행을 클릭 하 여 수행할 작업을 지정 하는 창을 엽니다. 
    - 여러 사용자에 대 한 액세스를 승인 하거나 거부 하려면 사용자 옆에 확인 표시를 추가 하 고 **X 사용자 검토** 단추를 클릭 하 여 수행할 작업을 지정 하는 창을 엽니다.

1. **승인** 또는 **거부**를 클릭 합니다. 

    ![승인, 거부 및 인식 되지 않는 옵션을 포함 하는 작업 창](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > 확실 하지 않은 경우 **알 수 없음**을 클릭 하면 됩니다. 그리고 사용자가 액세스를 유지 하기 위해 사용자가 선택 하는 것은 감사 로그에 기록 됩니다.

1. 액세스 검토 관리자는 결정에 대 한 **이유** 상자에 이유를 제공 해야 할 수 있습니다. 이유가 필요 하지 않은 경우에도 마찬가지입니다. 여전히 결정에 대 한 이유를 제공할 수 있으며 포함 하는 정보는 다른 검토자가 사용할 수 있습니다.

1. 수행할 작업을 지정 했으면 **저장**을 클릭 합니다.

    >[!NOTE]
    > 액세스 검토가 끝나기 전에 언제 든 지 응답을 변경할 수 있습니다. 응답을 변경 하려면 행을 선택 하 고 응답을 업데이트 합니다. 예를 들어 이전에 거부 된 사용자를 승인 하거나 이전에 승인 된 사용자를 거부할 수 있습니다.

    >[!IMPORTANT]
    > - 사용자의 액세스가 거부 되 면 즉시 제거 되지 않습니다. 이러한 설정은 검토 기간이 종료 되거나 관리자가 [자동 적용](complete-access-review.md#apply-the-changes) 이 사용 하도록 설정 된 경우 검토를 중지할 때 제거 됩니다.
    > - 검토자가 여러 개인 경우 마지막 제출 된 응답이 기록 됩니다. 관리자가 Alice와 Bob의 두 검토자를 지정 하는 예를 살펴보겠습니다. Alice가 액세스 검토를 먼저 열고 사용자의 액세스 요청을 승인 합니다. 검토 기간이 끝나기 전에 Bob은 액세스 검토를 열고 이전에 Alice가 승인한 동일한 요청에 대 한 액세스를 거부 합니다. 액세스를 거부 하는 마지막 결정은 기록 되는 응답입니다.

### <a name="approve-or-deny-access-based-on-recommendations"></a>권장 사항에 따라 액세스 승인 또는 거부

액세스 검토를 보다 쉽고 빠르게 수행할 수 있도록 한 번의 클릭으로 수락할 수 있는 권장 사항도 제공 합니다. 권장 사항은 사용자의 로그인 활동을 기반으로 생성 됩니다.

1. 페이지 아래쪽의 파란색 표시줄에서 **권장 구성 적용**을 클릭 합니다.

    !["권장 구성 적용" 단추가 선택 된 상태에서 열기 액세스 검토를 보여 주는 스크린샷](./media/perform-access-review/accept-recommendations.png)

    권장 작업에 대 한 요약이 표시 됩니다.

    ![권장 작업의 요약을 표시 하는 창](./media/perform-access-review/accept-recommendations-summary.png)

1. **확인** 을 클릭 하 여 권장 사항을 적용 합니다.

## <a name="perform-access-review-using-my-access-new"></a>내 액세스를 사용 하 여 액세스 검토 수행 (신규)

업데이트 된 사용자 인터페이스를 사용 하 여 새 검토자 환경에 액세스할 수 있는 방법은 다음과 같습니다.

### <a name="my-apps-portal"></a>내 앱 포털

1. 에서 내 앱에 로그인 [https://myapps.microsoft.com](https://myapps.microsoft.com) 합니다.

    ![사용 권한이 있는 앱을 나열 하는 내 앱 포털](./media/perform-access-review/myapps-access-panel.png)

2. **액세스 검토** 타일을 클릭 하 여 보류 중인 액세스 검토 목록을 표시 합니다.

    > [!NOTE]
    > **액세스 검토** 타일이 표시 되지 않는 경우 해당 조직에 대해 수행할 액세스 검토가 없으며 지금은 아무런 조치도 필요 하지 않습니다.

![미리 보기 중에 표시 되는 새로운 경험을 사용 하 여 앱 및 그룹에 대 한 보류 중인 액세스 검토 목록](./media/perform-access-review/banner.png)

3. **시도** 를 클릭 합니다. 페이지 맨 위에 있는 배너입니다. 그러면 새 내 액세스 환경으로 이동 됩니다.
  
### <a name="email"></a>Email

  >[!IMPORTANT]
> 전자 메일을 받을 때 지연이 발생할 수 있으며, 일부 경우에는 최대 24 시간이 걸릴 수 있습니다. 받는 azure-noreply@microsoft.com 사람 목록에를 추가 하 여 모든 전자 메일을 수신 하 고 있는지 확인 합니다.

   1. Microsoft에서 액세스를 검토 하도록 요청 하는 전자 메일을 찾습니다. 아래 예제 메일 메시지를 확인할 수 있습니다.

   ![그룹에 대 한 액세스를 검토 하기 위한 Microsoft의 예제 메일](./media/perform-access-review/access-review-email-preview.png)

   2. **검토 시작** 링크를 클릭 하 여 액세스 검토를 엽니다.

>[!NOTE]
>시작을 클릭 하 여 **내 앱** 으로 이동 하는 경우 **내 앱 포털**아래 섹션에 나열 된 단계를 수행 합니다.

### <a name="navigate-to-my-access-directly"></a>직접 액세스로 이동

브라우저를 사용 하 여 내 액세스를 열어 보류 중인 액세스 검토를 볼 수도 있습니다.

1. 에서 내 액세스에 로그인 https://myaccess.microsoft.com/

2. 왼쪽 막대의 메뉴에서 **액세스 검토** 를 선택 하 여 사용자에 게 할당 된 보류 중인 액세스 검토 목록을 표시 합니다.

   ![메뉴의 액세스 검토](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대 한 액세스 승인 또는 거부

그룹 및 앱에서 내 액세스를 연 후 다음을 확인할 수 있습니다.

- **이름** 액세스 검토의 이름입니다.
- **기한** 검토의 기한입니다. 이 날짜를 거부 한 후에는 검토 중인 그룹 또는 앱에서 사용자를 제거할 수 있습니다.
- **리소스** 검토 중인 리소스의 이름입니다.
- **진행률** 이 액세스 검토의 총 사용자 수에 대해 검토 한 사용자 수입니다.

액세스 검토의 이름을 클릭 하 여 시작 합니다.

![앱 및 그룹에 대 한 보류 중인 액세스 검토 목록](./media/perform-access-review/access-reviews-list-preview.png)

열리고 나면 액세스 검토의 범위에 사용자 목록이 표시 됩니다. 사용자가 자신의 액세스를 검토 하는 것으로 요청 하는 경우 페이지가 다르게 표시 됩니다. 자세한 내용은 [그룹 또는 응용 프로그램에 대 한 직접 액세스 검토](review-your-access.md)를 참조 하세요.

액세스를 승인 하거나 거부할 수 있는 두 가지 방법이 있습니다.

- 하나 이상의 사용자에 대 한 액세스를 수동으로 승인 또는 거부할 수 있습니다.
- 시스템 권장 사항을 수락할 수 있습니다.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대 한 액세스를 수동으로 승인 또는 거부

1. 사용자 목록을 검토 하 고 사용자의 지속적인 액세스를 승인 또는 거부할지 결정 합니다.
2. 이름 옆에 있는 원을 클릭 하 여 하나 이상의 사용자를 선택 합니다.
3. 위의 표시줄에서 **승인** 또는 **거부** 를 선택 합니다.
    - 확실 하지 않은 경우 **알 수 없음**을 클릭 하면 됩니다. 사용자는 자신의 액세스를 유지 하기 위해 사용자가 선택 하 여 감사 로그에 기록 합니다. 사용자가 제공 하는 모든 정보는 다른 검토자에 게 제공 된다는 점을 명심 해야 합니다. 사용자는 자신의 의견을 읽고 요청을 검토할 때이를 고려할 수 있습니다.

    ![검토가 필요한 사용자를 나열 하는 액세스 검토 열기](./media/perform-access-review/user-list-preview.png)

4. 액세스 검토 관리자는 결정에 대 한 **이유** 상자에 이유를 제공 해야 할 수 있습니다. 이유가 필요 하지 않은 경우에도 마찬가지입니다. 계속 해 서 의사 결정에 대 한 이유를 제공할 수 있으며, 포함 하는 정보는 다른 승인자가 검토할 수 있게 됩니다.

5. **제출**을 클릭합니다.
    - 액세스 검토가 종료 될 때까지 언제 든 지 응답을 변경할 수 있습니다. 응답을 변경 하려면 행을 선택 하 고 응답을 업데이트 합니다. 예를 들어 이전에 거부 된 사용자를 승인 하거나 이전에 승인 된 사용자를 거부할 수 있습니다.

 >[!IMPORTANT]
 > - 사용자의 액세스가 거부 되 면 즉시 제거 되지 않습니다. 검토 기간이 종료 되거나 관리자가 검토를 중지 하면 제거 됩니다. 
 > - 검토자가 여러 개인 경우 마지막 제출 된 응답이 기록 됩니다. 관리자가 Alice와 Bob의 두 검토자를 지정 하는 예를 살펴보겠습니다. Alice가 액세스 검토를 먼저 열고 사용자의 액세스 요청을 승인 합니다. 검토 기간이 끝나기 전에 Bob은 액세스 검토를 열고 이전에 Alice가 승인한 동일한 요청에 대 한 액세스를 거부 합니다. 액세스를 거부 하는 마지막 결정은 기록 되는 응답입니다.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>권장 사항에 따라 액세스 승인 또는 거부

액세스 검토를 보다 쉽고 빠르게 수행할 수 있도록 한 번의 클릭으로 수락할 수 있는 권장 사항도 제공 합니다. 권장 사항은 사용자의 로그인 활동을 기반으로 생성 됩니다.

1. 사용자를 하나 이상 선택한 다음 권장 구성 **적용**을 클릭 합니다.

    ![권장 구성 적용 단추를 표시 하는 액세스 검토 목록 열기](./media/perform-access-review/accept-recommendations-preview.png)

1. **제출** 을 클릭 하 여 권장 사항을 적용 합니다.

모든 사용자에 대 한 권장 사항을 적용 하려면 어떤 것도 선택 되지 않았는지 확인 하 고 위쪽 막대에서 **권장 구성 적용** 단추를 클릭 합니다.

>[!NOTE]
>권장 사항을 적용 하는 경우 이전 결정 사항은 변경 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)
