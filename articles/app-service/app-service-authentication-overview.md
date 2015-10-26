<properties 
	pageTitle="Azure 앱 서비스 API 앱 및 모바일 앱에서 인증" 
	description="Azure 앱 서비스에서 API 앱 및 모바일 앱 인증을 구성 및 사용하는 방법을 알아봅니다." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Azure 앱 서비스에서 API 앱 및 모바일 앱 인증

## 개요

이 문서에서는 [API 앱](../app-service-api/app-service-api-apps-why-best-platform.md) 및 [모바일 앱](../app-service-mobile/app-service-mobile-value-prop-preview.md)에 대한 기본 제공 인증 기능을 설명합니다.

이 문서 끝부분의 [다음 단계](#next-steps) 섹션에서는 관련 사용 방법 문서를 제공합니다.

## Azure 앱 서비스 게이트웨이

Azure 앱 서비스는 [OAuth 2.0](#oauth) 및 [OpenID Connect](#oauth)를 구현하고 여러 *ID 공급자*에서 작동하는 기본 제공 인증 서비스를 제공합니다. ID 공급자는 Azure 앱 서비스에서 신뢰할 수 있는 외부 서비스이며, 앱 사용자를 인증합니다. 앱 서비스는 가장 인기 있는 ID 공급자를 지원합니다.

* Azure Active Directory
* Microsoft 계정
* Google
* Twitter
* Facebook

### 게이트웨이 아키텍처

API 앱 또는 모바일 앱이 들어 있는 Azure 리소스 그룹은 *게이트웨이*를 포함하고 있습니다. 게이트웨이는 웹앱에서 실행하여 리소스 그룹의 API 앱 및 모바일 앱에 대한 인증을 포함한 관리 작업을 처리하는 Azure 리소스입니다.

게이트웨이는 로그인 절차를 처리하고, 토큰을 관리하고, 인증되지 않은 호출이 [인증된 액세스를 요구하도록 구성된](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app) API 앱에 접근하지 못하게 합니다. 리소스 그룹의 API 앱을 대상으로 하는 모든 수신 HTTP 요청은 게이트웨이를 통해 라우팅되므로 게이트웨이는 API 앱에 대한 액세스를 제어할 수 있습니다.

다음 다이어그램은 이러한 게이트웨이 기능을 보여줍니다.

![](./media/app-service-authentication-overview/gateway.png)

### 게이트웨이 기능

게이트웨이 인증 서비스는 사용자의 OAuth 2.0 구현 실행에 비해 여러 가지 이점을 제공합니다.

* Microsoft는 간단한 구문을 사용하여 인증 및 권한 부여 작업을 수행할 수 있도록 하는 SDK를 제공합니다.

* 앱 서비스는 더 많은 인증 작업을 처리하므로 개발 및 테스트 시간을 최소화하며 OAuth 공급자 구현에 대한 변경의 영향을 대부분 또는 모두 방지합니다.

* 보호할 응용 프로그램이 여러 개인데 이들을 리소스 그룹 한 개에 보관하면 게이트웨이에 대한 리디렉션 URL이 한 개뿐이므로 각 인증 공급자에 대해 한 개의 클라이언트 ID와 클라이언트 암호만 필요합니다.

* 게이트웨이를 모니터링하면 전체 리소스 그룹에 대한 인증 관련 트래픽을 모니터링할 수 있으므로 모니터링 및 문제 해결이 더 쉽습니다.

* 로컬로 디버그 모드에서 실행하는 동안 게이트웨이를 사용하도록 프로그램을 구성할 수 있고 ID 공급자 계정의 리디렉션 URL을 변경할 필요가 없으므로 디버깅이 더 쉽습니다.

## 서버 흐름과 클라이언트 흐름 비교

앱 서비스 게이트웨이는 클라이언트를 인증하는 두 가지 방법 *클라이언트 흐름* 및 *서버 흐름*을 제공합니다. 두 흐름에서 모두, 클라이언트 응용 프로그램은 사용자 자격 증명(일반적으로 사용자 이름 및 암호)를 ID 공급자에게 직접 보냅니다. 게이트웨이도 응용 프로그램도 어느 흐름의 사용자 자격 증명도 받지 않습니다.

### 클라이언트 흐름

클라이언트 흐름은 클라이언트 응용 프로그램이 ID 공급자의 액세스 토큰을 가져오기 위해 해당 공급자와 직접 통신하는 것을 의미합니다. 클라이언트 응용 프로그램은 공급자의 액세스 토큰을 게이트웨이에 보냅니다. 게이트웨이는 사용자 컨텍스트 토큰을 만들어 클라이언트에 보냅니다. 이 사용자 컨텍스트 토큰을 [Azure 모바일 서비스](/documentation/services/mobile-services/)에 대한 원래 코드 이름을 따서 Zumo 토큰이라고도 합니다. (API 앱 및 모바일 앱에 대한 인증 서비스는 원래 모바일 서비스용으로 개발된 것과 같은 아키텍처를 기반으로 합니다.)

아래 다이어그램은 이 흐름을 보여 줍니다.

![](./media/app-service-authentication-overview/clientflow.png)

클라이언트는 보호된 API 앱 또는 모바일 앱을 호출할 때 HTTP 요청에 Zumo 토큰을 제공합니다. 게이트웨이는 공급자 토큰을 저장하고 어느 토큰이 어느 Zumo 토큰과 연결되는지를 기억합니다.

### 서버 흐름

서버 흐름은 클라이언트 응용 프로그램이 ID 공급자와 통신하여 로그인을 시작하기 위해 게이트웨이에 의존한다는 것을 의미합니다. 클라이언트 브라우저는 게이트웨이 URL로 이동하며, 게이트웨이는 이 요청을 ID 공급자의 로그인 페이지로 리디렉션합니다. 사용자가 로그인한 후 게이트웨이는 ID 공급자의 토큰을 가져오고 Zumo 토큰을 만들고, Zumo 토큰을 클라이언트에 보냅니다.

![](./media/app-service-authentication-overview/serverflow.png)

클라이언트 응용 프로그램과 보호된 API 앱 또는 모바일 앱 간의 이후 상호 작용은 클라이언트 흐름의 경우와 같이 처리됩니다. 즉, 클라이언트는 Zumo 토큰을 HTTP 요청에 제공합니다.

### 클라이언트 흐름과 서버 흐름 중에서 선택하는 방법

클라이언트 흐름은 일반적으로 사용할 ID 공급자가 지원할 클라이언트 플랫폼에 대한 SDK를 가지고 있는 경우 최선의 선택입니다. 클라이언트 흐름은 사용자가 자격 증명을 입력해야 하는 횟수를 줄여 주므로 최고의 사용자 경험을 제공합니다. 예를 들어 사용자가 Android 장치를 가지고 있는 경우, 해당 사용자는 아마 장치와 연결된 Google 계정을 가지고 있을 것이므로, 자신의 사용자 이름 및 암호를 다시 입력할 필요 없이 해당 계정을 사용할 수 있어 사용자 경험이 개선됩니다. Android, iOS 또는 Windows Phone 장치의 Facebook 계정, 또는 Windows 데스크톱 또는 Windows Phone의 Microsoft 계정도 마찬가지입니다.

다른 시나리오에서는 서버 흐름이 더 현명한 선택일 수 있습니다.

- 지원할 ID 공급자와 클라이언트 플랫폼에 대한 고유한 클라이언트 SDK는 없습니다.

- 필요한 것을 신속하게 생산한 후 나중에 여건이 허락할 때 사용자 경험을 개선하는 것이 좋습니다. Azure 앱 서비스는 서버 흐름을 사용하여 인증 작업을 더 많이 수행할 수 있으므로, 수행해야 하는 개발 및 테스트의 양이 최소화됩니다.

## 발신 SaaS 플랫폼 호출을 대신 수행

로그인한 사용자를 대신하여 SaaS(Software-as-a-Service) 플랫폼에 대한 발신 호출을 하는 코드를 작성하거나 [커넥터 API 앱](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md)을 사용할 수 있습니다. 예를 들어 사용자의 Twitter 계정에서 한 트윗을 게시하려면 [Twitter SDK](https://dev.twitter.com/overview/api/twitter-libraries)를 사용하거나 사용자의 Azure 구독에 [Twitter 커넥터](../app-service-mobile/app-service-logic-connector-twitter.md)를 프로비저닝하고 해당 커넥터를 호출할 수 있습니다. 이 섹션에서는 API 앱 또는 모바일 앱에서 실행하는 코드에서 SaaS 플랫폼에 액세스하는 작업에 대해 설명합니다.

### <a id="obotoidprovider"></a> ID 공급자 토큰 사용 

게이트웨이는 하나 이상의 ID 공급자 액세스 토큰과 연결하고 토큰을 새로 고치는 *토큰 저장소*를 유지 관리합니다. 유효한 Zumo 토큰을 포함한 HTTP 요청을 받을 때, 게이트웨이는 해당 사용자와 관련된 ID 공급자 토큰을 알고 있습니다.
  
API 앱 또는 모바일 앱에서 실행 중인 코드가 로그온한 사용자를 대신하여 보호된 리소스를 호출해야 하는 경우, 다음 다이어그램과 같이 게이트웨이의 토큰 저장소에서 ID 공급자의 토큰을 검색하여 사용할 수 있습니다. 이 다이어그램에서는 클라이언트가 게이트웨이에서 이미 인증을 받았으며 Zumo 토큰이 있다고 가정합니다.

![](./media/app-service-authentication-overview/idprovidertoken.png)

예를 들어 ID 공급자가 AAD(Azure Active Directory)이고 사용자의 API 앱에서 AAD 액세스 토큰을 사용하여 AAD 그래프 API를 호출하거나 사용자가 권한을 가지고 있는 SharePoint 사이트에 대한 액세스를 요청하려 한다고 가정합니다. 이 경우 요청을 게이트웨이에 보내서 AAD 토큰을 검색한 다음, AAD 토큰을 사용하여 그래프 API를 호출하거나 SharePoint 사이트에 대 한 액세스 토큰을 가져올 수 있습니다.

### <a id="obotosaas"></a>다른 리소스에 액세스하기 위해 사용자 동의 얻기

또한 게이트웨이는 원래 ID 공급자가 아닌 공급자가 보호된 리소스에 액세스하려는 경우 사용자의 동의를 얻기 위한 기본 제공 기능을 가지고 있습니다. 예를 들어 Azure Active Directory를 사용하여 로그인하는 사용자의 경우, 해당 사용자의 Dropbox 계정에서 파일에 액세스하는 것이 좋습니다.

앱 서비스 게이트웨이는 다음과 같은 공급자로부터 그러한 액세스에 대한 사용자의 동의를 얻기 위한 기본 제공 지원을 포함하고 있습니다.

* Box
* DropBox
* Facebook
* Google
* Office365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer
* Azure Active Directory
* Microsoft 계정

이러한 공급자에 대해, 게이트웨이는 ID 공급자 액세스 토큰의 경우와 마찬가지로 액세스 토큰을 유지 관리하고 이 토큰을 Zumo 토큰과 연결합니다. 사용자 동의를 얻고 SaaS 플랫폼을 호출하는 프로세스는 다음 다이어그램에 나와 있습니다. 이 다이어그램에서는 클라이언트가 게이트웨이에서 이미 인증을 받았으며 Zumo 토큰이 있다고 가정합니다.

![](./media/app-service-authentication-overview/saastoken.png)

앱 서비스 런타임 지원 및 SDK를 사용하면 이러한 공급자 중 하나가 보호된 리소스에 액세스하는 코드를 비교적 쉽게 작성할 수 있습니다. 다이어그램에 표시되지 않은 예비 단계: 공급자와 함께 계정을 설정하고 Azure 앱 서비스에서 공급자의 클라이언트 ID 및 클라이언트 보안 설정을 구성해야 합니다.

이러한 공급자 및 많은 다른 공급자에 대해 미리 패키지된 [커넥터 API 앱](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md)을 사용하여 보호된 리소스에 액세스할 수도 있습니다.

## SDK 가용성

API 앱의 경우, SDK for .NET이 인증 기능을 제공합니다.

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService) - API 앱 클라이언트에 사용하기 위한 기능.  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) - API 앱에서 실행하는 웹 API 프로젝트에 사용하기 위한 기능.
 
또한 Visual Studio는 사용자의 API 앱을 호출하는 코드를 훨씬 더 단순화하기 위해 자동으로 SDK for .NET에서 작동하는 코드를 생성할 수 있습니다. 자세한 내용은 [.NET 클라이언트의 Azure 앱 서비스에서 API 앱 사용](../app-service-api/app-service-api-dotnet-consume.md)을 참조하세요.

모바일 앱의 경우, 다음과 같은 플랫폼에 대한 SDK를 사용할 수 있습니다.

- [.NET 서버](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript(개발 시의 자습서)

## 대체 인증 방법

게이트웨이 인증 서비스가 앱의 요구에 맞지 않은 경우, 인증을 직접 처리하거나 Azure API 관리 서비스를 사용할 수 있습니다.

### <a id="doityourself"></a>직접 인증

Azure에서 [ASP.NET Identity](http://www.asp.net/identity) 또는 [Thinktecture](http://www.thinktecture.com/identityAndAccessControl) 같은 인증 프레임워크를 실행할 수 있습니다. 이를 통해 모든 기능이 작동하는 방법을 제어할 수 있지만, 인증 기능의 개발과 테스트에 더 많은 시간을 소비해야 합니다. 또한 여러 리디렉션 URL로 보호할 앱이 여러 개인 경우, Facebook, Google 및 Twitter와 같은 타사 인증 공급자를 사용하여 여러 클라이언트 ID 및 클라이언트 암호를 구성해야 합니다.

현재 앱 서비스는 [모바일 서비스](mobile-services-dotnet-backend-get-started-custom-authentication.md)에서 가능한 것과 같은 게이트웨이 인증에 대한 자체 솔루션 사용을 지원하지 않습니다.

### <a id="apim"></a>Azure API 관리

인증으로 보호할 기존 API가 있으면 Azure API 관리 서비스로 보호할 수 있습니다. API 앱과 함께 API 관리 사용에 대한 자세한 내용은 Panos Kefalidis의 블로그 게시물: [Taking advantage of API Management for API Apps](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/)(API 앱에 대한 API 관리 활용)를 참조하세요.

## 다음 단계

이 문서에서는 API 앱 및 모바일 앱에 대해 Azure 앱 서비스에서 제공하는 인증 서비스를 설명했습니다. 기본 인증 프로토콜에 대해 배울 수 있는 리소스 및 서비스 인증 기능을 사용하는 방법에 관한 문서에 연결하는 몇몇 링크가 있습니다.

* [OAuth 2.0, OpenID Connect 및 JSON 웹 토큰(JWT)](#oauth)
* API 앱 리소스
	* [API 앱 클라이언트 흐름](#apiaclient)
	* [API 앱 서버 흐름](#apiaserver)
	* [API 앱 대신 호출](#apiaobo)
* 모바일 앱 리소스
	* [모바일 앱 클라이언트 흐름](#maclient)
	* [모바일 앱 서버 흐름](#maserver)
	* [모바일 앱 대신 호출](#maobo)

### <a id="oauth"></a>OAuth 2.0, OpenID Connect 및 JSON 웹 토큰(JWT)

* [OAuth 2.0 시작](http://shop.oreilly.com/product/0636920021810.do "OAuth 2.0 시작") 
* [OAuth2, OpenID Connect 및 JSON 웹 토큰(JWT) 소개 - PluralSight 코스](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [ASP.NET에서 여러 클라이언트를 위한 RESTful API 구축 및 보호 - PluralSight 코스](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

### <a id="apiaclient"></a>API 앱 클라이언트 흐름

* [API 앱 보호](../app-service-api/app-service-api-dotnet-add-authentication.md) - API 앱 구성 부분은 클라이언트와 서버 흐름에 모두 적용되지만, 브라우저 내 테스트 부분은 서버 흐름만 보여줍니다.
* [.NET 클라이언트에서 Azure 앱 서비스의 API 앱 사용](../app-service-api/app-service-api-dotnet-consume.md) - 인증된 호출에 대한 샘플 코드가 서버 흐름을 보여 주지만 뒤에는 샘플 코드가 포함된 [클라이언트 흐름](../app-service-api/app-service-api-dotnet-consume.md#client-flow)이 나옵니다.

### <a id="apiaserver"></a>API 앱 서버 흐름

* [API 앱 보호](../app-service-api/app-service-api-dotnet-add-authentication.md) - API 앱 구성 부분은 클라이언트와 서버 흐름에 모두 적용되고 브라우저 내 테스트 부분은 서버 흐름만 보여 줍니다.
* [.NET 클라이언트에서 Azure 앱 서비스의 API 앱 사용](../app-service-api/app-service-api-dotnet-consume.md) - 인증된 호출에 대한 샘플 코드가 서버 흐름을 보여줍니다. 

### <a id="apiaobo"></a>API 앱 대신 호출

* [Azure 앱 서비스에서 SaaS 커넥터 API 앱 배포 및 구성](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md) - 미리 패키지된 커넥터 API 앱을 프로비저닝하고, 해당 앱을 구성하고, 브라우저 도구를 사용하여 호출하는 방법을 보여줍니다.
* [Azure 앱 서비스의 ASP.NET API 앱에서 SaaS 플랫폼에 연결](../app-service-api/app-service-api-dotnet-connect-to-saas.md) - 자체 커넥터를 작성하는 방법, 즉 SaaS 공급자에 대한 대신 호출을 수행하는 사용자 지정 API 앱에 대한 코드를 프로비전, 구성 및 작성하는 방법을 보여 줍니다.

### <a id="maclient"></a>모바일 앱 클라이언트 흐름

* [iOS 앱에 Azure Active Directory Single Sign-On 추가](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>모바일 앱 서버 흐름

* [iOS 앱에 인증 추가](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [Xamarin.iOS 앱에 인증 추가](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [Xamarin Android 앱에 인증 추가](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [Windows 앱에 인증 추가](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md)

### <a id="maobo"></a>보호된 리소스에 대한 모바일 앱 대신 호출

* [모바일 앱에서 액세스 토큰 가져오기 및 SharePoint API 호출](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=Oct15_HO3-->