---
title: 웹 Api를 호출 하는 웹 앱 구성-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 웹 앱의 코드를 구성 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 68f6f8ec67aca44c89b338287bdd37b6066992e0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207023"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>웹 Api를 호출 하는 웹 앱: 코드 구성

사용자 시나리오에 로그인 [하는 웹 앱](scenario-web-app-sign-user-overview.md) 에 표시 된 것 처럼 웹 앱은 [OAuth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md) 을 사용 하 여 사용자를 로그인 합니다. 이 흐름에는 두 단계가 있습니다.

1. 인증 코드를 요청 합니다. 이 부분은 사용자에 대 한 개인 대화를 Microsoft id 플랫폼에 위임 합니다. 이 대화 상자에서 사용자가 로그인 하 고 web Api를 사용 하 여 동의 합니다. 개인 대화 상자가 성공적으로 종료 되 면 웹 앱은 해당 리디렉션 URI에 대 한 인증 코드를 수신 합니다.
1. 인증 코드를 교환 하 여 API에 대 한 액세스 토큰을 요청 합니다.

[사용자 시나리오에 로그인 하는 웹 앱](scenario-web-app-sign-user-overview.md) 은 첫 번째 단계만 다룹니다. 에서 사용자를 로그인 할 뿐만 아니라 이제 웹 Api를 호출 하도록 웹 앱을 수정 하는 방법을 알아봅니다.

## <a name="libraries-that-support-web-app-scenarios"></a>웹 앱 시나리오를 지 원하는 라이브러리

MSAL (Microsoft 인증 라이브러리)의 다음 라이브러리는 웹 앱에 대 한 권한 부여 코드 흐름을 지원 합니다.

| MSAL 라이브러리 | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | .NET Framework 및 .NET Core 플랫폼을 지원 합니다. 지원 되지 않음 (UWP), Xamarin.ios 및 Xamarin.ios는 공용 클라이언트 응용 프로그램을 빌드하는 데 사용 되기 때문에 유니버설 Windows 플랫폼 (UWP), Xamarin.ios 및 Xamarin.ios입니다. 웹 앱 및 web Api ASP.NET Core MSAL.NET는 더 높은 수준의 라이브러리 (Microsoft. Identity)에 캡슐화 되어 있습니다.|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> Python용 MSAL | Python 웹 응용 프로그램 지원. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Java용 MSAL | Java 웹 응용 프로그램에 대 한 지원. |

관심 있는 플랫폼에 대 한 탭을 선택 합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

