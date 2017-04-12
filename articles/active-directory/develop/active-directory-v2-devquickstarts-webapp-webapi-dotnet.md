---
title: "Azure AD v2.0 .NET 호출 API 시작하기 | Microsoft Docs"
description: "개인 Microsoft 계정과 회사 또는 학교 계정을 로그인에 사용하여 웹 서비스를 호출하는 .NET MVC 웹앱을 빌드하는 방법입니다."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 9cd676554542e4effef54790bf9095c5b7a8f75b
ms.openlocfilehash: c6cca8fda4ba6a26d5ee74b43e0bae37639865c5


---
# <a name="calling-a-web-api-from-a-net-web-app"></a>.NET 웹앱에서 Web API 호출
v2.0 끝점에서는 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다를 지원하는 인증을 웹앱 및 Web API에 빠르게 추가할 수 있습니다.  여기서는 Microsoft OWIN 미들웨어를 활용하여 OpenID Connect로 사용자를 로그인하는 MVC 웹앱을 만듭니다.  이 웹앱은 OAuth 2.0에서 보호 된 웹 API에 대한 OAuth 2.0 액세스 토큰을 가져와서 지정된 사용자의 “할 일 목록"을 만들고, 읽고, 삭제할 수 있도록 합니다.

이 자습서에서는 주로 MSAL을 사용하여 웹앱에서 액세스 토큰을 가져오고 사용하는 방법에 중점을 두며 [여기](active-directory-v2-flows.md#web-apps)서 자세히 설명합니다.  필수 조건으로 먼저 [웹앱에 기본 로그인을 추가](active-directory-v2-devquickstarts-dotnet-web.md)하는 방법 또는 [Web API 보안을 적절하게 유지](active-directory-v2-devquickstarts-dotnet-api.md)하는 방법을 알아보는 것이 좋습니다.

> [!NOTE]
> 일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다.  v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
> 
> 

## <a name="download-sample-code"></a>샘플 코드 다운로드
이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet)에서 유지 관리됩니다.  자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) 하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

또는 [완성된 앱을 .zip으로 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) 하거나 완성된 된 앱을 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 새 앱을 만들거나 다음 [세부 단계](active-directory-v2-app-registration.md)를 따릅니다.  다음을 수행해야 합니다.

* 곧 필요하게 되므로 앱에 할당된 **응용 프로그램 ID** 를 적어둡니다.
* **암호** 형식의 **앱 암호**를 만들고 나중에 사용할 수 있도록 해당 값을 적어둡니다.
* 앱에 대한 **웹** 플랫폼을 추가합니다.
* 올바른 **리디렉션 URI**를 입력합니다. 리디렉션 URI는 인증 응답을 보내야 하는 Azure AD를 나타냅니다. 이 자습서에 대한 기본값은 `https://localhost:44326/`입니다.

## <a name="install-owin"></a>OWIN 설치
패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 `TodoList-WebApp` 프로젝트에 추가합니다.  OWIN 미들웨어는 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 데 사용됩니다.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>사용자를 로그인
이제 [OpenID Connect 인증 프로토콜](active-directory-v2-protocols.md)을 사용하도록 OWIN 미들웨어를 구성합니다.  

* `TodoList-WebApp` 프로젝트 루트에 있는 `web.config` 파일을 열고 `<appSettings>` 섹션에 앱의 구성 값을 입력합니다.
  * `ida:ClientId` 는 등록 포털에서 앱에 할당된 **응용 프로그램 ID** 입니다.
  * `ida:ClientSecret` 는 등록 포털에서 만든 **앱 암호** 입니다.
  * `ida:RedirectUri` 는 포털에서 입력한 **리디렉션 URI** 입니다.
* `TodoList-Service` 프로젝트의 루트에 있는 `web.config` 파일을 열고 `ida:Audience`를 위와 동일한 **응용 프로그램 ID**로 바꿉니다.
* `App_Start\Startup.Auth.cs` 파일을 열고 위의 라이브러리에 대한 `using` 문을 추가합니다.
* 동일한 파일에서 `ConfigureAuth(...)` 메서드를 구현합니다.  `OpenIDConnectAuthenticationOptions` 에 제공하는 매개 변수는 앱이 Azure AD와 통신하기 위한 좌표로 사용됩니다.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>MSAL을 사용하여 액세스 토큰 가져오기
`AuthorizationCodeReceived` 알림에서 [OpenID Connect와 함께 OAuth 2.0](active-directory-v2-protocols.md)을 사용하여 authorization_code를 To-Do List Service에 대한 액세스 토큰으로 교환하려고 합니다.  MSAL을 통해 이 프로세스를 쉽게 수행할 수 있습니다.

