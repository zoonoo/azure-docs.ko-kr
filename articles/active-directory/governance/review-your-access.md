---
title: 액세스 검토에서 앱& 그룹에 대한 액세스 검토 - Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 또는 응용 프로그램에 대한 사용자 고유의 액세스를 검토하는 방법을 알아봅니다.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422409"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 또는 응용 프로그램에 대한 액세스 검토

Azure Active Directory(Azure AD)는 Azure AD 및 기타 Microsoft 온라인 서비스에서 Azure AD 액세스 검토라는 기능을 사용하여 기업이 그룹 또는 응용 프로그램에 대한 액세스를 관리하는 방법을 단순화합니다.

이 문서에서는 그룹 또는 응용 프로그램에 대한 사용자 고유의 액세스를 검토하는 방법을 설명합니다.

## <a name="open-the-access-review"></a>액세스 검토 열기

액세스 검토를 수행하는 첫 번째 단계는 액세스 검토를 찾아 여는 것입니다.

1. 액세스 검토를 요청하는 Microsoft의 전자 메일을 찾습니다. 다음은 그룹에 대한 액세스를 검토하는 예제 이메일입니다.

    ![그룹에 대한 액세스를 검토하기 위해 Microsoft의 전자 메일 예제](./media/review-your-access/access-review-email.png)

1. 액세스 **검토** 링크를 클릭하여 액세스 검토를 엽니다.

이메일이 없는 경우 다음 단계에 따라 보류 중인 액세스 리뷰를 찾을 수 있습니다.

1. 에서 MyApps 포털에 [https://myapps.microsoft.com](https://myapps.microsoft.com)로그인합니다.

    ![권한이 있는 MyApps 포털 목록 앱](./media/review-your-access/myapps-access-panel.png)

1. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 클릭하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

1. 페이지 의 오른쪽에 있는 액세스 **리뷰** 타일을 클릭하여 보류 중인 액세스 검토 목록을 확인합니다.

    타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

    ![앱 및 그룹에 대한 보류 중인 액세스 검토 목록](./media/review-your-access/access-reviews-list.png)

1. 수행하려는 액세스 **검토의** 검토 시작 링크를 클릭합니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 액세스 권한을 볼 수 있습니다.

1. 액세스를 검토하고 여전히 액세스가 필요한지 여부를 결정합니다.

    다른 사람에 대한 액세스를 검토하라는 요청이 있는 경우 페이지가 다르게 표시됩니다. 자세한 내용은 [그룹 또는 응용 프로그램에 대한 액세스 검토를](perform-access-review.md)참조하십시오.

    ![그룹에 대한 액세스가 필요한지 여부를 묻는 열기 액세스 검토](./media/review-your-access/perform-access-review.png)

1. **아니요를** 클릭하여 액세스를 유지하거나 **아니요를** 클릭하여 액세스를 제거합니다.

1. **예를**클릭하면 **이유** 상자에 자리 맞추기를 지정해야 할 수 있습니다.

    ![그룹에 대한 액세스가 필요한지 여부를 묻는 완료된 액세스 검토](./media/review-your-access/perform-access-review-submit.png)

1. **제출**을 클릭합니다.

    선택한 항목이 제출되고 MyApps 포털로 반환됩니다.

    응답을 변경하려면 액세스 리뷰 페이지를 다시 열고 응답을 업데이트합니다. 액세스 검토가 종료될 때까지 언제든지 응답을 변경할 수 있습니다.

    > [!NOTE]
    > 더 이상 액세스가 필요하지 않다고 표시되면 즉시 제거되지 않습니다. 검토가 종료되거나 관리자가 검토를 중지하면 제거됩니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램에 대한 액세스 검토 완료](complete-access-review.md)
