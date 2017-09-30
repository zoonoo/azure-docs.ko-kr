---
title: "Azure App Service의 인증 및 권한 부여 | Microsoft Docs"
description: "Azure 앱 서비스에 대한 인증/권한 부여 기능의 개념 참조 및 개요"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: b67f95847ac3228aa1d04d89af7e9fe4eeff3220
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Azure 앱 서비스의 인증 및 권한 부여
## <a name="what-is-app-service-authentication--authorization"></a>앱 서비스 인증/권한 부여란?
앱 서비스 인증/권한 부여는 앱 백 엔드에서 코드를 변경하지 않아도 되도록 사용자로 로그인하는 응용 프로그램에 대한 방법을 제공하는 기능입니다. 응용 프로그램을 보호하고 사용자 단위당 데이터로 작업하는 쉬운 방법을 제공합니다.

앱 서비스는 페더레이션된 ID를 사용하며 여기서 타사 ID 공급자는 계정을 저장하고 사용자를 인증합니다. 응용 프로그램은 앱에서 해당 정보 자체를 저장할 필요가 없도록 공급자의 ID 정보를 사용합니다. 앱 서비스는 기본적으로 Azure Active Directory, Facebook, Google, Microsoft 계정 및 Twitter와 같은 다섯 가지 ID 공급자를 지원합니다. 앱은 개수에 관계 없이 이러한 ID 공급자를 사용하여 최종 사용자에게 로그인 방법에 대한 옵션을 제공할 수 있습니다. 기본 지원을 확장하려면 다른 ID 공급자 또는 [사용자 고유의 사용자 지정 ID 솔루션][custom-auth]을 통합할 수 있습니다.

지금 바로 시작하려는 경우 다음 자습서 중 하나를 참조하세요.

* [iOS 앱][iOS](또는 [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] 또는 [Cordova])에 인증 추가
* [Azure App Service의 API Apps에 대한 사용자 인증][apia-user]

## <a name="how-authentication-works-in-app-service"></a>앱 서비스에서 인증이 작동하는 방식
ID 공급자 중 하나를 사용하여 인증하려면 먼저 ID 공급자를 구성하여 응용 프로그램에 대해 알아야 합니다. ID 공급자가 ID 및 암호를 제공하면 사용자는 그 정보를 앱 서비스에 입력해야 합니다. 이로써 앱 서비스가 ID 공급자에서 인증 토큰 같은 사용자 어설션 유효성을 검사할 수 있도록 트러스트 관계를 완료합니다.

이러한 공급자 중 하나를 사용하여 사용자를 로그인하려면 해당 공급자에 대한 사용자를 로그인하는 끝점으로 리디렉션되어야 합니다. 고객이 웹 브라우저를 사용하는 경우 인증되지 않은 모든 사용자를 사용자를 로그인하는 끝점으로 연결하도록 앱 서비스를 구성할 수 있습니다. 그렇지 않으면 고객을 `{your App Service base URL}/.auth/login/<provider>`로 연결해야 합니다. 여기서 `<provider>`는 aad, facebook, google, microsoft, twitter 중 하나입니다. 모바일 및 API 시나리오는 이 문서의 뒷부분에 나오는 섹션에 설명되어 있습니다.

