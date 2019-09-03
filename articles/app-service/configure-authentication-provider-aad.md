---
title: Azure Active Directory 인증 구성 - Azure App Service
description: App Service 앱에 대 한 Azure Active Directory 인증을 구성 하는 방법을 알아봅니다.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8b4b6549f9553773cc44c311f49befbb3eec9dc9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233104"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Azure Active Directory 로그인을 사용하도록 App Service 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> 지금은 AAD V2 (MSAL 포함)가 Azure App Service 및 Azure Functions에 대해 지원 되지 않습니다.
>

이 문서에서는 Azure Active Directory을 인증 공급자로 사용 하도록 Azure App Service를 구성 하는 방법을 보여 줍니다.

자체 등록을 사용 하 여 각 App Service 앱을 구성 하는 것이 좋습니다. 따라서 고유한 권한 및 동의가 있습니다. 또한 별도의 배포 슬롯에 대해 별도의 앱 등록을 사용 하는 것이 좋습니다. 이렇게 하면 테스트 중인 새 코드의 문제가 프로덕션에 영향을 주지 않도록 환경 간의 사용 권한 공유가 방지 됩니다.

## <a name="express"> </a>기본 설정을 사용하여 구성

1. [Azure Portal]에서 App Service 앱으로 이동합니다. 왼쪽 탐색 영역에서 **인증/권한 부여**를 선택합니다.
2. **인증/권한 부여**가 사용되도록 설정되지 않은 경우 **설정**을 선택합니다.
3. **Azure Active Directory**를 선택한 다음 **관리 모드**에서 **Express**를 선택합니다.
4. **확인**을 선택하여 Azure Active Directory에 App Service 앱을 등록합니다. 이렇게 하면 새 앱이 등록됩니다. 기존 앱 등록을 대신 선택하려는 경우 **기존 앱 선택** 을 클릭한 다음 테넌트 내에서 이전에 만든 앱 등록 이름을 검색합니다. 앱 등록을 클릭하여 선택하고 **확인**을 클릭합니다. Azure Active Directory 설정 페이지에서 **확인**을 클릭합니다.
기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.
5. 필드 앱에 대 한 액세스를 Azure Active Directory 인증 된 사용자로만 제한 하려면 **요청이 인증 되지 않은 경우** Azure Active Directory를 사용 하 **여 로그인**하도록 작업을 설정 합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Azure Active Directory로 리디렉션되어야 합니다.

    > [!NOTE]
    > 이러한 방식으로 액세스를 제한 하는 것은 앱에 대 한 모든 호출에 적용 됩니다 .이는 여러 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지를 사용 하는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 [여기](overview-authentication-authorization.md#authentication-flow)에 설명 된 대로 **익명 요청 허용 (작업 없음)** 을 사용 하는 것이 좋습니다. 앱은 수동으로 로그인을 시작 합니다.
6. **Save**을 클릭합니다.

## <a name="advanced"> </a>고급 설정을 사용하여 구성

사용 하려는 Azure Active Directory 테 넌 트가 Azure에 로그인 하는 테 넌 트와 다른 경우 구성 설정을 수동으로 제공할 수도 있습니다. 구성을 완료하려면 먼저 Azure Active Directory에 등록을 만든 다음 App Service에 등록 세부 정보 중 일부를 제공해야 합니다.

### <a name="register"> </a>App Service 앱에 대 한 Azure AD에서 앱 등록 만들기

앱 등록을 수동으로 만들 때 나중에 App Service 앱을 구성 하는 데 필요한 세 가지 정보, 즉 클라이언트 ID, 테 넌 트 ID 및 클라이언트 암호와 응용 프로그램 ID URI를 선택할 수 있습니다.

1. [Azure Portal]에서 App Service 앱으로 이동 하 여 앱의 **URL**을 확인 합니다. Azure Active Directory 앱 등록을 구성 하는 데 사용 합니다.
1. [Azure Portal]의 왼쪽 메뉴에서 **Active Directory** > **앱 등록** > **새 등록**을 선택 합니다. 
1. **응용 프로그램 등록** 페이지에서 앱 등록에 대 한 **이름을** 입력 합니다.
1. **리디렉션 URI**에서 **웹** 을 선택 하 고 App Service 앱의 URL을 입력 한 후 경로 `/.auth/login/aad/callback`를 추가 합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback` )을 입력합니다. 그런 다음 **만들기**를 선택합니다.
1. 앱 등록을 만든 후 나중에 **응용 프로그램 (클라이언트) id** 와 **디렉터리 (테 넌 트) id** 를 복사 합니다.
1. **브랜딩**을 선택 합니다. **홈 페이지 url**에서 App Service 앱의 url을 입력 하 고 **저장**을 선택 합니다.
1. **API** > **집합**표시를 선택 합니다. App Service 앱의 URL에 붙여 넣고 **저장**을 선택 합니다.

    > [!NOTE]
    > 이 값은 앱 등록의 **응용 프로그램 ID URI** 입니다. 백 엔드 API에 액세스 하는 프런트 엔드 웹 앱을 사용 하려는 경우, 예를 들어 백 엔드를 사용 하 여 프런트 엔드에 대 한 액세스 권한을 명시적으로 부여 하려면 다음을 수행 하는 App Service 경우 *프런트 엔드* 의 **응용 프로그램 ID URI** 가 필요 합니다. > 백 엔드입니다.
1. **범위 추가**를 선택합니다. **범위 이름**에 *user_impersonation*를 입력 합니다. 텍스트 상자에 동의 페이지에서 사용자에 게 표시할 동의 범위 이름 및 설명을 입력 합니다 (예: *내 앱 액세스*). 완료 되 면 **범위 추가**를 클릭 합니다.
1. 필드 클라이언트 암호를 만들려면 **인증서 & 비밀** > **새 클라이언트 암호** > **추가**를 선택 합니다. 페이지에 표시 된 클라이언트 암호 값을 복사 합니다. 다른 곳으로 이동 하면 다시 표시 되지 않습니다.
1. 필드 **회신 url**을 여러 개 추가 하려면 메뉴에서 **인증** 을 선택 합니다.

### <a name="secrets"> </a>App Service 앱에 Azure Active Directory 정보 추가

1. [Azure Portal]에서 App Service 앱으로 이동합니다. 왼쪽 메뉴에서 **인증/권한 부여**를 선택 합니다. 인증/권한 부여 기능이 사용 하도록 설정 되지 않은 경우 **켜기**를 선택 합니다. 
1. 필드 기본적으로 App Service 인증은 앱에 대 한 인증 되지 않은 액세스를 허용 합니다. 사용자 인증을 적용 하려면 **요청이 인증 되지 않은 경우 수행할 작업** 을 Azure Active Directory를 **사용 하 여 로그인**하도록 설정 합니다.
1. 인증 공급자 아래에서 **Azure Active Directory**를 선택 합니다.
1. **관리 모드**에서 **고급** 을 선택 하 고 다음 표에 따라 App Service 인증을 구성 합니다.

    |필드|Description|
    |-|-|
    |클라이언트 ID| 앱 등록의 **응용 프로그램 (클라이언트) ID** 를 사용 합니다. |
    |발급자 ID| 을 `https://login.microsoftonline.com/<tenant-id>`사용 하 고  *\<테 넌 트 id >* 를 앱 등록의 **디렉터리 (테 넌 트) id** 로 바꿉니다. |
    |클라이언트 암호 (선택 사항)| 앱 등록에서 생성 한 클라이언트 암호를 사용 합니다.|
    |허용되는 토큰 대상 그룹| *백 엔드* 앱 인 경우 프런트 엔드 앱의 인증 토큰을 허용 하려면 여기에 *프런트 엔드* 의 **응용 프로그램 ID URI** 를 추가 합니다. |
1. **확인**을 선택 하 고 **저장**을 선택 합니다.

이제 App Service 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## <a name="configure-a-native-client-application"></a>네이티브 클라이언트 애플리케이션 구성
**Active Directory 인증 라이브러리**와 같은 클라이언트 라이브러리를 사용 하 여 로그인을 수행 하려는 경우에는 네이티브 클라이언트를 등록할 수 있습니다.

1. [Azure Portal]의 왼쪽 메뉴에서 **Active Directory** > **앱 등록** > **새 등록**을 선택 합니다. 
1. **응용 프로그램 등록** 페이지에서 앱 등록에 대 한 **이름을** 입력 합니다.
1. **리디렉션 URI**에서 **공용 클라이언트 (모바일 & 데스크톱)** 를 선택 하 고 App Service 앱의 URL을 입력 한 후 경로 `/.auth/login/aad/callback`를 추가 합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback` )을 입력합니다. 그런 다음 **만들기**를 선택합니다.

    > [!NOTE]
    > Windows 응용 프로그램의 경우 대신 [패키지 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) 를 URI로 사용 합니다.
1. 앱 등록을 만든 후에는 **응용 프로그램 (클라이언트) ID**의 값을 복사 합니다.
1. 왼쪽 메뉴에서 **api 사용** > 권한**내 api** **추가** > 를 선택 합니다.
1. App Service 앱에 대해 이전에 만든 앱 등록을 선택 합니다. 앱 등록이 표시 되지 않으면 [App Service 앱에 대 한 AZURE AD에서 앱 등록 만들기](#register)에서 **user_impersonation** 범위를 추가 했는지 확인 합니다.
1. **User_impersonation** 을 선택 하 고 **사용 권한 추가**를 클릭 합니다.

이제 App Service 앱에 액세스할 수 있는 네이티브 클라이언트 애플리케이션을 구성했습니다.

## <a name="related-content"> </a>관련 콘텐츠

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
