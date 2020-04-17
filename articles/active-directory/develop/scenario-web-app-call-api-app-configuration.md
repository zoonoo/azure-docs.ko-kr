---
title: 웹 API를 호출하는 웹 앱 구성 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 웹 앱의 코드를 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7f05d33b43df85c49a0c92b60157e2a6448325ac
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537137"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>웹 API를 호출하는 웹 앱: 코드 구성

[사용자 시나리오에서 서명하는 웹 앱에](scenario-web-app-sign-user-overview.md) 표시된 것처럼 웹 앱은 [OAuth 2.0 권한 부여 코드 흐름을](v2-oauth2-auth-code-flow.md) 사용하여 사용자를 로그인합니다. 이 흐름에는 다음 두 단계가 있습니다.

1. 권한 부여 코드를 요청합니다. 이 부분은 사용자와의 개인 대화를 Microsoft ID 플랫폼으로 위임합니다. 이 대화 중에 사용자는 웹 API 사용에 서명하고 동의합니다. 개인 대화가 성공적으로 종료되면 웹 앱은 리디렉션 URI에서 권한 부여 코드를 받습니다.
1. 권한 부여 코드를 구속하여 API에 대한 액세스 토큰을 요청합니다.

[사용자 시나리오에서 서명하는 웹 앱은](scenario-web-app-sign-user-overview.md) 첫 번째 단계만 다루었습니다. 여기에서는 사용자에게 로그인할 뿐만 아니라 이제 웹 API를 호출할 수 있도록 웹 앱을 수정하는 방법을 알아봅니다.

## <a name="libraries-that-support-web-app-scenarios"></a>웹 앱 시나리오를 지원하는 라이브러리

MSAL(Microsoft 인증 라이브러리)의 다음 라이브러리는 웹 앱에 대한 권한 부여 코드 흐름을 지원합니다.

| MSAL 라이브러리 | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | .NET 프레임워크 및 .NET 코어 플랫폼 지원. 이러한 플랫폼은 공용 클라이언트 응용 프로그램을 빌드하는 데 사용되기 때문에 지원되지 않는 유니버설 윈도우 플랫폼(UWP), Xamarin.iOS 및 Xamarin.Android는 지원되지 않습니다. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> Python용 MSAL | 파이썬 웹 응용 프로그램에 대한 지원. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Java용 MSAL | Java 웹 응용 프로그램에 대한 지원. |

관심 있는 플랫폼의 탭을 선택합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

사용자 로그인은 오픈 ID 연결(OIDC) 미들웨어에 위임되므로 OIDC 프로세스와 상호 작용해야 합니다. 상호 작용 방법은 사용하는 프레임워크에 따라 다릅니다.

ASP.NET 코어의 경우 미들웨어 OIDC 이벤트를 구독합니다.

- ASP.NET Core에서 Open ID Connect 미들웨어를 통해 권한 부여 코드를 요청할 수 있습니다. ASP.NET 또는 ASP.NET 코어는 사용자가 로그인하고 동의할 수 있도록 합니다.
- 웹 앱을 구독하여 권한 부여 코드를 수신합니다. 이 구독은 C# 대리자를 사용하여 수행됩니다.
- 권한 부여 코드를 받으면 MSAL 라이브러리를 사용하여 사용합니다. 결과 액세스 토큰 및 새로 고침 토큰은 토큰 캐시에 저장됩니다. 캐시는 컨트롤러와 같은 응용 프로그램의 다른 부분에서 자동으로 다른 토큰을 획득하는 데 사용할 수 있습니다.

이 문서의 코드 예제와 다음 예제는 [ASP.NET Core 웹 앱 증분 자습서, 2장에서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)추출됩니다. 전체 구현 세부 정보는 해당 자습서를 참조할 수 있습니다.

