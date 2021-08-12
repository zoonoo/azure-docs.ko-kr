---
title: 액세스 검토에서 그룹 및 앱에 대한 액세스 검토 - Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 또는 애플리케이션에 대한 자신의 액세스 권한을 검토하는 방법을 알아봅니다.
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
ms.openlocfilehash: 5532ec5cb0f619199749621d71f7d2165dfe2776
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109787386"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 또는 애플리케이션에 대한 자신의 액세스 권한 검토

Azure AD(Azure Active Directory)는 Azure AD 액세스 검토라는 기능을 사용하여 기업에서 Azure AD 및 기타 Microsoft Online Services의 그룹 또는 애플리케이션에 대한 액세스를 관리하는 방법을 간소화합니다.

이 문서에서는 그룹 또는 애플리케이션에 대한 자신의 액세스 권한을 검토하는 방법을 설명합니다.

## <a name="review-your-access-using-my-apps"></a>내 앱을 사용하여 내 액세스 권한 검토

액세스 검토를 수행하는 첫 번째 단계는 액세스 검토를 찾아서 여는 것입니다.

>[!IMPORTANT]
> 이메일을 받을 때 지연될 수 있으며, 경우에 따라 최대 24시간이 걸릴 수 있습니다. 수신 허용 목록에 추가azure-noreply@microsoft.com하여 모든 메일을 수신하고 있는지 확인합니다.

1. 액세스 검토를 요청하는 Microsoft의 이메일을 찾습니다. 다음은 그룹에 대한 내 액세스 권한을 검토하라는 예시 이메일입니다.

    ![그룹에 대한 내 액세스 권한을 검토하라고 Microsoft에서 보낸 이메일 예시](./media/review-your-access/access-review-email.png)

1. **액세스 검토** 링크를 클릭하여 액세스 검토를 엽니다.

이메일이 없는 경우 다음 단계에 따라 보류 중인 액세스 권한 검토를 찾을 수 있습니다.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에서 My Apps 포털에 로그인합니다.

    ![권한이 있는 앱을 나열하는 내 앱 포털](./media/review-your-access/myapps-access-panel.png)

1. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 클릭하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

1. 페이지의 오른쪽에서 **액세스 검토** 타일을 클릭하면 보류 중인 액세스 검토 목록이 표시됩니다.

    타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

    ![내 앱 및 그룹에 대한 보류 중인 액세스 검토 목록](./media/review-your-access/access-reviews-list.png)

1. 수행하려는 액세스 검토의 **검토 시작** 링크를 클릭합니다.

### <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 내 액세스 권한을 볼 수 있습니다.

1. 액세스 권한을 검토하고 여전히 액세스 권한이 필요한지 여부를 결정합니다.

    다른 사람의 액세스 권한을 검토하라는 요청이면 페이지가 다르게 표시됩니다. 자세한 내용은 [그룹 또는 애플리케이션에 대한 액세스 검토](perform-access-review.md)를 참조하세요.

    ![그룹에 대한 액세스 권한이 아직 필요한지 여부를 묻는 오픈 액세스 검토를 보여주는 스크린샷](./media/review-your-access/perform-access-review.png)

1. 액세스를 유지하려면 **예** 를 선택하고 액세스를 제거하려면 **아니요** 를 선택합니다.

1. **예** 를 클릭하는 경우 **이유** 상자에 근거를 지정해야 할 수 있습니다.

    ![그룹에 대한 액세스 권한이 필요한지 여부를 묻고 "예"가 선택된 상태에서 완료된 액세스 검토를 보여주는 스크린샷](./media/review-your-access/perform-access-review-submit.png)

1. **제출** 을 클릭합니다.

    선택 항목이 제출되고 내 앱 포털로 돌아갑니다.

    응답을 변경하려면 액세스 검토 페이지를 다시 열고 응답을 업데이트합니다. 액세스 검토가 끝날 때까지 언제든지 응답을 변경할 수 있습니다.

    > [!NOTE]
    > 더 이상 액세스할 필요가 없다고 표시한 경우, 즉시 제거되지 않습니다. 검토가 종료되거나 관리자가 검토를 중지하면 제거됩니다.

## <a name="review-your-own-access-using-my-access-new"></a>내 액세스(신규)를 사용하여 내 액세스 권한 검토

다음과 같은 몇 가지 방법으로 내 액세스에서 업데이트된 사용자 인터페이스를 사용하여 새 환경에 액세스할 수 있습니다.