웹 앱에서 Microsoft. Identity를 사용할 때 보호 된 Api를 호출할 수 있도록 하려면를 호출 `AddWebAppCallsProtectedWebApi` 하 고 토큰 캐시 serialization 형식만 지정 해야 합니다 (예: 메모리 내 토큰 캐시).

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddSignIn(Configuration, "AzureAd")
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddInMemoryTokenCaches();

    // more code here
}
```

토큰 캐시에 대해 자세히 알아보려면 [토큰 캐시 serialization 옵션](#token-cache) 을 참조 하세요.

> [!NOTE]
> 여기에서 코드 예제를 완전히 이해 하려면 [ASP.NET Core 기본 사항](https://docs.microsoft.com/aspnet/core/fundamentals)에 대해 잘 알고 있어야 하며 특히 [종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) 및 [옵션](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)을 사용 해야 합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

사용자 로그인은 OIDC (Open ID connect) 미들웨어에 위임 되므로 OIDC 프로세스와 상호 작용 해야 합니다. 상호 작용 하는 방법은 사용 하는 프레임 워크에 따라 다릅니다.

ASP.NET의 경우 미들웨어 OIDC 이벤트를 구독 합니다.

- Open ID Connect 미들웨어를 통해 인증 코드를 요청 ASP.NET Core 수 있습니다. ASP.NET 또는 ASP.NET Core를 사용 하 여 사용자가 로그인 하 고 동의할 수 있습니다.
- 웹 앱을 구독 하 여 인증 코드를 수신 합니다. 이 구독은 c # 대리자를 사용 하 여 수행 됩니다.
- 인증 코드를 받으면 MSAL 라이브러리를 사용 하 여 해당 코드를 사용 합니다. 결과 액세스 토큰과 새로 고침 토큰은 토큰 캐시에 저장 됩니다. 컨트롤러와 같은 응용 프로그램의 다른 부분에서 캐시를 사용 하 여 다른 토큰을 자동으로 가져올 수 있습니다.

이 문서의 코드 예제 및 다음은 [ASP.NET 웹 앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)에서 추출 된 코드 예제입니다. 전체 구현에 대 한 자세한 내용은 해당 샘플을 참조 하는 것이 좋습니다.

# <a name="java"></a>[Java](#tab/java)

이 문서의 코드 예제 및 다음 코드 예제는 Java 용 MSAL을 사용 하는 웹 앱 샘플 인 [Microsoft Graph를 호출 하는 java 웹 응용 프로그램](https://github.com/Azure-Samples/ms-identity-java-webapp)에서 추출 됩니다.
이 샘플은 현재 Java 용 MSAL에서 권한 부여 코드 URL을 생성 하 고 Microsoft id 플랫폼에 대 한 권한 부여 끝점 탐색을 처리 합니다. 스 프린트 보안을 사용 하 여에서 사용자에 게 서명할 수도 있습니다. 전체 구현에 대 한 자세한 내용은 샘플을 참조 하는 것이 좋습니다.

# <a name="python"></a>[Python](#tab/python)

이 문서의 코드 예제 및 다음 예제는 MSAL을 사용 하는 웹 앱 샘플 인 Microsoft Graph를 호출 하는 [Python 웹 응용 프로그램](https://github.com/Azure-Samples/ms-identity-python-webapp)에서 추출 됩니다. Python.
이 샘플에서는 현재 MSAL을 사용할 수 있습니다. Python은 권한 부여 코드 URL을 생성 하 고 Microsoft id 플랫폼에 대 한 권한 부여 끝점 탐색을 처리 합니다. 전체 구현에 대 한 자세한 내용은 샘플을 참조 하는 것이 좋습니다.

---

## <a name="code-that-redeems-the-authorization-code"></a>인증 코드를 교환 하는 코드

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Openid connect는 올바른 연결 설정을 설정 하 고, 코드를 받은 이벤트를 구독 하 고, 코드를 교환 하 여 코드를 단순화 합니다. 인증 코드를 교환 하는 데 필요한 추가 코드는 없습니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET는 Openid connect Connect의 구성과 `OnAuthorizationCodeReceived` 이벤트에 대 한 구독이 [App_Start \startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) 파일에서 발생 한다는 점을 제외 하 고 ASP.NET Core와 유사한 작업을 처리 합니다. 이 개념은 ASP.NET `RedirectUri` [# L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)에서를 지정 해야 한다는 점을 제외 하 고는 ASP.NET Core와도 유사 합니다. 이 구성은 응용 프로그램을 배포할 때 변경 해야 하기 때문에 ASP.NET Core 하는 것 보다 약간 더 강력 합니다.

Startup.Auth.cs에 대 한 코드는 다음과 같습니다.

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

[사용자가 로그인 하는 웹 앱:](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) Java 샘플에서 권한 부여 코드를 가져오는 방법을 이해 하려면 코드 구성을 참조 하세요. 앱이 코드를 받은 후에는 [Authfilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. `AuthHelper.processAuthenticationCodeRedirect` [Authhelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)의 메서드에 위임 합니다.
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

메서드 `getAuthResultByAuthCode` 는 [authhelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)에서 정의 됩니다. MSAL `ConfidentialClientApplication`을 만든 다음 인증 코드에서 생성 `acquireToken()` 된 `AuthorizationCodeParameters` 를 사용 하 여를 호출 합니다.

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

[사용자를 로그인 하는 웹 앱: 코드 구성](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)에 표시 된 것 처럼 권한 부여 코드 흐름이 요청 됩니다. 그런 다음 `/getAToken` URL에서 경로를 확인 `authorized` 하는 함수에서 코드를 수신 합니다. 이 코드의 전체 컨텍스트는 [py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) 를 참조 하세요.

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

클라이언트 암호 대신 클라이언트 인증서 또는 클라이언트 어설션을 사용 하 여 기밀 클라이언트 응용 프로그램에서 해당 id를 증명할 수도 있습니다.
클라이언트 어설션을 사용 하는 것은 [클라이언트 어설션에](msal-net-client-assertions.md)자세히 설명 된 고급 시나리오입니다.

## <a name="token-cache"></a>토큰 캐시

> [!IMPORTANT]
> 웹 앱 또는 웹 Api에 대 한 토큰 캐시 구현은 데스크톱 응용 프로그램에 대 한 구현과 다르며, 일반적으로 [파일을 기반](scenario-desktop-acquire-token.md#file-based-token-cache)으로 합니다.
> 보안 및 성능상의 이유로, 웹 앱 및 웹 Api의 경우 사용자 계정 마다 하나의 토큰 캐시가 있는지 확인 하는 것이 중요 합니다. 각 계정에 대 한 토큰 캐시를 직렬화 해야 합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET core 자습서에서는 종속성 주입을 사용 하 여 응용 프로그램에 대 한 Startup.cs 파일에서 토큰 캐시 구현을 결정할 수 있습니다. Microsoft. Identity. Web에는 [토큰 캐시 serialization](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)에 설명 된 미리 작성 된 토큰 캐시 serializer가 제공 됩니다. ASP.NET Core [분산 메모리 캐시](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)를 선택 하는 것이 좋습니다.

```csharp
// Use a distributed token cache by adding:
    services.AddSignIn(Configuration, "AzureAd");
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
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

토큰 캐시 공급자에 대 한 자세한 내용은 [ASP.NET Core 웹 앱 자습서를 참조 하세요. 자습서의 토큰 캐시](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) 단계입니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

웹 앱 또는 웹 Api에 대 한 토큰 캐시 구현은 데스크톱 응용 프로그램에 대 한 구현과 다르며, 일반적으로 [파일을 기반](scenario-desktop-acquire-token.md#file-based-token-cache)으로 합니다.

웹 앱 구현에서는 ASP.NET 세션 또는 서버 메모리를 사용할 수 있습니다. 예를 들어 Msalappbuilder에서 MSAL.NET 응용 프로그램을 만든 후 캐시 구현이 후크 되는 방법을 확인 [합니다. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

MSAL Java는 토큰 캐시를 serialize 및 deserialize 하는 메서드를 제공 합니다. Java 샘플은 Authhelper의 `getAuthResultBySilentFlow` 메서드에 표시 된 것 처럼 세션에서 직렬화를 처리 합니다 [. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

`SessionManagementHelper` 클래스의 세부 정보는 [Java 용 msal 샘플](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)에서 제공 됩니다.

# <a name="python"></a>[Python](#tab/python)

Python 샘플에서 각 요청에 대 한 기밀 클라이언트 응용 프로그램을 다시 만들고 Flask 세션 캐시에서 serialize 하 여 계정 당 하나의 캐시를 확인 합니다.

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

이 시점에서 사용자가 로그인 하면 토큰이 토큰 캐시에 저장 됩니다. 웹 앱의 다른 부분에서 사용 되는 방법을 살펴보겠습니다.

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 웹 앱: 전역 로그 아웃 시 캐시에서 계정 제거](scenario-web-app-call-api-sign-in.md)
