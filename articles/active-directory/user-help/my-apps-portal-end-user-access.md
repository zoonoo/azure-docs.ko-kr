---
title: 내 앱 포털에서 앱을 찾을 & 사용-Azure AD
description: 내 앱 포털을 찾은 다음 조직의 클라우드 기반 앱에 액세스 하는 방법을 알아봅니다.
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
ms.openlocfilehash: a5dae30c8b8f43602581b39005389211da83cb9e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375896"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>내 앱 포털에서 로그인 하 고 앱 시작

웹 기반 **내 앱** 포털에서 회사 또는 학교 계정을 사용 하 여 다음을 수행할 수 있습니다.

- 조직의 클라우드 기반 앱을 많이 보고 시작 합니다.
- 프로필 및 계정 정보 업데이트
- **그룹** 정보 보기
- 앱 및 그룹에 대 한 **액세스 검토** 수행

**My Apps** 포털에 액세스할 수 없는 경우 사용 권한에 대해 기술 지원팀에 문의하세요.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> 이 콘텐츠는 내 앱 사용자를 대상으로 합니다. 관리자인 경우 [애플리케이션 관리 설명서](https://docs.microsoft.com/azure/active-directory/manage-apps)에서 클라우드 기반 앱을 설정 및 관리하는 방법에 대한 더 자세한 정보를 확인할 수 있습니다.

## <a name="supported-browsers"></a>지원되는 브라우저

다음 웹 브라우저 중 하나에서 **내 앱** 포털에 액세스할 수 있습니다.

- Google Chrome

- Mozilla Firefox, 버전 26.0 이상

- Microsoft Edge

- Internet Explorer 버전 11 (제한 된 지원)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장을 다운로드 하 여 설치 합니다.

메시지가 표시 되 면 내 앱 보안 로그인 확장을 다운로드 하 여 설치 합니다. 이 확장은 Single Sign-On 프로세스를 사용 해야 하는 조직의 클라우드 앱을 시작 하는 데 도움이 됩니다. 조직에서 Single Sign-On를 이미 설정한 경우에는 확장이 자동으로 설치 되며이 섹션을 건너뛸 수 있습니다.

이 확장을 통해 다음과 같은 작업을 할 수 있습니다.

- 로그인 페이지에서 앱에 직접 로그인 합니다.

- **빠른 검색** 기능을 사용 하 여 앱을 시작 합니다.

- **최근** 사용한 섹션에서 사용한 마지막 앱을 확인 하세요.

- [응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)를 사용 하 여 원격으로 내부 회사 url을 사용 합니다.

### <a name="to-download-and-install-the-extension"></a>확장을 다운로드 하 고 설치 하려면

사용 중인 브라우저에 따라 확장을 다운로드 하 여 설치 합니다.

- **Google Chrome.** Chrome 웹 스토어에서 [내 앱 보안 로그인 확장](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) 기능으로 이동한 후 **chrome에 추가**를 선택 합니다.

- **Mozilla Firefox** **Firefox 추가 기능** 페이지에서 [내 앱 보안 로그인 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/) 기능으로 이동한 후 **firefox에 추가**를 선택 합니다.

- **Microsoft Edge** Microsoft Store에서 [내 앱 보안 로그인 확장](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) 기능으로 이동한 후 **가져오기**를 선택 합니다.

사용자가 로그인 하 고 확장을 사용자 지정할 수 있도록 **주소** 표시줄의 오른쪽에 아이콘이 추가 됩니다.

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>확장을 사용 하 여 내 앱 포털을 변경 하려면

**최근에 사용한** 섹션에서 볼 앱 수를 선택 하 고 조직의 내부 url을 리디렉션할 수 있도록 허용할지 여부를 결정할 수 있습니다.

1. 새 **내 앱 보안 로그인 확장** 아이콘 ![확장 아이콘을 선택 하 고, **주소** 표시줄의 오른쪽에](media/my-apps-portal/my-apps-portal-extension-icon.png) 한 다음 **로그인을 선택 하 여 시작**합니다.

2. **설정 아이콘 ![** 설정 아이콘](media/my-apps-portal/my-apps-portal-extension-settings-icon.png)을 마우스 오른쪽 단추로 클릭 한 다음 **설정**을 선택 합니다.

3. **설정** 상자에서 포털에 표시 하려는 최근 앱 수를 선택 하 고, 원격으로 사용할 수 있도록 조직의 내부 url을 리디렉션할 수 있도록 허용할지 여부를 선택 합니다.

    ![사용 가능한 사용자 지정을 보여 주는 확장의 설정 페이지](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>장치에서 내 앱 포털 액세스 및 사용

컴퓨터, Intune에서 관리 되는 브라우저 또는 iOS 또는 Android 모바일 장치에서 내 앱 포털에 액세스 하 여 사용할 수 있습니다.

![내 앱 포털의 앱 페이지](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>컴퓨터에서 My Apps 포털 액세스 및 사용

조직의 클라우드 기반 앱에 액세스 하 여 사용할 수 있는 권한이 있는 경우 **내 앱** 포털을 통해 가져올 수 있습니다.

1. 컴퓨터의 회사 또는 학교 계정에 로그인 합니다.

2. 지원 되는 웹 브라우저에서를 열고 https://myapps.microsoft.com로 이동 하거나 `https://myapps.microsoft.com/contoso.com`와 같이 사용자 지정 된 페이지로 이동 하는 경우 조직에서 제공 하는 링크를 사용 합니다.

    **앱** 페이지가 나타나고 사용자가 사용할 수 있는 조직에서 소유 하는 모든 클라우드 기반 앱이 표시 됩니다.

3. **앱** 페이지에서 사용 하 여 시작 하려는 앱을 선택 합니다.

    앱에 대 한 새 페이지가 열립니다. 여기에서 로그인 하거나 (필요한 경우) 앱 사용을 시작할 수 있습니다.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Intune 관리 브라우저에서 My Apps 포털 액세스 및 사용

IOS 및 Android 장치의 Intune Managed Browser에서 조직의 앱을 보고 사용 합니다.

1. 모바일 장치에서 [Apple 앱 스토어](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) 및 [Google Play 스토어](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)에서 Intune Managed Browser 앱을 다운로드 하 여 설치 합니다.

2. Intune Managed Browser 앱을 열거나 https://myapps.microsoft.com로 이동 하거나, https://myapps.microsoft.com/contoso.com와 같이 사용자 지정 된 페이지로 이동 하는 경우 조직에서 제공 하는 링크를 사용 합니다.

    조직에서 소유 하 고 사용할 수 있는 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

3. **앱** 페이지에서 사용 하 여 시작 하려는 앱을 선택 합니다.

    앱에 대 한 새 페이지가 열립니다. 여기에서 로그인 하거나 (필요한 경우) 앱 사용을 시작할 수 있습니다.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>IOS 장치에서 내 앱 포털 액세스 및 사용

IOS 버전 7 이상을 실행 하는 iPhone 또는 iPad 장치에서 **내 앱** 포털을 보고 사용 합니다. IOS 장치에서 조직의 앱에 액세스 하기 위해 [My apps 모바일 앱](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) 을 설치할 수도 있습니다.

1. 모바일 장치에서 Safari와 같은 웹 브라우저 앱을 시작 합니다.

2. https://myapps.microsoft.com로 이동 하거나 https://myapps.microsoft.com/contoso.com와 같이 사용자 지정 된 페이지로 이동 하는 경우 조직에서 제공 하는 링크를 사용 합니다.

    **앱** 페이지가 나타나고 사용자가 사용할 수 있는 조직에서 소유 하는 모든 클라우드 기반 앱이 표시 됩니다.

3. **앱** 페이지에서 사용 하 여 시작 하려는 앱을 선택 합니다.

    앱에 대 한 새 페이지가 열립니다. 여기에서 로그인 하거나 (필요한 경우) 앱 사용을 시작할 수 있습니다.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Android 장치에서 My Apps 포털 액세스 및 사용

Android 장치에서 **내 앱** 포털을 보고 사용 합니다.

1. 모바일 장치에서 Google Chrome 등의 웹 브라우저 앱을 시작 합니다.

2. https://myapps.microsoft.com로 이동 하거나 https://myapps.microsoft.com/contoso.com와 같이 사용자 지정 된 페이지로 이동 하는 경우 조직에서 제공 하는 링크를 사용 합니다.

    조직에서 소유 하 고 사용할 수 있는 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

3. **앱** 페이지에서 사용 하 여 시작 하려는 앱을 선택 합니다.

    앱에 대 한 새 페이지가 열립니다. 여기에서 로그인 하거나 (필요한 경우) 앱 사용을 시작할 수 있습니다.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>내 앱 포털에 새 앱 추가

관리자가 사용자에 게 권한을 부여 하면 **앱** 페이지에 새 앱을 추가할 수 있습니다.

1. **앱** 페이지에서 다음 중 하나를 수행 합니다.
    - 원래 내 앱 환경을 사용할 경우 표시 된 것 처럼 **앱 추가** 를 선택 합니다.

      ![앱 추가 페이지, 내 앱 포털](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - 업데이트 된 내 앱 환경에 있는 경우 **셀프 서비스 앱 추가**를 선택 합니다.

       ![앱 추가 페이지의 My Apps 포털 myapplications.microsoft.com](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. 제공 된 목록에서 추가 하려는 앱을 선택 하 고 **추가**를 선택 합니다.

3. **앱이 앱 페이지의** 목록에 추가 됩니다.

    일부 앱은 추가 하기 전에 관리자 승인이 필요할 수 있습니다. 이 경우 관리자가 앱을 승인할 때까지 앱이 **앱 페이지에** 추가 되지 않습니다.

## <a name="start-a-cloud-based-app"></a>클라우드 기반 앱 시작

**내 앱** 포털에서 사용할 수 있는 클라우드 기반 앱을 시작할 수 있습니다. 사용 권한이 있는 앱만 볼 수 있습니다.

- **앱** 페이지에서 사용 하 여 시작 하려는 앱을 선택 합니다.

    앱에 대 한 새 페이지가 열립니다. 여기에서 로그인 하거나 (필요한 경우) 앱 사용을 시작할 수 있습니다.

## <a name="activities-in-the-my-apps-portal"></a>내 앱 포털의 작업

**내 앱** 포털을 가져온 후 다음을 수행할 수 있습니다.

- 조직에서 만들고 제공하는 다양한 범주로 앱을 구성합니다. 자세한 내용은 [내 앱 포털에서 컬렉션 액세스 및 사용](my-applications-portal-workspaces.md)을 참조하세요.

- 애플리케이션에 부여된 권한을 검토, 업데이트 또는 취소 합니다. 자세한 내용은 [내 앱 포털에서 애플리케이션 사용 권한 편집 또는 해지](my-applications-portal-permissions-saved-accounts.md)를 참조하세요.

>[!Note]
>사용 가능한 컬렉션이나 범주가 표시되지 않으면 관리자가 설정하지 않았거나 사용자와 공유하지 않은 것입니다. 공유 작업 영역을 보기 위한 추가 지원이나 권한을 조직의 기술 지원 팀에 요청하세요.

## <a name="next-steps"></a>다음 단계

**앱** 페이지로 이동 하 고 나면 다음을 수행할 수 있습니다.

- [프로필 정보 변경](my-apps-portal-end-user-update-profile.md)

- [그룹 관련 정보 보기 및 업데이트](my-apps-portal-end-user-groups.md)

- [내 액세스 검토 수행](my-apps-portal-end-user-access-reviews.md)
