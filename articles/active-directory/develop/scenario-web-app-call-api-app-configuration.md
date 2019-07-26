---
title: 웹 Api를 호출 하는 웹 앱 (코드 구성)-Microsoft identity platform
description: 웹 Api를 호출 하는 웹 앱을 빌드하는 방법에 대해 알아봅니다 (앱 코드 구성).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad995908ff20d123a77b511d127652aa17c4634
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494523"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>웹 Api를 호출 하는 웹 앱-코드 구성

[웹 앱 로그인 사용자 시나리오](scenario-web-app-sign-user-overview.md)에서 볼 수 있듯이 사용자 로그인이 Oidc (Open ID connect) 미들웨어에 위임 되 면 oidc 프로세스에서 후크 할 수 있습니다. 이 작업을 수행 하는 방법은 사용 하는 프레임 워크 (여기서는 ASP.NET 및 ASP.NET Core)에 따라 다르지만, 결과적으로 미들웨어 OIDC 이벤트를 구독 합니다. 원리는 다음과 같습니다.

- ASP.NET 또는 ASP.NET core가 인증 코드를 요청 하도록 허용 합니다. 이 ASP.NET/ASP.NET core를 사용 하면 사용자가 로그인 하 여 동의할 수 있습니다.
- 웹 앱에의 한 권한 부여 코드 수신을 구독 합니다.
- 인증 코드를 받으면 MSAL 라이브러리를 사용 하 여 코드와 결과 액세스 토큰을 교환 하 고 토큰 캐시에서 토큰 저장소를 새로 고칩니다. 여기서 캐시는 응용 프로그램의 다른 부분에서 다른 토큰을 자동으로 획득 하는 데 사용할 수 있습니다.