### <a name="my-apps-portal"></a>내 앱 포털

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에서 My Apps 포털에 로그인합니다.

    ![권한이 있는 앱을 나열하는 내 앱 포털](./media/review-your-access/myapps-access-panel.png)

2. **액세스 검토** 배열을 클릭하여 보류 중인 액세스 검토 목록을 표시합니다.

    > [!NOTE]
    > **액세스 검토** 타일이 표시되지 않으면 해당 조직에 대해 수행할 액세스 검토가 없으며 이 시점에서 어떤 작업도 필요하지 않습니다.

3. 페이지 위쪽의 배너에서 **사용해 보세요!** 를 클릭하고 새 내 액세스 환경으로 이동합니다.

    ![미리 보기 중에 사용 가능한 새 환경 배너로 표시된 앱 및 그룹에 대한 보류 중인 액세스 검토 목록](./media/review-your-access/banner-your-access.png)

4. **액세스 검토 수행** 섹션에서 계속 진행합니다.

### <a name="email"></a>메일

>[!IMPORTANT]
> 이메일을 받을 때 지연될 수 있으며, 경우에 따라 최대 24시간이 걸릴 수 있습니다. 수신 허용 목록에 추가azure-noreply@microsoft.com하여 모든 메일을 수신하고 있는지 확인합니다.

1. Microsoft에서 액세스를 검토하도록 요청하는 메일을 찾습니다. 아래와 같은 이메일 메시지의 예를 볼 수 있습니다.

 ![그룹에 대한 액세스를 검토하도록 Microsoft에서 보낸 이메일의 예](./media/review-your-access/access-review-email-preview.png)

2. **액세스 검토** 링크를 클릭하여 액세스 검토를 엽니다.

3. **액세스 검토 수행** 섹션에서 계속 진행합니다.

>[!NOTE]
>[검토 시작]을 클릭하여 **내 앱** 포털로 이동하면 위쪽의 **내 앱 포털** 이라는 섹션에 나열된 단계를 수행합니다.

### <a name="directly-at-my-access"></a>내 액세스에서 직접

브라우저를 사용하여 내 액세스를 열어 보류 중인 액세스 검토를 볼 수도 있습니다.

1. https://myaccess.microsoft.com/ 에서 내 액세스에 로그인합니다.

2. 왼쪽 사이드바의 메뉴에서 **액세스 검토** 를 선택하여 사용자에게 할당된 보류 중인 액세스 검토 목록을 표시합니다.

   ![메뉴의 액세스 검토](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>액세스 검토 수행

1. 그룹 및 앱 아래에서 다음을 볼 수 있습니다.
    
    - **이름** 액세스 검토의 이름입니다.
    - **기한** 검토 기한입니다. 이 날짜 이후에는 검토 중인 그룹 또는 앱에서 거부된 사용자가 제거될 수 있습니다.
    - **리소스** 검토 중인 리소스의 이름입니다.
    - **진행률** 이 액세스 검토의 총 사용자 수에 대해 검토된 사용자 수입니다.
    
2. 시작하려면 액세스 검토 이름을 클릭합니다.

   ![앱 및 그룹에 대한 보류 중인 액세스 검토 목록](./media/review-your-access/access-reviews-list-preview.png)

3. 액세스 권한을 검토하고 여전히 액세스 권한이 필요한지 여부를 결정합니다.

    다른 사람의 액세스 권한을 검토하라는 요청이면 페이지가 다르게 표시됩니다. 자세한 내용은 [그룹 또는 애플리케이션에 대한 액세스 검토](perform-access-review.md)를 참조하세요.

    ![그룹에 대한 액세스가 필요한지 여부를 묻는 액세스 검토 열기](./media/review-your-access/review-access-preview.png)

1. 액세스를 유지하려면 **예** 를 선택하고 액세스를 제거하려면 **아니요** 를 선택합니다.

1. **예** 를 클릭하는 경우 **이유** 상자에 근거를 지정해야 할 수 있습니다.

    ![그룹에 대한 액세스가 필요한지 여부를 묻는 액세스 검토 완료](./media/review-your-access/review-access-yes-preview.png)

1. **제출** 을 클릭합니다.

    선택 항목이 제출되고 내 액세스 페이지로 돌아갑니다.

    응답을 변경하려면 액세스 검토 페이지를 다시 열고 응답을 업데이트합니다. 액세스 검토가 끝날 때까지 언제든지 응답을 변경할 수 있습니다.

    > [!NOTE]
    > 더 이상 액세스할 필요가 없다고 표시한 경우, 즉시 제거되지 않습니다. 검토가 종료되거나 관리자가 검토를 중지하면 제거됩니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)
