---
title: Azure Active Directory 인증 구성 - Azure App Service
description: App Services 애플리케이션에 대해 Azure Active Directory 인증을 구성하는 방법을 알아봅니다.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 0c5ceda99fe35fafff23f2bcf4ea766d7dd42b75
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403224"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Azure Active Directory 로그인을 사용하도록 App Service 앱 구성

> [!NOTE]
> 지금은 AAD V2 MSAL 등 Azure App Services 및 Azure Functions에 대 한 지원 되지 않습니다. 업데이트를 다시 확인 하세요.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Azure Active Directory를 인증 공급자로 사용하도록 Azure App Services를 구성하는 방법을 보여 줍니다.

## <a name="express"> </a>기본 설정을 사용하여 구성

1. [Azure Portal]에서 App Service 앱으로 이동합니다. 왼쪽 탐색 영역에서 **인증/권한 부여**를 선택합니다.
2. **인증/권한 부여**가 사용되도록 설정되지 않은 경우 **설정**을 선택합니다.
3. **Azure Active Directory**를 선택한 다음 **관리 모드**에서 **Express**를 선택합니다.
4. **확인**을 선택하여 Azure Active Directory에 App Service 앱을 등록합니다. 이렇게 하면 새 앱이 등록됩니다. 기존 앱 등록을 대신 선택하려는 경우 **기존 앱 선택** 을 클릭한 다음 테넌트 내에서 이전에 만든 앱 등록 이름을 검색합니다.
   앱 등록을 클릭하여 선택하고 **확인**을 클릭합니다. Azure Active Directory 설정 페이지에서 **확인**을 클릭합니다.
   기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.
5. (옵션) Azure Active Directory에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **요청이 인증되지 않으면 수행할 동작**을 **Azure Active Directory로 로그인**으로 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Azure Active Directory로 리디렉션되어야 합니다.
6. **저장**을 클릭합니다.

이제 App Service 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## <a name="advanced"> </a>고급 설정을 사용하여 구성

수동으로 구성 설정을 제공할 수도 있습니다. 사용하려는 Azure Active Directory 테넌트가 Azure에 로그인하는 테넌트와 다른 경우 권장되는 솔루션입니다. 구성을 완료하려면 먼저 Azure Active Directory에 등록을 만든 다음 App Service에 등록 세부 정보 중 일부를 제공해야 합니다.

### <a name="register"> </a>Azure Active Directory에 App Service 앱 등록

1. [Azure Portal]에 로그인한 다음, App Service 앱으로 이동합니다. 앱 **URL**을 복사합니다. Azure Active Directory 앱 등록을 구성하는 데 이 정보를 사용합니다.
2. **Active Directory**를 선택한 후 **앱 등록**을 선택하고 위쪽의 **새 애플리케이션 등록**을 클릭하여 새 앱 등록을 시작합니다. 
3. **만들기** 페이지에서 앱 등록의 **이름**을 입력하고 **웹앱/API** 유형을 선택한 후 **로그온 URL** 상자에 애플리케이션 URL(1단계)을 붙여 넣습니다. 그런 다음 **만들기**를 클릭합니다.
4. 몇 초 후에 방금 만든 새 앱 등록이 나타납니다.
5. 앱 등록이 추가되면 앱 등록 이름을 클릭하고 위쪽의 **설정**을 클릭한 후 **속성**을 클릭합니다. 
6. **앱 ID URI** 상자에 애플리케이션 URL(1단계)을 붙여 넣고, **홈페이지 URL**에도 애플리케이션 URL(1단계)을 붙여 넣은 후 **저장**을 클릭합니다.
7. 이제 **회신 URL**을 클릭하고 **회신 URL**을 편집한 다음, 애플리케이션 URL(1단계)을 붙여넣고 URL 끝에 */.auth/login/aad/callback*을 추가합니다(예: `https://contoso.azurewebsites.net/.auth/login/aad/callback`). **저장**을 클릭합니다.   
8.  이때 앱의 **애플리케이션 ID**를 복사합니다. 나중에 사용하기 위해 보관합니다. App Service 앱을 구성하는 데 필요합니다.
9. **등록된 앱** 페이지를 닫습니다. **앱 등록** 페이지에서 맨 위의 **엔드포인트** 단추를 클릭하고 **WS-FEDERATION SIGN-ON ENDPOINT** URL을 복사하되 끝에 있는 `/wsfed`를 URL에서 제거합니다. 최종 결과는 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`과 같아야 합니다. 소버린 클라우드의 경우 도메인 이름은 다를 수 있습니다. 이 URL은 나중에 발급자 URL로 사용됩니다.

### <a name="secrets"> </a>App Service 앱에 Azure Active Directory 정보 추가

1. [Azure Portal]로 돌아가서 App Service 앱으로 이동합니다. **인증/권한 부여**를 클릭합니다. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **설정**으로 전환합니다. 인증 공급자 아래의 **Azure Active Directory**를 클릭하여 앱을 구성합니다.

    (선택 사항) 기본적으로 앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다. **요청이 인증되지 않은 경우 수행할 작업**을 **Azure Active Directory로 로그인**으로 설정합니다. 이 옵션을 사용하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Azure Active Directory로 리디렉션되어야 합니다.
2. Active Directory 인증 구성에서 **관리 모드** 아래의 **고급**을 클릭합니다. 클라이언트 ID 상자에 애플리케이션 ID(8단계)를 붙여넣고, 발급자 URL 값에 URL(9단계)을 붙여넣습니다. 그런 후 **OK**를 클릭합니다.
3. Active Directory 인증 구성 페이지에서 **저장**을 클릭합니다.

이제 App Service 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## <a name="configure-a-native-client-application"></a>네이티브 클라이언트 애플리케이션 구성
네이티브 클라이언트를 등록하여 권한 매핑에 대한 제어를 강화할 수 있습니다. 이 작업은 **Active Directory 인증 라이브러리** 등의 클라이언트 라이브러리를 사용하여 로그인하려는 경우에 필요합니다.

1. **Azure Portal**에서 [Azure Portal]로 이동합니다.
2. 왼쪽 탐색 영역에서 **앱 등록**을 선택합니다. 위쪽의 **새 앱 등록**을 클릭합니다.
4. **만들기** 페이지에서 앱 등록의 **이름**을 입력합니다. **애플리케이션 유형**에서 **네이티브**를 선택합니다.
5. HTTPS 체계를 사용하여 **리디렉션 URI** 상자에 사이트의 */.auth/login/done* 엔드포인트를 입력합니다. 이 값은 *https://contoso.azurewebsites.net/.auth/login/done*과 비슷해야 합니다. Windows 애플리케이션을 만드는 경우 URI로 [패키지 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)를 사용합니다.
5. **만들기**를 클릭합니다.
6. 앱 등록이 추가되면 선택하여 엽니다. **애플리케이션 ID**를 찾아 해당 값을 적어둡니다.
7. **모든 설정** > **필요한 권한** > **추가** > **API 선택**을 클릭합니다.
8. 이전에 등록한 App Service 앱의 이름을 입력하여 검색한 다음 선택하고 **선택**을 클릭합니다.
9. **\<app_name >에 액세스**를 선택합니다. 그런 다음 **선택**을 클릭합니다. **완료**를 클릭합니다.

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
