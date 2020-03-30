---
title: 앱 & 그룹에 대한 조직의 액세스 관리 - Azure AD
description: 내 앱 포털에서 조직의 앱 및 그룹에 대한 보안 액세스를 관리하기 위해 액세스 검토를 수행하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062384"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>내 앱 포털에서 액세스 검토 수행

웹 기반 **내 앱** 포털에서 회사 또는 학교 계정을 사용하여 앱 및 그룹에 대한 액세스 검토를 수행할 수 있습니다. 액세스 검토를 통해 오래된 액세스 또는 변경 된 액세스 요구 사항을 관리하고 검토 및 업데이트되도록 할 수 있습니다.

**My Apps** 포털에 액세스할 수 없는 경우 사용 권한에 대해 기술 지원팀에 문의하세요.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>이 콘텐츠는 **내 앱** 사용자를 위한 것입니다. 관리자인 경우 [애플리케이션 관리 설명서](https://docs.microsoft.com/azure/active-directory/manage-apps)에서 클라우드 기반 앱을 설정 및 관리하는 방법에 대한 더 자세한 정보를 확인할 수 있습니다.

## <a name="manage-access-reviews"></a>액세스 검토 관리

관리자가 사용자 고유의 액세스 검토를 수행할 수 있는 권한을 부여한 경우 **내 앱** 포털 페이지의 **Access 리뷰** 타일에서 그룹 또는 앱 액세스를 관리할 수 있습니다.

>[!Note]
>**액세스 리뷰** 타일이 표시되지 않으면 액세스 검토를 수행할 수 있는 권한이 없거나 승인을 기다리는 보류 중인 리뷰가 없다는 의미입니다. 타일에 액세스할 수 있어야 한다고 생각되면 헬프데스크에 문의하여 도움을 받으십시오.

## <a name="to-perform-your-access-reviews"></a>액세스 검토를 수행하려면

1. 회사 또는 학교 계정에 로그인합니다.

2. 웹 브라우저를 열고 https://myapps.microsoft.com로 이동하거나 조직에서 제공하는 링크를 사용합니다. 예를 https://myapps.microsoft.com/contoso.com들어.

    조직에서 소유하고 사용할 수 있는 모든 클라우드 기반 앱을 표시하는 **앱** 페이지가 나타납니다.

    ![내 앱 포털의 앱 페이지](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 액세스 **리뷰** 타일을 선택하여 승인을 기다리는 액세스 리뷰 목록을 확인합니다.

    ![조직에 대한 보류 중인 액세스 검토가 있는 검토 페이지에 액세스](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 액세스 검토를 시작하려면 **검토 시작을** 선택합니다.

5. 액세스를 검토하고 여전히 필요한지 여부를 확인합니다.

    ![검토 페이지에 액세스하여 검토 세부 정보를 표시합니다.](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >관리자이고 조직그룹 및 앱에 대한 조직의 액세스를 검토할 수 있는 경우 다른 페이지가 표시됩니다. 조직의 그룹 또는 앱 검토에 대한 자세한 내용은 [Azure AD Access 검토에서 그룹 또는 응용 프로그램에 대한 검토 액세스를 참조합니다.](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)

6. 액세스를 유지하려면 **예를** 선택하거나 **아니요를** 선택하여 액세스를 제거합니다.

    **예를**선택하면 **이유** 상자에 자리맞추기를 지정해야 할 수 있습니다.

    ![샘플 텍스트가 있는 이유 상자를 표시하는 액세스 검토 페이지](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. **제출**을 선택합니다.

    액세스 검토가 완료되고 내 **앱** 포털로 돌아갑니다.

    >[!Note]
    >액세스 검토 기간이 끝날 때까지 언제든지 액세스를 변경할 수 있습니다. 앱 또는 그룹에 대한 액세스 권한을 삭제하면 앱이 즉시 삭제되지 않습니다. 제거는 액세스 검토 기간이 끝나거나 관리자가 검토를 닫을 때 발생합니다.

## <a name="next-steps"></a>다음 단계

- [내 앱 포털에서 앱 액세스 및 사용](my-apps-portal-end-user-access.md)
- [프로필 정보 변경](my-apps-portal-end-user-update-profile.md)
- [그룹 관련 정보 보기 및 업데이트](my-apps-portal-end-user-groups.md)