> [!NOTE]
> 이 문서의 코드 조각은 완전히 작동 하는 GitHub의 다음 샘플에서 추출 됩니다.
>
> - [ASP.NET Core 웹 앱 증분 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)
> - [ASP.NET 웹 앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

## <a name="libraries-supporting-web-app-scenarios"></a>웹 앱 시나리오를 지 원하는 라이브러리

Web Apps에 대 한 권한 부여 코드 흐름을 지 원하는 라이브러리는 다음과 같습니다.

| MSAL 라이브러리 | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 지원 되는 플랫폼은 .NET Framework 및 .NET Core 플랫폼입니다 (이러한 플랫폼은 공용 클라이언트 응용 프로그램을 빌드하는 데 사용 되므로 UWP, Xamarin.ios 및 Xamarin.ios가 아님). |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 개발 진행 중-공개 미리 보기 |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 개발 진행 중-공개 미리 보기 |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 구성

ASP.NET Core 파일에서 발생 하는 `Startup.cs` 작업입니다. `OnAuthorizationCodeReceived` Open ID connect 이벤트를 구독 하 고이 이벤트에서 msal을 호출 하려고 합니다. 토큰 캐시에 `AcquireTokenFromAuthorizationCode` 저장 하는 데 영향을 주는 NET의 메서드, 요청 `scopes`된에 대 한 액세스 토큰 및 만료에 근접 한 경우 액세스 토큰을 새로 고치는 데 사용 되는 새로 고침 토큰 및 동일한 사용자를 대신 하 여 토큰을 가져오는 데 사용 되는 새로 고침 토큰 다른 리소스의 경우

```CSharp
string[] scopes = new string[]{ "user.read" };
string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };
```

아래 코드의 주석은 weaving MSAL.NET 및 ASP.NET Core의 몇 가지 복잡 한 측면을 이해 하는 데 도움이 됩니다. 전체 세부 정보는 [ASP.NET Core 웹 앱 증분 자습서, 2 장](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) 에서 제공 됩니다.

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add("offline_access");
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

ASP.NET Core에서 기밀 클라이언트 응용 프로그램 빌드는 HttpContext에 있는 정보를 사용 합니다. 이 `HttpContext` 는 웹 앱에 대 한 URL과 로그인 한 사용자 ( `ClaimsPrincipal`)를 알고 있습니다. 

또한 "AzureAD" 섹션을 포함 하 고 둘 다에 바인딩되는 ASP.NET Core 구성을 사용 합니다.

- `_applicationOptions` [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) 형식의 데이터 구조
- ASP.NET Core `azureAdOptions` 에`Authentication.AzureAD.UI`정의 된 [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) 형식의 인스턴스입니다. 마지막으로 응용 프로그램은 토큰 캐시를 유지 관리 해야 합니다.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _applicationOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (UserTokenCacheProvider != null)
 {
  UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (AppTokenCacheProvider != null)
 {
  AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

토큰 캐시 공급자에 대 한 자세한 내용은 [ASP.NET Core 웹 앱 자습서를 참조 하세요. 토큰 캐시](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/455d32f09f4f6647b066ebee583f1a708376b12f/2-WebApp-graph-user/2-2-TokenCache)

> [!NOTE]
> `AcquireTokenByAuthorizationCode`ASP.NET에 의해 요청 된 인증 코드를 교환 하 고 MSAL.NET 사용자 토큰 캐시에 추가 되는 토큰을 가져옵니다. 그런 다음 ASP.NET Core 컨트롤러에서 사용 됩니다.

## <a name="aspnet-configuration"></a>ASP.NET 구성

Openidconnect의 구성과 `OnAuthorizationCodeReceived` 이벤트에 대 한 구독이 `App_Start\Startup.Auth.cs` 파일에서 발생 한다는 점을 제외 하 고 ASP.NET는 항목을 처리 하는 방법이 비슷합니다. 여기서는 구성 파일에 RedirectUri를 지정 해야 한다는 점을 제외 하 고는 비슷한 개념을 확인할 수 있습니다.

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

마지막으로, 클라이언트 암호 대신 클라이언트 인증서 또는 클라이언트 어설션을 사용 하 여 기밀 클라이언트 응용 프로그램에서 해당 id를 증명할 수 있습니다.
클라이언트 어설션을 사용 하는 것은 [클라이언트 어설션에](msal-net-client-assertions.md) 자세히 설명 된 고급 시나리오입니다.

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>ASP.NET (Core) 웹 앱에 대 한 MSAL.NET Token cache

웹 앱 (또는 팩트의 웹 Api)에서 토큰 캐시 구현은 데스크톱 응용 프로그램 토큰 캐시 구현 (일반적으로 [파일 기반](scenario-desktop-acquire-token.md#file-based-token-cache))과 다릅니다. ASP.NET/ASP.NET Core 세션, Redis 캐시, 데이터베이스 또는 Azure Blob storage를 사용할 수 있습니다. 위의 코드 조각에서이는 캐시 서비스를 바인딩하는 `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` 메서드 호출의 개체입니다. 여기에서 발생 하는 작업에 대 한 자세한 내용은이 시나리오 가이드의 범위를 벗어나는 것 이지만 링크는 아래에 제공 되어 있습니다.

> [!IMPORTANT]
> 웹 앱 및 웹 Api의 경우 사용자 마다 토큰 캐시가 하나씩 있어야 합니다 (계정 당). 각 계정의 토큰 캐시를 직렬화해야 합니다.

웹 앱 및 웹 Api에 토큰 캐시를 사용 하는 방법에 대 한 예제는 [2-2 단계 토큰 캐시](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)의 [ASP.NET Core 웹 앱 자습서](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 에서 제공 됩니다. 구현 방법은 [Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 폴더에 있는 [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) 라이브러리의 [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) 폴더를 살펴보세요.

## <a name="next-steps"></a>다음 단계

이 시점에서 토큰 캐시에 사용자가 로그인 하면 토큰 캐시에 저장 됩니다. 웹 앱의 다른 부분에서 사용 되는 방법을 살펴보겠습니다.

> [!div class="nextstepaction"]
> [웹 앱에 로그인 합니다.](scenario-web-app-call-api-sign-in.md)
