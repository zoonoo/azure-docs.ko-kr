---
title: 웹 앱 호출 web Api (코드 구성-)는 Microsoft id 플랫폼
description: 웹 앱을 빌드하는 방법을 알아봅니다 호출 web Api (응용 프로그램의 코드 구성)는
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd7f393f889facf147cf25625d5c3b20f886ddf5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784933"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>호출 웹 Api 코드 구성 되는 웹 앱

에 표시 된 대로 [웹 앱 로그인 사용자 시나리오](scenario-web-app-sign-user-overview.md)에 Open ID를 위임 된 사용자를 로그인 connect (OIDC) 미들웨어, OIDC 프로세스에서 후크 하는 데 사용할 수 있습니다. 작업을 수행 하는 방법은 프레임 워크에 따라 다릅니다. 있습니다 사용 (여기 ASP.NET 및 ASP.NET Core) 미들웨어 OIDC 이벤트를 구독할 것을 하지만 합니다. 원리는 다음과 같습니다.

- 하면 사용할 수 있게 ASP.NET 또는 ASP.NET core 권한 부여 코드를 요청 합니다. ASP.NET/ASP.NET 코어는 수행 하 여 사용자가 로그인 하 고 동의 하면 수 있도록
- 웹 앱에서 권한 부여 코드 수신에 가입할 수 있습니다.
- 인증 코드를 수신 하면 MSAL 라이브러리 코드 및 결과 액세스 토큰을 사용 하 고 토큰 캐시의 저장소 토큰 새로 고침을 사용 합니다. 여기에서 캐시 수 응용 프로그램의 다른 부분에서 자동으로 다른 토큰을 획득 합니다.

## <a name="libraries-supporting-web-app-scenarios"></a>웹 앱 시나리오를 지 원하는 라이브러리

웹 앱에 대 한 권한 부여 코드 흐름을 지 원하는 라이브러리는 다음과 같습니다.

| MSAL 라이브러리 | 설명 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 지원 되는 플랫폼은.NET Framework 및.NET Core 플랫폼 (UWP 없습니다, Xamarin.iOS 및 Xamarin.Android 플랫폼으로 하는 데 공용 클라이언트 응용 프로그램 빌드) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 진행 중-공개 미리 보기에서 개발 |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 진행 중-공개 미리 보기에서 개발 |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 구성

ASP.NET Core에서의 작업이 수행 된 `Startup.cs` 파일입니다. 구독 하는 것이 좋습니다는 `OnAuthorizationCodeReceived` ID 열 이벤트를 연결 하 고이 이벤트에서 MSAL을 호출 합니다. NET의 메서드 `AcquireTokenFromAuthorizationCode` 토큰 캐시, 요청 된 범위에 대 한 액세스 토큰 및 만료에 가까워질 때 액세스 토큰 새로 고침 또는 동일한 사용자를 대신해 서 토큰을 가져오려면 사용할 새로 고침 토큰을 저장 하는 효과가 있는 에 있지만 다른 리소스에 대 한 합니다.

아래 코드의 주석의 위빙을 MSAL.NET 및 ASP.NET Core의 몇 가지 까다로운 측면을 이해 하는 데 도움이 됩니다.

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
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

ASP.NET core에서는 기밀 클라이언트 응용 프로그램 빌드 HttpContext에 있는 정보를 사용 합니다. 웹 앱에서 로그인 한 사용자를 인식 된 URL이이 HttpContext (에 `ClaimsPrincipal`). 또한 사용 하 여 ASP.NET Core 구성에는 "azure Ad" 섹션에는 및에 바인딩된는 `_applicationOptions` 데이터 구조입니다. 마지막으로 응용 프로그램 토큰 캐시를 유지 해야 합니다.

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
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

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
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` 실제로 ASP.NET에서 요청한 권한 부여 코드를 교환 하 고 MSAL.NET 사용자 토큰 캐시에 추가 되는 토큰을 가져옵니다. 여기에서 하 한 ASP.NET Core 컨트롤러에서 사용 합니다.

## <a name="aspnet-configuration"></a>ASP.NET 구성

ASP.NET 작업을 처리 하는 방법은 비슷합니다 점을 제외 하 고 OpenIdConnect 및 구독을 구성 합니다 `OnAuthorizationCodeReceived` 이벤트가 발생 하면는 `App_Start\Startup.Auth.cs` 파일입니다. 알게 비슷한 개념 RedirectUri 약간 작은 경우 구성 파일을 지정 해야 하는 여기는 점을 제외 하 고 강력한:

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
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
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

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>(코어) ASP.NET 웹 앱에 대 한 MSAL.NET 토큰 캐시

웹 앱, 웹 Api의 팩트를 토큰 캐시 구현을 다릅니다 데스크톱 응용 프로그램 토큰 캐시 구현을 (하는 경우가 많습니다 [기반 파일](scenario-desktop-acquire-token.md#file-based-token-cache)합니다. ASP.NET/ASP.NET Core 세션 또는 Redis cache를 또는 데이터베이스 또는 Azure Blob 저장소도 사용할 수 있습니다. 코드에서이 코드 조각은의 개체는 `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` 캐시 서비스를 바인딩하는 메서드 호출 합니다. 세부 정보는 같습니다.이 시나리오 가이드에서 다루지 않지만 아래의 링크를 클릭 발생 합니다.

> [!IMPORTANT]
> 매우 중요 한 사항은 웹 앱과 web Api에 있어야 한다는 사용자 (계정) 당 하나의 토큰 캐시 됩니다. 각 계정의 토큰 캐시를 직렬화해야 합니다.

토큰 캐시를 사용 하 여 웹 앱 및 웹 Api에 대 한 방법의 예제에서 사용할 수는 [ASP.NET Core 웹 앱 자습서](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 단계의 [2-2 토큰 캐시](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)합니다. 구현 방법은 [Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 폴더에 있는 [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) 라이브러리의 [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) 폴더를 살펴보세요.

## <a name="next-steps"></a>다음 단계

이 시점에서 때 사용자 로그인 토큰을 토큰 캐시에 저장 됩니다. 웹 앱의 다른 부분에 다음 사용 방법 확인해 보겠습니다.

> [!div class="nextstepaction"]
> [웹 앱에 로그인](scenario-web-app-call-api-sign-in.md)