> [!NOTE]
> 여기서 코드 예제를 완전히 이해하려면 [ASP.NET 핵심 기본 사항,](https://docs.microsoft.com/aspnet/core/fundamentals)특히 [종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) 및 [옵션에](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)대해 잘 알고 있어야 합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

사용자 로그인은 오픈 ID 연결(OIDC) 미들웨어에 위임되므로 OIDC 프로세스와 상호 작용해야 합니다. 상호 작용 방법은 사용하는 프레임워크에 따라 다릅니다.

ASP.NET 경우, 당신은 미들웨어 OIDC 이벤트를 구독합니다 :

- ASP.NET Core에서 Open ID Connect 미들웨어를 통해 권한 부여 코드를 요청할 수 있습니다. ASP.NET 또는 ASP.NET 코어는 사용자가 로그인하고 동의할 수 있도록 합니다.
- 웹 앱을 구독하여 권한 부여 코드를 수신합니다. 이 구독은 C# 대리자를 사용하여 수행됩니다.
- 권한 부여 코드를 받으면 MSAL 라이브러리를 사용하여 사용합니다. 결과 액세스 토큰 및 새로 고침 토큰은 토큰 캐시에 저장됩니다. 캐시는 컨트롤러와 같은 응용 프로그램의 다른 부분에서 자동으로 다른 토큰을 획득하는 데 사용할 수 있습니다.

이 문서의 코드 예제와 다음 예제는 [ASP.NET 웹 앱 샘플에서](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)추출됩니다. 전체 구현 세부 정보는 해당 샘플을 참조할 수 있습니다.

# <a name="java"></a>[Java](#tab/java)

이 문서의 코드 예제와 다음 예제는 Java용 MSAL을 사용하는 웹 앱 샘플인 [Microsoft Graph를 호출하는 Java](https://github.com/Azure-Samples/ms-identity-java-webapp)웹 응용 프로그램에서 추출됩니다.
이 샘플에서는 현재 Java용 MSAL이 권한 부여 코드 URL을 생성하고 Microsoft ID 플랫폼의 권한 부여 끝점으로 탐색을 처리합니다. Sprint 보안을 사용하여 사용자를 로그인할 수도 있습니다. 전체 구현 세부 정보는 샘플을 참조할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

이 문서의 코드 예제와 다음 예제는 MSAL을 사용하는 웹 앱 샘플인 [Microsoft Graph라고 하는 Python 웹 응용 프로그램에서](https://github.com/Azure-Samples/ms-identity-python-webapp)추출됩니다. 파이썬.
샘플은 현재 MSAL을 허용합니다. 파이썬은 권한 부여 코드 URL을 생성하고 Microsoft ID 플랫폼의 권한 부여 끝점으로 탐색을 처리합니다. 전체 구현 세부 정보는 샘플을 참조할 수 있습니다.

---

## <a name="code-that-redeems-the-authorization-code"></a>권한 부여 코드를 사용할 수 있는 코드

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

ASP.NET 코어에서 `Startup.cs` 파일에서 `OnAuthorizationCodeReceived` OpenID Connect 이벤트를 구독합니다. 이 이벤트에서 MSAL.NET `AcquireTokenFromAuthorizationCode` 메서드를 호출합니다. 이 메서드는 토큰 캐시에 다음 토큰을 저장합니다.

- 요청된 `scopes`에 대한 *액세스 토큰입니다.*
- *새로 고침 토큰*. 이 토큰은 만료가 가까워지면 액세스 토큰을 새로 고치거나 동일한 사용자를 대신하여 다른 리소스에 대해 다른 토큰을 얻는 데 사용됩니다.

[ASP.NET Core 웹 앱 자습서에서는](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) 웹 앱에 대해 재사용 가능한 코드를 제공합니다.

다음은 [Startup.cs#L40-L42의](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42)코드입니다. 그것은 기능을 호출:

- 웹 `AddMicrosoftIdentityPlatformAuthentication` 앱에 인증을 추가하는 메서드입니다.
- 웹 `AddMsal` API 호출 기능을 추가하는 메서드입니다.
- 토큰 `AddInMemoryTokenCaches` 캐시 구현을 선택하는 방법에 대한 메서드입니다.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead`[콘스탄츠.cs#L5에서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)정의됩니다.

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

코드 구성 - 당신은 `AddMicrosoftIdentityPlatformAuthentication` 이미 [사용자에 서명 웹 응용 프로그램에서](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)내용을 공부했습니다 .

### <a name="the-addmsal-method"></a>애드미솔 방법

에 대 `AddMsal` 한 코드는 [Microsoft.Identity.Web/WebAppService컬렉션확장.cs#L108-L159에](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)있습니다.

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

이 `AddMsal` 메서드는 다음을 보장합니다.

- ASP.NET Core 웹 앱은 사용자에 대한 ID 토큰과`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`인증 코드()를 모두 요청합니다.
- `offline_access` 범위가 추가됩니다. 이 범위는 새로 고침 토큰을 얻기 위해 응용 프로그램에 대한 사용자 동의를 얻습니다.
- 응용 프로그램은 OIDC `OnAuthorizationCodeReceived` 이벤트를 구독하고 MSAL.NET 사용하여 호출을 사용하며, 여기에 캡슐화된 재사용 가능한 구성 `ITokenAcquisition`요소 구현으로 캡슐화됩니다.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>토큰 인수.AddaccountToCache로부터 권한 부여코드Async 방법

메서드는 `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145에](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)있습니다. 다음을 보장합니다.

- ASP.NET MSAL.NET ()에`context.HandleCodeRedemption();`병렬로 인증 코드를 사용하지 않습니다.
- ID 토큰의 클레임은 MSAL에서 사용자의 계정에 대한 토큰 캐시 키를 계산하는 데 사용할 수 있습니다.
- 필요한 경우 MSAL.NET 응용 프로그램의 인스턴스가 만들어집니다.
- 코드는 MSAL.NET 응용 프로그램에서 사용됩니다.
- 새 ID 토큰은 `context.HandleCodeRedemption(null, result.IdToken);`을 호출하는 동안 ASP.NET Core와 공유됩니다. 액세스 토큰은 ASP.NET 코어와 공유되지 않습니다. 사용자와 연결된 MSAL.NET 토큰 캐시에 남아 있으며 ASP.NET Core 컨트롤러에서 사용할 준비가 되었습니다.

관련 코드는 `TokenAcquisition`다음과 같습니다.

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>토큰 획득.빌드기밀 클라이언트 응용 프로그램 메서드

ASP.NET Core에서 기밀 클라이언트 응용 프로그램을 빌드하면 `HttpContext`에 있는 정보가 사용됩니다. 요청과 연결된 속성은 `HttpContext` `CurrentHttpContext` 속성을 사용하여 액세스됩니다. `HttpContext`웹 앱의 URL 및 로그인한 사용자(에)에 `ClaimsPrincipal`대한 정보가 있습니다.

이 `BuildConfidentialClientApplication` 메서드는 ASP.NET 코어 구성도 사용합니다. 구성에는 "AzureAD" 섹션이 있으며 다음 요소 모두에 바인딩됩니다.

- 형식의 `_applicationOptions` 데이터 [구조기밀클라이언트응용프로그램](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- ASP.NET `azureAdOptions` 코어에 `Authentication.AzureAD.UI`정의된 [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)형식의 인스턴스입니다.

마지막으로 응용 프로그램은 토큰 캐시를 유지 관리해야 합니다. 자세한 내용은 다음 섹션에서 확인할 수 있습니다.

메서드의 `GetOrBuildConfidentialClientApplication()` 코드는 [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333)에 있습니다. 종속성 주입에 의해 주입된 멤버를 사용합니다(Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59의 `TokenAcquisition` 생성자에서 [전달).](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)

다음은 다음의 코드입니다. `GetOrBuildConfidentialClientApplication`

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>요약

`AcquireTokenByAuthorizationCode`실제로 요청을 ASP.NET 권한 부여 코드를 구속하는 메서드이며 MSAL.NET 사용자 토큰 캐시에 추가되는 토큰을 가져옵니다. 캐시에서 토큰은 ASP.NET 코어 컨트롤러에서 사용됩니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET OpenID Connect의 구성과 `OnAuthorizationCodeReceived` 이벤트 구독이 [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) 파일에서 발생한다는 점을 제외하면 ASP.NET 코어와 유사하게 처리합니다. 개념은 ASP.NET 코어의 개념과 유사하지만 ASP.NET `RedirectUri` [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)에서 를 지정해야 한다는 점을 제외하면 . 이 구성은 응용 프로그램을 배포할 때 변경해야 하기 때문에 ASP.NET Core의 구성보다 약간 덜 강력합니다.

Startup.Auth.cs 대한 코드는 다음과 같습니다.

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

[사용자에게 서명하는 웹 앱: 코드 구성을](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) 참조하여 Java 샘플에서 권한 부여 코드를 받는 방법을 이해합니다. 앱이 코드를 받은 후 [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. `AuthHelper.processAuthenticationCodeRedirect` [AuthHelper.java#L67-L97의](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)메서드에 위임합니다.
1. `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

메서드는 `getAuthResultByAuthCode` [AuthHelper.java#L176에](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)정의되어 있습니다. MSAL을 `ConfidentialClientApplication`만든 다음 권한 `acquireToken()` `AuthorizationCodeParameters` 부여 코드에서 만든 호출을 만듭니다.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

권한 부여 코드 흐름은 [사용자에게 서명하는 웹 앱에](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)표시된 대로 요청됩니다. 그런 다음 FLASK가 `authorized` URL에서 라우팅하는 함수에서 코드를 수신합니다. `/getAToken` 이 코드의 전체 컨텍스트는 [app.py#L30-L44를](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) 참조하십시오.

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

클라이언트 보안 검색대 대신 기밀 클라이언트 응용 프로그램은 클라이언트 인증서 또는 클라이언트 어설션을 사용하여 ID를 증명할 수도 있습니다.
클라이언트 어설션의 사용은 [클라이언트 어설션에](msal-net-client-assertions.md)자세히 설명된 고급 시나리오입니다.

## <a name="token-cache"></a>토큰 캐시

> [!IMPORTANT]
> 웹 앱 또는 웹 API에 대한 토큰 캐시 구현은 [파일 기반인](scenario-desktop-acquire-token.md#file-based-token-cache)데스크톱 응용 프로그램에 대한 구현과 다릅니다.
> 보안 및 성능상의 이유로 웹 앱 및 웹 API의 경우 사용자 계정당 하나의 토큰 캐시가 있는지 확인하는 것이 중요합니다. 각 계정에 대한 토큰 캐시를 직렬화해야 합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 핵심 자습서에서는 종속성 주입을 사용하여 응용 프로그램의 Startup.cs 파일에서 토큰 캐시 구현을 결정할 수 있습니다. Microsoft.Identity.Web에는 토큰 캐시 [직렬화에](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)설명된 미리 빌드된 토큰 캐시 직렬화기가 함께 제공됩니다. 흥미로운 가능성은 코어 분산 [메모리 캐시에](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)ASP.NET 선택하는 것입니다 .

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

토큰 캐시 공급자에 대한 자세한 내용은 [ASP.NET 핵심 웹 앱 자습서 | 참조 토큰은](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) 자습서의 단계를 캐시합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

웹 앱 또는 웹 API에 대한 토큰 캐시 구현은 [파일 기반인](scenario-desktop-acquire-token.md#file-based-token-cache)데스크톱 응용 프로그램에 대한 구현과 다릅니다.

웹 앱 구현은 ASP.NET 세션 또는 서버 메모리를 사용할 수 있습니다. 예를 들어 [MsalAppBuilder.cs#L39-L51에서](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)MSAL.NET 응용 프로그램을 만든 후 캐시 구현이 어떻게 연결되는지 확인하십시오.

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java는 토큰 캐시를 직렬화하고 역직렬화하는 메서드를 제공합니다. Java 샘플은 `getAuthResultBySilentFlow` [AuthHelper.java#L99-L122의](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)메서드에 표시된 대로 세션에서 직렬화를 처리합니다.

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

`SessionManagementHelper` 클래스의 세부 사항은 [Java에 대한 MSAL 샘플에](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)제공됩니다.

# <a name="python"></a>[Python](#tab/python)

Python 샘플에서 각 요청에 대한 기밀 클라이언트 응용 프로그램을 다시 만든 다음 Flask 세션 캐시에서 직렬화하여 계정당 하나의 캐시가 보장됩니다.

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>다음 단계

이 시점에서 사용자가 로그인하면 토큰이 토큰 캐시에 저장됩니다. 웹 앱의 다른 부분에서 어떻게 사용되는지 살펴보겠습니다.

> [!div class="nextstepaction"]
> [웹 API를 호출하는 웹 앱: 전역 로그아웃시 캐시에서 계정 제거](scenario-web-app-call-api-sign-in.md)
