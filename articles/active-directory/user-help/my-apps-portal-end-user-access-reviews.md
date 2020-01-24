---
title: 앱 & 그룹에 대 한 조직 액세스 관리-Azure AD
description: 내 앱 포털에서 조직의 앱 및 그룹에 대 한 보안 액세스를 관리 하기 위해 액세스 검토를 수행 하는 방법을 알아봅니다.
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
ms.openlocfilehash: e739024686bdac497b9b7dd450c5ed46e3cf9a63
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705023"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>내 앱 포털에서 액세스 검토를 수행 합니다.

웹 기반 **My Apps** 포털을 통해 회사 또는 학교 계정을 사용하여 다양한 조직의 클라우드 기반 앱을 확인 및 시작하고, 일부 프로필 및 계정 정보를 업데이트하고, **그룹** 정보를 보고, 앱 및 그룹에 대한 **액세스 검토**를 수행할 수 있습니다. **My Apps** 포털에 액세스할 수 없는 경우 사용 권한에 대해 기술 지원팀에 문의해야 합니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>이 콘텐츠는 사용자를 위한 것입니다. 관리자인 경우 [애플리케이션 관리 설명서](https://docs.microsoft.com/azure/active-directory/manage-apps)에서 클라우드 기반 앱을 설정 및 관리하는 방법에 대한 더 자세한 정보를 확인할 수 있습니다.

## <a name="manage-access-reviews"></a>액세스 검토 관리

관리자가 사용자에 게 액세스 검토를 수행할 수 있는 권한을 부여 받은 경우 **내 앱** 포털 페이지의 **액세스 검토** 타일에서 그룹 또는 앱 액세스를 관리할 수 있습니다.

>[!Note]
>**액세스 검토** 타일이 표시 되지 않으면 액세스 검토를 수행할 수 있는 권한이 없거나 승인을 기다리는 보류 중인 검토가 없음을 의미 합니다. 타일에 대 한 액세스 권한이 있다고 생각 되 면 지원 센터에 문의 하세요.

### <a name="to-perform-your-access-reviews"></a>액세스 검토를 수행 하려면

1. 회사 또는 학교 계정에 로그인합니다.

2. 웹 브라우저를 열고 https://myapps.microsoft.com 로 이동 하거나 조직에서 제공 하는 링크를 사용 합니다. 예를 들어 https://myapps.microsoft.com/contoso.com 와 같은 조직에 대해 사용자 지정 된 페이지로 이동할 수 있습니다.

    조직에서 소유 하 고 사용할 수 있는 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

    ![내 앱 포털의 앱 페이지](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. **액세스 검토** 타일을 선택 하 여 승인을 기다리는 액세스 검토 목록을 표시 합니다.

    ![조직에 대 한 액세스 검토가 보류 중인 액세스 검토 페이지](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. **검토 시작** 을 선택 하 여 액세스 검토를 시작 합니다.

5. 액세스를 검토 하 고 여전히 필요한 지 여부를 확인 합니다.

    ![검토 세부 정보를 표시 하는 액세스 검토 페이지](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >관리자이 고 그룹 및 앱에 대 한 조직의 액세스를 검토할 수 있는 경우 다른 페이지가 표시 됩니다. 조직의 그룹 또는 앱을 검토 하는 방법에 대 한 자세한 내용은 [AZURE AD 액세스 검토에서 그룹 또는 응용 프로그램](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)에 대 한 액세스 검토를 참조 하세요.

6. 액세스를 유지 하려면 **예** 를 선택 하 고, 액세스를 제거 하려면 **아니요** 를 선택 합니다.

    **예**를 선택 하는 경우 **이유** 상자에 근거를 지정 해야 할 수 있습니다.

    ![예제 텍스트를 사용 하 여 이유 상자를 표시 하는 액세스 검토 페이지](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. **제출**을 선택합니다.

    액세스 검토가 완료 되 고 **내 앱** 포털로 돌아갑니다.

    >[!Note]
    >액세스 검토 기간이 종료 될 때까지 언제 든 지 액세스를 변경할 수 있습니다. 앱 또는 그룹에 대 한 액세스 권한을 제거 하는 경우 즉시 제거 되지 않습니다. 제거는 액세스 검토 기간이 끝나거나 관리자가 검토를 닫을 때 발생 합니다.

## <a name="next-steps"></a>다음 단계

- [내 앱 포털에서 앱에 액세스 하 고 앱을 사용](my-apps-portal-end-user-access.md)합니다.

- [프로필 정보를 변경](my-apps-portal-end-user-update-profile.md)합니다.

- [그룹 관련 정보를 보고 업데이트](my-apps-portal-end-user-groups.md)합니다.
