---
title: Manage your organization's access to apps & groups - Azure AD
description: Learn how to perform an access review to manage security access for your organization's apps and groups from the My Apps portal.
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
ms.openlocfilehash: 232f4b6fde84b7e1cd706b62e0ba9a0998b4171e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231832"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Perform an access review from the My Apps portal

웹 기반 **My Apps** 포털을 통해 회사 또는 학교 계정을 사용하여 다양한 조직의 클라우드 기반 앱을 확인 및 시작하고, 일부 프로필 및 계정 정보를 업데이트하고, **그룹** 정보를 보고, 앱 및 그룹에 대한 **액세스 검토**를 수행할 수 있습니다. **My Apps** 포털에 액세스할 수 없는 경우 사용 권한에 대해 기술 지원팀에 문의해야 합니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>이 콘텐츠는 사용자를 위한 것입니다. 관리자인 경우 [애플리케이션 관리 설명서](https://docs.microsoft.com/azure/active-directory/manage-apps)에서 클라우드 기반 앱을 설정 및 관리하는 방법에 대한 더 자세한 정보를 확인할 수 있습니다.

## <a name="manage-access-reviews"></a>액세스 검토 관리

If your administrator has given you permission to perform your own access reviews, you can manage your groups or apps access from the **Access reviews** tile on the **My Apps** portal page.

>[!Note]
>If you don't see the **Access reviews** tile it either means that you don't have permission to perform access reviews, or that you don't have any pending reviews waiting for your approval. If you think you should have access to the tile, contact your Help desk for assistance.

### <a name="to-perform-your-access-reviews"></a>To perform your access reviews

1. 회사 또는 학교 계정에 로그인합니다.

2. Open your web browser and go to https://myapps.microsoft.com, or use the link provided by your organization. For example, you might be directed to a customized page for your organization, such as https://myapps.microsoft.com/contoso.com.

    The **Apps** page appears, showing all the cloud-based apps owned by your organization and available for you to use.

    ![Apps page in the My Apps portal](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Select the **Access reviews** tile to see a list of access reviews waiting for your approval.

    ![Access reviews page with pending access reviews for the organization](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Select **Begin review** to start your access review.

5. Review your access and determine whether it's still necessary.

    ![Access review page, showing the review details](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >If you're an administrator, and allowed to review your organization's access to groups and apps, you'll see a different page. For more information about reviewing groups or apps for your organization, see [Review access to groups or applications in Azure AD Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Select **Yes** to keep your access or **No** to remove your access.

    If you select **Yes**, you might need to specify a justification in the **Reason** box.

    ![Access review page, showing the Reason box with sample text](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. **제출**을 선택합니다.

    Your access review is complete and you return to the **My Apps** portal.

    >[!Note]
    >You can change your access at any time until your access review period ends. If you remove your access to an app or group, it's not removed immediately. The removal happens when the access review period ends or when an administrator closes the review.

## <a name="next-steps"></a>다음 단계

- [Access and use apps on the My Apps portal](my-apps-portal-end-user-access.md).

- [Change your profile information](my-apps-portal-end-user-update-profile.md).

- [View and update your groups-related information](my-apps-portal-end-user-groups.md).
