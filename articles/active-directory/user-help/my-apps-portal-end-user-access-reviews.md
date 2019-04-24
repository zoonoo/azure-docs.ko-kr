---
title: My Apps 포털-Azure Active Directory에서에서 액세스 검토 수행 | Microsoft Docs
description: 보기 및 조직의 앱 및 그룹에 대 한 보안 액세스를 관리 하는 방법에 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482883"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>My Apps 포털에서 액세스 검토 수행
웹 기반 **My Apps** 포털을 통해 회사 또는 학교 계정을 사용하여 다양한 조직의 클라우드 기반 앱을 확인 및 시작하고, 일부 프로필 및 계정 정보를 업데이트하고, **그룹** 정보를 보고, 앱 및 그룹에 대한 **액세스 검토**를 수행할 수 있습니다. **My Apps** 포털에 액세스할 수 없는 경우 사용 권한에 대해 기술 지원팀에 문의해야 합니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>이 콘텐츠는 사용자를 위한 것입니다. 관리자인 경우 [애플리케이션 관리 설명서](https://docs.microsoft.com/azure/active-directory/manage-apps)에서 클라우드 기반 앱을 설정 및 관리하는 방법에 대한 더 자세한 정보를 확인할 수 있습니다.

## <a name="manage-access-reviews"></a>액세스 검토 관리
관리자가 자신의 액세스 검토를 수행할 수 있는 권한을 제공한, 그룹 또는 앱 액세스를 관리할 수 있습니다는 **액세스 검토** 타일에 **My Apps** 포털 페이지입니다.

>[!Note]
>표시 되지 않는 경우는 **액세스 검토** , 액세스 검토를 수행할 수 있는 권한이 없는 또는 없는지 검토 승인 될 때까지 기다리는 보류 중인 의미 중 하나를 타일입니다. 타일에 액세스할 수 있어야 하는 것이 생각을 하는 경우 도움이 필요 하면 기술 지원팀에 문의 합니다.

### <a name="to-perform-your-access-reviews"></a>사용자 액세스 검토를 수행 하려면

1.  회사 또는 학교 계정에 로그인합니다.

2.  웹 브라우저를 열고 이동 https://myapps.microsoft.com, 하거나 조직에서 제공 된 링크를 사용 합니다. 예를 들어, 있습니다 수 페이지로 이동 하는 사용자 지정 조직에 대 한 같은 https://myapps.microsoft.com/contoso.com합니다.

    합니다 **앱** 조직에서 소유 하 고 사용 하 여 사용할 수 있습니다 모든 클라우드 기반 앱을 보여 주는 페이지가 나타납니다.

    ![My Apps 포털의 앱 페이지](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 선택 된 **액세스 검토** 액세스 목록을 보려면 타일 검토 승인 대기 합니다.

    ![액세스 검토를 조직에 대 한 보류 중인 사용 하 여 액세스 검토 페이지](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 선택 **검토 시작** 액세스 검토를 시작 합니다.

5. 액세스를 검토 하 고 여전히 필요한 인지 여부를 결정 합니다.

    ![액세스 검토 페이지에서 검토 세부 정보를 표시 합니다.](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >관리자는 그룹 및 앱에 대 한 조직의 액세스를 검토 하도록 허용 하는 경우 다른 페이지를 표시 됩니다. 그룹 또는 조직에 대 한 앱을 검토 하는 방법에 대 한 자세한 내용은 참조 하세요. [그룹 또는 응용 프로그램에서 Azure AD 액세스 검토에 대 한 액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)합니다.

6. 선택 **예** 사용자 액세스 권한을 유지 또는 **No** 하 게 액세스 권한을 제거 합니다.

    선택 하는 경우 **예**에서 맞춤을 지정 해야 합니다 **이유** 상자입니다.

    ![액세스 검토 페이지에서 샘플 텍스트를 사용 하 여 이유 상자를 보여 주는](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. **제출**을 선택합니다.

    액세스 검토 완료 되 고 돌아갑니다 합니다 **My Apps** 포털입니다.

    >[!Note]
    >언제 든 지 액세스 검토 기간이 종료 될 때까지 사용자의 액세스를 변경할 수 있습니다. 앱 또는 그룹에 대 한 액세스를 제거 하면 즉시 제거 되지 않습니다. 제거는 액세스 검토 기간이 종료 또는 관리자가 검토를 닫을 때 발생 합니다. 

## <a name="next-steps"></a>다음 단계

- [액세스 하 고 My Apps 포털에서 앱 사용](my-apps-portal-end-user-access.md)합니다.

- [프로필 정보 변경](my-apps-portal-end-user-update-profile.md)합니다.

- [보기 및 사용자 그룹 관련 정보를 업데이트](my-apps-portal-end-user-groups.md)합니다.