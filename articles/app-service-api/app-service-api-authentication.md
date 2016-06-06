<properties
	pageTitle="Azure 앱 서비스의 API 앱에 대한 인증 및 권한 부여 | Microsoft Azure"
	description="Azure 앱 서비스에서 API 앱에 대해 제공하는 인증 및 권한 부여 서비스에 대해 알아보세요."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="tdykstra"/>

# Azure 앱 서비스의 API 앱에 대한 인증 및 권한 부여

[AZURE.INCLUDE [선택기](../../includes/app-service-api-auth-selector.md)]

## 개요 

> [AZURE.NOTE] 이 항목은 웹, 모바일 및 API 앱을 다루는 통합된 [앱 서비스 인증/권한 부여](../app-service/app-service-authentication-overview.md) 항목으로 마이그레이션됩니다.

Azure 앱 서비스는 [OAuth 2.0](#oauth) 및 [OpenID Connect](#oauth)를 구현하는 기본 제공 인증 서비스를 제공합니다. 이 문서에서는 Azure 앱 서비스의 API 앱에 사용할 수 있는 서비스 및 옵션을 설명합니다.

다음 다이어그램에서는 앱 서비스 인증의 몇 가지 주요 특징을 보여 줍니다.

* 들어오는 API 요청을 전처리합니다. 즉, 앱 서비스에서 지원하는 모든 언어 또는 프레임워크와 작동합니다.
* 사용자 고유의 코드에서 얼마나 많은 인증이 작동하도록 할지에 대한 여러 옵션을 제공합니다.
* 최종 사용자 및 서비스 계정 인증 모두에 대해 작동합니다. 
* 여기서는 Azure Active Directory, Facebook, Google, Twitter 및 Microsoft 계정의 5가지 ID 공급자를 지원합니다.
* API 앱, 웹앱 및 모바일 앱에 대해서도 마찬가지로 작동합니다.

![](./media/app-service-api-authentication/api-apps-overview.png)

## 언어 알 수 없음

앱 서비스 인증 처리는 요청이 API 앱에 도달하기 전에 발생하므로 API 앱에 대해 작동하는 인증 기능은 임의 언어 또는 프레임워크로 작성됩니다. 사용자 API는 ASP.NET, Java, Node.js 또는 앱 서비스에서 지원하는 모든 프레임워크를 기반으로 할 수 있습니다.

앱 서비스가 HTTP 요청의 인증 헤더에서 JSON 웹 토큰(JWT)을 전달하고 임의 언어 또는 프레임워크로 작성된 코드는 해당 토큰에서 필요한 정보를 얻을 수 있습니다. 또한 앱 서비스는 다음과 같은 몇 가지 특수 헤더를 설정하여 가장 일반적으로 사용되는 클레임에 보다 쉽게 액세스할 수 있도록 합니다.

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
.NET API에서 `Authorize` 특성을 사용할 수 있으며, 세분화된 권한 부여의 경우 클레임 정보는 .NET 클래스에 자동으로 채워지기 때문에 클레임을 기반으로 코드를 쉽게 작성할 수 있습니다.

## 여러 보호 옵션

앱 서비스는 익명 HTTP 요청이 API 앱에 도달하지 못하도록 하고 모든 요청을 전달하고 이를 포함하는 요청에 대한 토큰의 유효성을 검사하거나 작업 없이 모든 요청을 통과시킬 수 있습니다.

1. 인증된 요청만 API 앱에 도달하도록 허용합니다.

	브라우저에서 익명 요청을 받으면 앱 서비스는 선택한 인증 공급자(Azure AD, Google, Twitter 등)의 로그온 페이지로 리디렉션합니다

	이 옵션을 사용하면 앱에서 인증 코드를 전혀 작성하지 않아도 되며 가장 중요한 클레임이 HTTP 헤더에 제공되므로 인증 코드가 간소화됩니다.

2. 모든 요청이 API 앱에 도달하도록 허용하지만 인증된 요청을 유효성 검사하고 HTTP 헤더에 인증 정보를 전달합니다.

	이 옵션은 익명 요청 처리에 더 많은 유연성을 제공하지만 익명 사용자가 API를 사용하지 못하도록 하려면 코드를 작성해야 합니다. 가장 일반적인 클레임은 HTTP 요청의 헤더에 전달되기 때문에 인증 코드는 비교적 간단합니다.
	
3. 모든 요청이 API 앱에 도달하도록 허용하고 요청에서 인증 정보에 어떠한 작업도 하지 않습니다.

	이 옵션은 인증 및 권한 부여 작업을 전적으로 응용 프로그램 코드의 몫으로 둡니다.

[Azure 포털](https://portal.azure.com/)의 **인증/권한 부여** 블레이드에서 원하는 옵션을 선택합니다.

![](./media/app-service-api-authentication/authblade.png)

옵션 1과 2에 대해 **앱 서비스 인증**을 설정하고 **요청이 인증되지 않은 경우에 수행할 동작** 드롭다운 목록에서 **로그인** 또는 **요청 허용(작업 없음)**을 선택합니다. **로그인**을 선택한 경우 인증 공급자를 선택하고 해당 공급자를 구성해야 합니다.

![](./media/app-service-api-authentication/actiontotake.png)

인증 공급자 구성 블레이드를 구성하는 방법을 설명하는 자세한 지침은 [Azure Active Directory 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)을 참조하세요. 이 문서는 모바일 앱 뿐만 아니라 API 앱에 적용되고 다른 인증 공급자에 대한 다른 문서에 연결합니다.
 
## <a id="internal"></a> 서비스 계정 인증

앱 서비스 인증은 한 API 앱에서 다른 API 앱을 호출하는 경우와 같은 내부 시나리오에 대해 작동합니다. 이 시나리오에서는 최종 사용자 자격 증명 대신, 서비스 계정에 대한 자격 증명을 사용하여 토큰을 가져옵니다. Azure Active Directory에서 서비스 계정은 *서비스 주체*라고도 하며, 이러한 계정을 사용하는 인증을 서비스 간 시나리오라고도 합니다.

서비스 간 시나리오의 경우 Azure Active Directory를 사용하여 호출된 API 앱을 보호하고 API 앱을 호출할 때 AAD 서비스 주체 권한 부여 토큰을 제공합니다. AAD 응용 프로그램에서 클라이언트 ID 및 클라이언트 암호를 제공하여 토큰을 가져옵니다. 모바일 서비스 Zumo 토큰을 처리하는 데 사용되는 것과 같은 특수한 Azure 전용 코드는 필요 없습니다. ASP.NET API 앱을 사용한 이 시나리오의 예는 [API 앱에 대한 서비스 주체 인증](app-service-api-dotnet-service-principal-auth.md) 자습서에서 설명합니다.

앱 서비스 인증을 사용하지 않고 서비스 간 시나리오를 처리하려면 클라이언트 인증서 또는 기본 인증을 사용하면 됩니다. Azure의 클라이언트 인증서에 대한 자세한 내용은 [웹앱에 대한 TLS 상호 인증을 구성하는 방법](../app-service-web/app-service-web-configure-tls-mutual-auth.md)을 참조하세요. ASP.NET에서 기본 인증에 대한 자세한 내용은 [ASP.NET Web API 2에서의 인증 필터](http://www.asp.net/web-api/overview/security/authentication-filters)를 참조하세요.

앱 서비스 논리 앱에서 API 앱으로 서비스 계정 인증은 [논리 앱으로 앱 서비스에서 호스팅되는 사용자 지정 API 사용](../app-service-logic/app-service-logic-custom-hosted-api.md)에 설명된 특수 사례입니다.

## 모바일 클라이언트 인증

모바일 클라이언트에서 인증을 처리하는 방법에 대한 내용은 [모바일 앱 인증에 대한 설명서](../app-service-mobile/app-service-mobile-ios-get-started-users.md)를 참조하세요. 앱 서비스 인증은 모바일 앱 및 API 앱에서 동일한 방식으로 작동합니다.
  
## 자세한 정보

Azure 앱 서비스에서 인증 및 권한 부여에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [앱 서비스 인증/권한 부여 확장](/blog/announcing-app-service-authentication-authorization/)
* [Azure Active Directory 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)(페이지 맨 위에 있는 다른 인증 공급자에 대한 링크를 포함합니다.) 

OAuth 2.0, OpenID Connect 및 JSON 웹 토큰(JWT)에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [OAuth 2.0 시작](http://shop.oreilly.com/product/0636920021810.do "OAuth 2.0 시작") 
* [OAuth2, OpenID Connect 및 JSON 웹 토큰(JWT) 소개 - PluralSight 코스](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [ASP.NET에서 여러 클라이언트를 위한 RESTful API 구축 및 보호 - PluralSight 코스](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Azure Active Directory에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure AD 시나리오](http://aka.ms/aadscenarios)
* [Azure AD 개발자 가이드](http://aka.ms/aaddev)
* [Azure AD 샘플](http://aka.ms/aadsamples)

## 다음 단계

이 문서에서는 API 앱에 사용할 수 있는 앱 서비스의 인증 및 권한 부여 기능을 설명했습니다. 시작 시리즈의 다음 자습서는 [앱 서비스 API 앱의 사용자 인증](app-service-api-dotnet-user-principal-auth.md)을 구현하는 방법을 보여 줍니다.

<!---HONumber=AcomDC_0525_2016-->