웹 브라우저를 통해 응용 프로그램과 상호 작용하는 사용자는 응용 프로그램을 찾아보는 동안 인증을 유지할 수 있도록 쿠키가 제공됩니다. 모바일 등 기타 클라이언트 유형의 경우 `X-ZUMO-AUTH` 헤더에 표시되는 JWT(JSON 웹 토큰)가 클라이언트에 발급됩니다. 이 작업은 모바일 앱 클라이언트 SDK가 자동으로 처리합니다. 또는 Azure Active Directory ID 토큰 또는 액세스 토큰이 `Authorization` 헤더에 [전달자 토큰](https://tools.ietf.org/html/rfc6750)으로 직접 포함될 수 있습니다.

앱 서비스는 응용 프로그램에서 발급한 모든 쿠키 또는 토큰의 유효성을 검사하여 사용자를 인증합니다. 응용 프로그램에 액세스할 수 있는 사용자를 제한하는 방법은 이 문서의 뒷부분에 나오는 [권한 부여](#authorization) 섹션을 참조하세요.

### <a name="mobile-authentication-with-a-provider-sdk"></a>공급자 SDK를 통해 모바일 인증
모든 것이 백 엔드에서 구성된 후 앱 서비스를 사용하여 로그인하도록 모바일 클라이언트를 수정할 수 있습니다. 두 가지 접근 방법은 다음과 같습니다.

* 지정된 ID 공급자가 게시한 SDK를 사용하여 ID를 설정한 다음 앱 서비스에 액세스를 얻습니다.
* 모바일 앱 클라이언트 SDK가 사용자에서 로그인할 수 있도록 한 줄의 코드를 사용합니다.

> [!TIP]
> 대부분의 응용 프로그램은 공급자 SDK를 사용하여 사용자가 로그인할 때 더 많은 일관된 환경을 가져오고 새로 고침 지원을 사용하고 공급자가 지정하는 다른 혜택을 가져와야 합니다.
> 
> 

공급자 SDK를 사용하면 사용자는 앱에서 실행 중인 운영 체제와 더 밀접하게 통합되는 환경에 로그인할 수 있습니다. 또한 이렇게 하면 클라이언트의 공급자 토큰 및 사용자 정보를 가져오며 이는 Graph API를 사용하고 사용자 환경을 사용자 지정하는 작업을 용이하게 합니다. 클라이언트의 코드가 사용자에서 로그인하고 클라이언트 코드가 공급자 토큰에 대한 액세스를 갖기 때문에 경우에 따라 블로그 및 포럼에서 "클라이언트 흐름" 또는 "클라이언트에서 제어된 흐름"이라는 것을 확인합니다.

공급자 토큰을 가져오면 유효성 검사에 대한 앱 서비스에 전송되어야 합니다. 앱 서비스는 토큰 유효성을 검사한 후, 클라이언트에 반환되는 새 앱 서비스 토큰을 만듭니다. 모바일 앱 클라이언트 SDK에는 이 교환을 관리하고 응용 프로그램 백 엔드에 대한 모든 요청에 토큰을 자동으로 연결하는 도우미 메서드가 있습니다. 또한 선택하는 경우 개발자는 공급자 토큰에 대한 참조를 유지할 수 있습니다.

### <a name="mobile-authentication-without-a-provider-sdk"></a>공급자 SDK 없이 모바일 인증
공급자 SDK를 설정하지 않으려는 경우 Azure 앱 서비스의 모바일 앱 기능을 로그인하도록 허용할 수 있습니다. 모바일 앱 클라이언트 SDK는 선택한 공급자에게 웹 보기를 열고 사용자를 로그인합니다. 서버가 사용자를 로그인하는 프로세스를 관리하고 클라이언트 SDK가 공급자 토큰을 받지 않기 때문에 경우에 따라 블로그 및 포럼에 "서버 흐름" 또는 "서버에서 제어된 흐름"이라는 것이 나타납니다.

이 흐름을 시작하는 코드는 각 플랫폼에 대한 인증 자습서에 포함됩니다. 흐름의 끝에서 클라이언트 SDK에는 앱 서비스 토큰이 있고 토큰은 응용 프로그램 백 엔드에 대한 모든 요청에 자동으로 연결됩니다.

### <a name="service-to-service-authentication"></a>서비스 간 인증
사용자에게 응용 프로그램에 대한 액세스를 제공했지만 고유의 API를 호출하는 다른 응용 프로그램을 신뢰할 수도 있습니다. 예를 들어 다른 웹앱에서 API를 호출하는 하나의 웹앱을 가질 수 있습니다. 이 시나리오에서는 사용자 자격 증명 대신 서비스 계정에 대한 자격 증명을 사용하여 토큰을 가져옵니다. Azure Active Directory 용어에서 서비스 계정은 *서비스 주체* 라고도 하며, 이러한 계정을 사용하는 인증을 서비스 간 시나리오라고도 합니다.

> [!IMPORTANT]
> Mobile App은 고객 장치에서 실행되므로 신뢰할 수 있는 응용 프로그램으로 집계되지 *않으며* 서비스 주체 흐름을 사용하면 안 됩니다. 대신 이전에 상세하게 설명된 사용자 흐름을 사용해야 합니다.
> 
> 

서비스 간 시나리오의 경우 앱 서비스가 Azure Active Directory를 사용하여 응용 프로그램을 보호할 수 있습니다. 호출 응용 프로그램은 Azure Active Directory의 클라이언트 ID 및 클라이언트 암호를 제공하여 얻은 Azure Active Directory 서비스 주체 권한 부여 토큰을 제공해야 합니다. ASP.NET API 앱을 사용하는 이 시나리오의 예는 [API Apps에 대한 서비스 주체 인증][apia-service] 자습서에서 설명합니다.

앱 서비스 인증을 사용하여 서비스 간 시나리오를 처리하려면 클라이언트 인증서 또는 기본 인증을 사용하면 됩니다. Azure의 클라이언트 인증서에 대한 자세한 내용은 [웹앱에 대한 TLS 상호 인증을 구성하는 방법](app-service-web-configure-tls-mutual-auth.md)을 참조하세요. ASP.NET에서 기본 인증에 대한 자세한 내용은 [ASP.NET Web API 2에서의 인증 필터](http://www.asp.net/web-api/overview/security/authentication-filters)를 참조하세요.

앱 서비스 논리 앱에서 API Apps로 서비스 계정 인증은 [논리 앱으로 앱 서비스에서 호스트되는 사용자 지정 API 사용](../logic-apps/logic-apps-custom-hosted-api.md)에 구체적으로 설명된 특수 사례입니다.

## <a name="authorization"></a>앱 서비스에서 권한 부여가 작동하는 방식
응용 프로그램에 액세스할 수 있는 요청을 완벽하게 제어할 수 있습니다. 다음 동작 중 하나로 앱 서비스 인증/권한 부여를 구성할 수 있습니다.

* 인증된 요청만 응용 프로그램에 도달하도록 허용합니다.
  
    브라우저에서 익명 요청을 받으면 앱 서비스는 사용자가 로그인할 수 있도록 사용자가 선택한 ID 공급자의 페이지로 리디렉션됩니다. 모바일 장치에서 요청이 오면 HTTP *401 권한 없음* 응답이 반환됩니다.
  
    이 옵션을 사용하면 앱에서 인증 코드를 작성할 필요가 전혀 없습니다. 세부적인 권한 부여가 필요한 경우 해당 사용자에 대한 정보를 코드에 사용할 수 있습니다.
* 모든 요청이 응용 프로그램에 도달하도록 허용하지만 인증된 요청을 유효성 검사하고 HTTP 헤더에 인증 정보를 전달합니다.
  
    이 옵션은 응용 프로그램 코드에 대한 권한 부여 결정을 연기합니다. 익명 요청을 보다 유연하게 처리할 수 있다는 장점이 있지만 코드를 작성해야 합니다.
* 모든 요청이 응용 프로그램에 도달하도록 허용하고 요청에서 인증 정보에 어떠한 작업도 하지 않습니다.
  
    이 경우 인증/권한 부여 기능이 꺼집니다. 인증 및 권한 부여 작업이 전적으로 응용 프로그램 코드에 의해 처리됩니다.

이전 동작은 Azure 포털의 **요청이 인증되지 않은 경우에 수행할 동작**을 통해 제어됩니다. ***공급자 이름*으로 로그인**을 선택하는 경우 모든 요청이 인증되어야 합니다. **요청 허용(작업 없음)**은 코드에 대한 권한 부여 결정을 연기하지만 여전히 인증 정보를 제공합니다. 코드에서 모든 항목을 처리하게 하려는 경우 인증/권한 부여 기능을 비활성화할 수 있습니다.

## <a name="working-with-user-identities-in-your-application"></a>응용 프로그램에서 사용자 ID 사용
앱 서비스는 특수 헤더를 사용하여 일부 사용자 정보를 응용 프로그램에 전달합니다. 외부 요청은 이러한 헤더를 금지하며 앱 서비스 인증/권한 부여를 통해 설정된 경우에만 존재합니다. 다음은 이러한 헤더의 예입니다.

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

모든 언어로 작성된 코드 또는 프레임워크는 이러한 헤더에서 필요한 정보를 가져올 수 있습니다. ASP.NET 4.6 앱의 경우 **ClaimsPrincipal** 이 적절한 값으로 자동 설정됩니다.

또한 응용 프로그램이 `/.auth/me` 끝점에서 HTTP GET을 통해 추가 사용자 세부 정보를 가져올 수도 있습니다. 요청에 포함된 유효한 토큰은 사용되는 공급자에 대한 세부 정보, 기본 공급자 토큰 및 기타 사용자 정보와 함께 JSON 페이로드를 반환합니다. 모바일 앱 서버 SDK는 이 데이터를 사용하기 위한 도우미 메서드를 제공합니다. 자세한 내용은 [Azure Mobile Apps Node.js SDK를 사용하는 방법](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) 및 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)을 참조하세요.

## <a name="documentation-and-additional-resources"></a>문서 및 추가 리소스
### <a name="identity-providers"></a>ID 공급자
다음 자습서에서는 앱 서비스를 구성하여 다른 인증 공급자를 사용하는 방법을 보여줍니다.

* [Azure Active Directory 로그인을 사용하도록 앱을 구성하는 방법][AAD]
* [Facebook 로그인을 사용하도록 앱을 구성하는 방법][Facebook]
* [Google 로그인을 사용하도록 앱을 구성하는 방법][Google]
* [Microsoft 계정 로그인을 사용하도록 앱을 구성하는 방법][MSA]
* [Twitter 로그인을 사용하도록 앱을 구성하는 방법][Twitter]

여기에 제공된 것 이외의 ID 시스템을 사용하려는 경우 [Mobile Apps .NET 서버 SDK에서 사용자 지정 인증 지원 미리 보기][custom-auth]를 사용할 수 있습니다. 이 미리 보기는 Web Apps, Mobile Apps 또는 API Apps에 사용할 수 있습니다.

### <a name="web-applications"></a>웹 응용 프로그램
다음 자습서는 웹 응용 프로그램에 인증을 추가하는 방법을 보여 줍니다.

* [Azure App Service 시작 - 2부][web-getstarted]

### <a name="mobile-applications"></a>모바일 응용 프로그램
다음 자습서는 서버 지향 흐름을 사용하여 모바일 클라이언트에 인증을 추가하는 방법을 보여 줍니다.

* [iOS 앱에 인증 추가][iOS]
* [Android 앱에 인증 추가][Android]
* [Windows 앱에 인증 추가][Windows]
* [Xamarin.iOS 앱에 인증 추가][Xamarin.iOS]
* [Xamarin.Android 앱에 인증 추가][Xamarin.Android]
* [ 앱에 인증 추가][Xamarin.Forms]
* [Cordova 앱에 인증 추가][Cordova]

Azure Active Directory에 대한 클라이언트에서 제어된 흐름을 사용하려는 경우 다음 리소스를 사용합니다.

* [iOS용 Active Directory 인증 라이브러리 사용][ADAL-iOS]
* [Android용 Active Directory 인증 라이브러리 사용][ADAL-Android]
* [Windows 및 Xamarin용 Active Directory 인증 라이브러리 사용][ADAL-dotnet]

Facebook에 대한 클라이언트에서 제어된 흐름을 사용하려는 경우 다음 리소스를 사용합니다.

* [iOS용 Facebook SDK 사용](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Twitter에 대한 클라이언트에서 제어된 흐름을 사용하려는 경우 다음 리소스를 사용합니다.

* [iOS용 Twitter 패브릭 사용](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Google에 대한 클라이언트에서 제어된 흐름을 사용하려는 경우 다음 리소스를 사용합니다.

* [iOS용 Google 로그인 SDK 사용](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>API 응용 프로그램
다음 자습서에서는 API Apps를 보호하는 방법을 보여 줍니다.

* [Azure App Service의 API Apps에 대한 사용자 인증][apia-user]
* [Azure App Service의 API Apps에 대한 서비스 주체 인증][apia-service]

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal

