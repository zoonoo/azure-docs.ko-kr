---
title: 액세스 검토에서 그룹 및 애플리케이션에 대한 액세스 검토 - Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 멤버의 액세스 또는 애플리케이션 액세스를 검토하는 방법을 알아봅니다.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 402fadfaa464c4cb13b3fa5ace22679b386140d0
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109787062"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 및 애플리케이션에 대한 액세스 검토

Azure AD(Azure Active Directory)는 Azure AD 액세스 검토라는 기능을 사용하여 기업에서 Azure AD 및 기타 Microsoft Online Services의 그룹 및 애플리케이션에 대한 액세스를 관리하는 방법을 간소화합니다. 이 문서에서는 지정된 검토자가 애플리케이션에 대한 액세스 권한이 있는 그룹 멤버 또는 사용자에 대한 액세스 검토를 수행하는 방법에 대해 설명합니다. 검토하려는 경우 패키지에 대한 액세스는 [Azure AD 권한 관리에서 액세스 패키지의 액세스 검토](entitlement-management-access-reviews-review-access.md)를 참조하세요.

## <a name="perform-access-review-using-my-apps"></a>My Apps로 액세스 검토 수행

알림 메일에서 또는 사이트로 직접 이동하여 액세스 검토 프로세스를 시작할 수 있습니다.

- **이메일**:

>[!IMPORTANT]
> 이메일을 받을 때 지연될 수 있으며, 경우에 따라 최대 24시간이 걸릴 수 있습니다. 수신 허용 목록에 추가azure-noreply@microsoft.com하여 모든 메일을 수신하고 있는지 확인합니다.

1. Microsoft에서 액세스를 검토하도록 요청하는 메일을 찾습니다. 그룹에 대한 액세스를 검토하는 예시 메일은 다음과 같습니다.

    ![그룹에 대한 액세스를 검토하도록 Microsoft에서 보낸 이메일의 예를 보여 주는 스크린샷](./media/perform-access-review/access-review-email.png)

1. **검토 시작** 링크를 클릭하여 액세스 검토를 엽니다.

- **이메일이 없는 경우** 다음 단계에 따라 보류 중인 액세스 검토를 찾을 수 있습니다.

    1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에서 My Apps 포털에 로그인합니다.

        ![권한이 있는 앱을 나열하는 내 앱 포털](./media/perform-access-review/myapps-access-panel.png)

    1. 페이지의 오른쪽 위 모서리에 위치한 이름과 기본 조직 옆, 사용자를 클릭합니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

    1. **액세스 검토** 배열을 클릭하여 보류 중인 액세스 검토 목록을 표시합니다.

        > [!NOTE]
        > **액세스 검토** 타일이 표시되지 않으면 해당 조직에 대해 수행할 액세스 검토가 없으며 이 시점에서 어떤 작업도 필요하지 않습니다.

        ![앱 및 그룹에 대한 보류 중인 액세스 검토 목록을 보여 주는 스크린샷](./media/perform-access-review/access-reviews-list.png)

    1. 수행하려는 액세스 검토의 **검토 시작** 링크를 클릭합니다.

액세스 검토를 열면 액세스를 검토해야 하는 사용자의 이름이 표시됩니다.

사용자 고유의 액세스 권한을 검토하기 위한 요청인 경우 페이지가 다르게 표시됩니다. 자세한 내용은 [그룹 또는 애플리케이션에 대한 직접 액세스 검토](review-your-access.md)를 참조하세요.

![열려 있는 액세스 검토 - 검토할 사용자가 나열됨](./media/perform-access-review/perform-access-review.png)

액세스를 승인하거나 거부할 방법은 두 가지가 있습니다.

- 각 사용자 요청에 대한 적절한 작업을 선택하여 하나 이상의 사용자에 대한 액세스를 승인하거나 거부할 수 있습니다.
- 시스템 권장 사항을 수락할 수 있습니다.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대한 액세스 승인 또는 거부

1. 사용자 목록을 검토하고 사용자의 지속적인 액세스를 승인 또는 거부할지 결정합니다.

    - 단일 사용자에 대한 액세스를 승인하거나 거부하려면 해당 행을 클릭하여 수행할 작업을 지정하는 창을 엽니다. 
    - 여러 사용자에 대한 액세스를 승인하거나 거부하려면 사용자 옆에 확인 표시를 추가하고 **X 사용자 검토** 단추를 클릭하여 수행할 작업을 지정하는 창을 엽니다.

1. **승인** 또는 **거부** 를 클릭합니다. 

    ![승인, 거부 및 알 수 없음 옵션이 포함된 작업 창](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > 확실하지 않은 경우 **알 수 없음** 을 클릭하면 됩니다. 그러면 사용자가 자신의 액세스를 유지하고 선택한 항목이 감사 로그에 기록됩니다.

1. 액세스 검토 관리자는 결정에 대한 **근거** 상자에 근거를 입력해야 할 수 있습니다. 근거가 필요하지 않은 경우라도 마찬가지입니다. 여전히 결정에 대한 근거를 제공할 수 있으며 포함하는 정보는 다른 검토자가 사용할 수 있습니다.

1. 수행할 작업을 지정했으면 **저장** 을 클릭합니다.

    >[!NOTE]
    > 액세스 검토가 끝나기 전에 언제든지 응답을 변경할 수 있습니다. 응답을 변경하려면 행을 선택하고 응답을 업데이트합니다. 예를 들어, 이전에 거부된 사용자를 승인하거나 이전에 승인된 사용자를 거부할 수 있습니다.

    >[!IMPORTANT]
    > - 사용자의 액세스가 거부된 뒤 즉시 제거되지 않습니다. 해당 설정은 검토 기간이 종료되거나 관리자가 [자동 적용](complete-access-review.md#apply-the-changes)을 사용하도록 설정한 경우 검토를 중지할 때 제거됩니다.
    > - 검토자가 여러 명인 경우 마지막에 제출된 응답이 기록됩니다. 관리자가 Alice와 Bob이라는 두 검토자를 지정하는 예를 살펴보겠습니다. Alice가 액세스 검토를 먼저 열고 사용자의 액세스 요청을 승인합니다. 검토 기간이 끝나기 전에 Bob은 액세스 검토를 열고 이전에 Alice가 승인한 동일한 요청에 대한 액세스를 거부합니다. 액세스를 거부한 마지막 결정이 기록되는 응답입니다.

### <a name="approve-or-deny-access-based-on-recommendations"></a>권장 사항에 따라 액세스 승인 또는 거부

액세스 검토를 더욱 쉽고 빠르게 수행할 수 있도록 한 번의 클릭으로 권장 사항을 수락할 수 있습니다. 권장 사항은 사용자의 로그인 활동을 기반으로 생성됩니다.

1. 페이지 하단의 파란색 표시줄에서 **권장 사항 수락** 을 클릭합니다.

    ![열려 있는 액세스 검토 목록을 보여 주는 스크린샷 - "권장 사항 동의" 단추가 선택됨](./media/perform-access-review/accept-recommendations.png)

    권장되는 작업 요약이 표시됩니다.

    ![권장되는 작업의 요약을 표시하는 창](./media/perform-access-review/accept-recommendations-summary.png)

1. **확인** 을 클릭하여 권장 사항을 적용합니다.

## <a name="perform-access-review-using-my-access-new"></a>내 액세스를 사용하여 액세스 검토 수행(신규)

다음과 같은 몇 가지 방법으로 내 액세스에서 업데이트된 사용자 인터페이스를 사용하여 새 검토자 환경에 액세스할 수 있습니다.

### <a name="my-apps-portal"></a>내 앱 포털

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에서 내 앱에 로그인합니다.

    ![권한이 있는 앱을 나열하는 내 앱 포털](./media/perform-access-review/myapps-access-panel.png)

2. **액세스 검토** 배열을 클릭하여 보류 중인 액세스 검토 목록을 표시합니다.

    > [!NOTE]
    > **액세스 검토** 타일이 표시되지 않으면 해당 조직에 대해 수행할 액세스 검토가 없으며 이 시점에서 어떤 작업도 필요하지 않습니다.

![미리 보기 중에 사용 가능한 새 환경 배너로 표시된 앱 및 그룹에 대한 보류 중인 액세스 검토 목록](./media/perform-access-review/banner.png)

3. 페이지 위쪽의 배너에서 **사용해 보세요!** 를 클릭합니다. 그러면 새 내 액세스 환경으로 이동합니다.
  
### <a name="email"></a>메일

  >[!IMPORTANT]
> 이메일을 받을 때 지연될 수 있으며, 경우에 따라 최대 24시간이 걸릴 수 있습니다. 수신 허용 목록에 추가azure-noreply@microsoft.com하여 모든 메일을 수신하고 있는지 확인합니다.

   1. Microsoft에서 액세스를 검토하도록 요청하는 메일을 찾습니다. 아래와 같은 이메일 메시지의 예를 볼 수 있습니다.

   ![그룹에 대한 액세스를 검토하도록 Microsoft에서 보낸 이메일의 예](./media/perform-access-review/access-review-email-preview.png)

   2. **검토 시작** 링크를 클릭하여 액세스 검토를 엽니다.

>[!NOTE]
>[검토 시작]을 클릭하여 **내 앱** 포털로 이동하면 위쪽의 **내 앱 포털** 이라는 섹션에 나열된 단계를 수행합니다.

### <a name="navigate-to-my-access-directly"></a>내 액세스로 직접 이동

브라우저를 사용하여 내 액세스를 열어 보류 중인 액세스 검토를 볼 수도 있습니다.

1. https://myaccess.microsoft.com/ 에서 내 액세스에 로그인합니다.

2. 왼쪽 사이드바의 메뉴에서 **액세스 검토** 를 선택하여 사용자에게 할당된 보류 중인 액세스 검토 목록을 표시합니다.

   ![메뉴의 액세스 검토](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대한 액세스 승인 또는 거부

[그룹 및 앱] 아래에서 [내 액세스]를 열면 다음 항목이 표시됩니다.

- **이름** 액세스 검토의 이름입니다.
- **기한** 검토 기한입니다. 이 날짜 이후에는 검토 중인 그룹 또는 앱에서 거부된 사용자가 제거될 수 있습니다.
- **리소스** 검토 중인 리소스의 이름입니다.
- **진행률** 이 액세스 검토의 총 사용자 수에 대해 검토된 사용자 수입니다.

시작하려면 액세스 검토 이름을 클릭합니다.

![앱 및 그룹에 대한 보류 중인 액세스 검토 목록](./media/perform-access-review/access-reviews-list-preview.png)

열리면 액세스 검토 범위에 있는 사용자의 목록이 표시됩니다. 사용자 고유의 액세스 권한을 검토하기 위한 요청인 경우 페이지가 다르게 표시됩니다. 자세한 내용은 [그룹 또는 애플리케이션에 대한 직접 액세스 검토](review-your-access.md)를 참조하세요.

액세스를 승인하거나 거부할 방법은 두 가지가 있습니다.

- 한 명 이상의 사용자에 대한 액세스를 수동으로 승인하거나 거부할 수 있습니다.
- 시스템 권장 사항을 수락할 수 있습니다.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>한 명 이상의 사용자에 대한 액세스를 수동으로 승인 또는 거부

1. 사용자 목록을 검토하고 사용자의 지속적인 액세스를 승인 또는 거부할지 결정합니다.
2. 이름 옆의 원을 클릭하여 한 명 이상의 사용자를 선택합니다.
3. 위쪽 막대에서 **승인** 또는 **거부** 를 선택합니다.
    - 확실하지 않은 경우 **알 수 없음** 을 클릭하면 됩니다. 사용자가 자신의 액세스를 유지하고 선택한 항목이 감사 로그에 기록됩니다. 사용자가 제공하는 모든 정보는 다른 검토자가 사용할 수 있다는 점에 유의해야 합니다. 이러한 검토자는 요청을 검토할 때 사용자의 주석을 읽고 고려할 수 있습니다.

    ![열려 있는 액세스 검토 - 검토가 필요한 사용자가 나열됨](./media/perform-access-review/user-list-preview.png)

4. 액세스 검토 관리자는 결정에 대한 **근거** 상자에 근거를 입력해야 할 수 있습니다. 근거가 필요하지 않은 경우라도 마찬가지입니다. 여전히 결정에 대한 근거를 제공할 수 있으며, 포함된 정보는 검토를 위해 다른 승인자가 사용할 수 있습니다.

5. **제출** 을 클릭합니다.
    - 액세스 검토가 끝날 때까지 언제든지 응답을 변경할 수 있습니다. 응답을 변경하려면 행을 선택하고 응답을 업데이트합니다. 예를 들어, 이전에 거부된 사용자를 승인하거나 이전에 승인된 사용자를 거부할 수 있습니다.

 >[!IMPORTANT]
 > - 사용자의 액세스가 거부된 뒤 즉시 제거되지 않습니다. 검토 기간이 종료되거나 관리자가 검토를 중지하면 제거됩니다. 
 > - 검토자가 여러 명인 경우 마지막에 제출된 응답이 기록됩니다. 관리자가 Alice와 Bob이라는 두 명의 검토자를 지정하는 예를 살펴보겠습니다. Alice가 액세스 검토를 먼저 열고 사용자의 액세스 요청을 승인합니다. 검토 기간이 끝나기 전에 Bob은 액세스 검토를 열고 이전에 Alice가 승인한 동일한 요청에 대한 액세스를 거부합니다. 액세스를 거부한 마지막 결정이 기록되는 응답입니다.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>권장 사항에 따라 액세스 승인 또는 거부

액세스 검토를 더욱 쉽고 빠르게 수행할 수 있도록 한 번의 클릭으로 권장 사항을 수락할 수 있습니다. 권장 사항은 사용자의 로그인 활동을 기반으로 생성됩니다.

1. 한 명 이상의 사용자를 선택한 다음, **권장 사항 동의** 를 클릭합니다.

    ![열려 있는 액세스 검토 - 권장 사항 동의 단추가 표시됨](./media/perform-access-review/accept-recommendations-preview.png)

1. **제출** 을 클릭하여 권장 사항에 동의합니다.

모든 사용자에 대한 권장 사항에 동의하려면 아무도 선택되지 않았는지 확인하고 위쪽 막대에서 **권장 사항 동의** 단추를 클릭합니다.

>[!NOTE]
>권장 사항에 동의하면 이전 결정이 변경되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)