* 먼저 MSAL 미리 보기 버전을 설치합니다.

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* 또 다른 `using` 문을 MSAL용 `App_Start\Startup.Auth.cs` 파일에 추가합니다.
* 이제 새 메서드인 `OnAuthorizationCodeReceived` 이벤트 처리기를 추가합니다.  이 처리기는 MSAL을 사용하여 할 일 목록 API에 대한 액세스 토큰을 가져오는 데 사용하며, 나중에 MSAL의 토큰 캐시에 토큰을 저장합니다.

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* 웹앱에서는 MSAL에 토큰을 저장하는 데 사용할 수 있는 확장 가능한 토큰 캐시가 있습니다.  이 샘플에서는 HTTP 세션 저장소를 사용하여 토큰을 캐시하는 `NaiveSessionCache` 를 구현합니다.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Web API 호출
이제 3단계에서 획득한 access_token을 실제로 사용해 보겠습니다.  To-Do List API에 대한 모든 CRUD 요청을 수행하는 웹앱의 `Controllers\TodoListController.cs` 파일을 엽니다.

* 여기서 MSAL을 다시 사용하여 MSAL 캐시에서 access_token을 가져올 수 있습니다.  먼저 MSAL에 대한 `using` 문을 이 파일에 추가합니다.
  
    `using Microsoft.Identity.Client;`
* `Index` 작업에 MSAL의 `AcquireTokenSilentAsync` 메서드를 사용하여 To-Do List 서비스에서 데이터를 읽는 데 사용할 수 있는 access_token을 가져옵니다.

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* 그런 다음 샘플에서는 결과 토큰을 `Authorization` 헤더로 HTTP GET 요청에 추가합니다. To-Do List 서비스는 이 헤더를 사용하여 요청을 인증합니다.
* To-Do List 서비스에서 `401 Unauthorized` 응답을 반환하는 경우 MSAL의 access_token이 어떤 이유로든 잘못된 것입니다.  이 경우 MSAL 캐시에서 모든 access_token을 삭제하고 사용자에게 다시 로그인해야 할 수도 있다는 메시지를 표시해야 합니다. 다시 로그인하면 토큰 획득 흐름이 다시 시작됩니다.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* 마찬가지로, MSAL이 어떤 이유로든 access_token을 반환할 수 없는 경우 다시 로그인하도록 사용자에게 지시해야 합니다.  이는 `MSALException` catch만큼 단순합니다.

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* 정확히 동일한 `AcquireTokenSilentAsync` 호출이 `Create` 및 `Delete` 작업에서 구현됩니다.  웹앱에서 이 MSAL 메서드를 사용하여 앱에 필요할 때마다 access_token을 가져올 수 있습니다.  MSAL이 자동으로 토큰 획득, 캐싱 및 새로 고침을 처리합니다.

마지막으로 앱을 빌드하고 실행합니다.  Microsoft 계정이나 Azure AD 계정으로 로그인하고 위쪽 탐색 모음에 사용자 ID가 반영되는 방식을 확인합니다.  사용자의 할 일 모음에서 일부 항목을 추가 및 삭제하여 OAuth 2.0 보안 API 호출의 작동 방식을 확인합니다.  이제 개인 및 회사/학교 계정으로 사용자를 인증할 수 있는 업계 표준 프로토콜을 사용하여 웹앱 및 Web API가 보안되었습니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip)에 제공됩니다.  

## <a name="next-steps"></a>다음 단계
추가 리소스는 다음을 확인해보세요.

* [개발자 가이드 v2.0 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "azure-active-directory" 태그 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>당사 제품에 대한 보안 업데이트 가져오기
[이 페이지](https://technet.microsoft.com/security/dd252948) 를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.




<!--HONumber=Feb17_HO1-->


