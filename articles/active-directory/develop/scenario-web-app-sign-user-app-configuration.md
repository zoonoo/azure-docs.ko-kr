---
title: 사용자에 서명하는 웹 앱 구성 - Microsoft ID 플랫폼 | Azure
description: 사용자에게 서명하는 웹 앱을 빌드하는 방법 알아보기(코드 구성)
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
ms.openlocfilehash: 3cb51a57baa87849e97f7b05762dc4d6eba787a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537114"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>사용자에 서명하는 웹 앱: 코드 구성

사용자에게 로그인하는 웹 앱의 코드를 구성하는 방법을 알아봅니다.

## <a name="libraries-for-protecting-web-apps"></a>웹 앱 보호를 위한 라이브러리

<!-- This section can be in an include for web app and web APIs -->
웹 앱(및 웹 API)을 보호하는 데 사용되는 라이브러리는 다음과 같습니다.

| 플랫폼 | 라이브러리 | Description |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [.NET에 대한 ID 모델 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | ASP.NET 및 ASP.NET 코어에서 직접 사용하는 .NET에 대한 Microsoft ID 모델 확장은 .NET 프레임워크와 .NET Core 모두에서 실행되는 DLL 집합을 제안합니다. ASP.NET 또는 ASP.NET 코어 웹 앱에서 **TokenValidationParameters** 클래스(특히 일부 파트너 시나리오)를 사용하여 토큰 유효성 검사를 제어할 수 있습니다. |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Java 웹 응용 프로그램 지원 |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | 파이썬 웹 응용 프로그램 지원 |

관심 있는 플랫폼에 해당하는 탭을 선택합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이 문서와 다음의 코드 조각은 ASP.NET [Core 웹 앱 증분 자습서, 1장에서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)추출됩니다.

전체 구현 세부 정보는 이 자습서를 참조할 수 있습니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

이 문서와 다음의 코드 조각은 ASP.NET [웹 앱 샘플에서](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)추출됩니다.

전체 구현 세부 정보는 이 샘플을 참조할 수 있습니다.

# <a name="java"></a>[Java](#tab/java)

이 문서와 다음의 코드 조각은 MSAL Java에서 Microsoft 그래프 샘플을 [호출하는 Java 웹 응용 프로그램에서](https://github.com/Azure-Samples/ms-identity-java-webapp) 추출됩니다.

전체 구현 세부 정보는 이 샘플을 참조할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

이 문서와 다음의 코드 조각은 MSAL 파이썬에서 Microsoft 그래프 샘플을 [호출하는 파이썬 웹 응용 프로그램에서](https://github.com/Azure-Samples/ms-identity-python-webapp) 추출됩니다.

전체 구현 세부 정보는 이 샘플을 참조할 수 있습니다.

---

## <a name="configuration-files"></a>구성 파일

Microsoft ID 플랫폼을 사용하여 사용자를 로그인하는 웹 응용 프로그램은 일반적으로 구성 파일을 통해 구성됩니다. 입력해야 하는 설정은 다음과 같습니다.

- 예를 들어`Instance`앱이 국가 클라우드에서 실행되도록 하려는 경우 클라우드 인스턴스 ()
- 테넌트 ID()`TenantId`
- Azure 포털에서`ClientId`복사된 응용 프로그램의 클라이언트 ID()

경우에 따라 응용 프로그램을 에 의해 `Authority`매개 변수화할 수 `Instance` `TenantId`있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 코어에서 이러한 설정은 [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) 파일, "AzureAd" 섹션에 있습니다.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

ASP.NET 코어에서 다른 파일[(속성\launchSettings.json)에는](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)응용 프로그램 및 다양한 프로필에 대한 URL (속성\launchSettings.json)과`applicationUrl`TLS / SSL 포트 ()가`sslPort`포함되어 있습니다.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Azure 포털에서 응용 프로그램의 **인증** 페이지에 등록해야 하는 회신 URI가 이러한 URL과 일치해야 합니다. 앞의 두 구성 파일의 경우 `https://localhost:44321/signin-oidc`. 그 이유는 `applicationUrl` `http://localhost:3110`,하지만 `sslPort` 지정 (44321)입니다. `CallbackPath`is는 `/signin-oidc`에 `appsettings.json`정의된 대로 입니다.

마찬가지로 로그아웃 URI는 `https://localhost:44321/signout-callback-oidc`로 설정됩니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 응용 프로그램은 [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) 파일(줄 12~15)을 통해 구성됩니다.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Azure 포털에서 응용 프로그램의 **인증** 페이지에 등록해야 하는 회신 URI가 이러한 URL과 일치해야 합니다. 즉, 그들은해야한다 `https://localhost:44326/`.

# <a name="java"></a>[Java](#tab/java)

Java에서 구성은 아래에 `src/main/resources`있는 [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) 파일에 있습니다.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Azure 포털에서 응용 프로그램에 대해 **인증** 페이지에 등록해야 하는 회신 URI는 `redirectUri` 응용 프로그램이 정의하는 인스턴스와 일치해야 합니다. 즉, 그들은해야한다 `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me`.

# <a name="python"></a>[Python](#tab/python)

다음은 [app_config.py의](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)파이썬 구성 파일입니다.

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> 이 빠른 시작은 단순화를 위해 구성 파일에 클라이언트 비밀을 저장하는 것을 제안합니다. 프로덕션 앱에서 [Flask 설명서에](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)설명된 대로 키 자격 증명 모음 또는 환경 변수와 같은 다른 방법을 사용하여 비밀을 저장하는 것이 좋습니다.
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>초기화 코드

초기화 코드는 플랫폼에 따라 다릅니다. ASP.NET 코어 및 ASP.NET 경우 사용자의 로그인이 OpenID Connect 미들웨어에 위임됩니다. ASP.NET 또는 ASP.NET 코어 템플릿은 Azure Active Directory(Azure AD) v1.0 끝점에 대한 웹 응용 프로그램을 생성합니다. 일부 구성은 Microsoft ID 플랫폼(v2.0) 끝점에 맞게 조정해야 합니다. Java의 경우 응용 프로그램의 협조를 받아 Spring에서 처리합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 코어 웹 앱(및 웹 API)에서는 컨트롤러 또는 `[Authorize]` 컨트롤러 작업에 특성이 있기 때문에 응용 프로그램이 보호됩니다. 이 특성은 사용자가 인증되어 있는지 확인합니다. 응용 프로그램을 초기화하는 코드는 Startup.cs 파일에 있습니다.

Microsoft ID 플랫폼(이전의 Azure AD v2.0)으로 인증을 추가하려면 다음 코드를 추가해야 합니다. 코드의 주석은 설명이 있어야 합니다.

> [!NOTE]
> Visual studio 내에서 기본 ASP.NET Core 웹 프로젝트를 사용하거나 `dotnet new mvc`을 `AddAzureAD` 사용하여 프로젝트를 시작하는 경우 기본적으로 메서드를 사용할 수 있습니다. 관련 패키지가 자동으로 로드되기 때문입니다.
>
> 프로젝트를 처음부터 빌드하고 다음 코드를 사용하려는 경우 NuGet 패키지 **Microsoft.AspNetCore.Authentication.AzureAD.UI를** 프로젝트에 추가하여 메서드를 `AddAzureAD` 사용할 수 있도록 하는 것이 좋습니다.

다음 코드는 [Startup.cs#L33-L34에서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)사용할 수 있습니다.

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

`AddMicrosoftIdentityPlatformAuthentication` 확장 방법은 [Microsoft.Identity.Web/WebAppServiceCollectionExtension.cs#L23에 정의되어](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)있습니다. 그것은 :

- 인증 서비스를 추가합니다.
- 구성 파일을 읽을 수 있는 옵션을 구성합니다.
- 사용 된 기관이 Microsoft ID 플랫폼 (이전 Azure AD v2.0) 끝점이 되도록 OpenID 연결 옵션을 구성 합니다.
- 토큰 발급자의 유효성을 검사합니다.
- 이름에 해당하는 클레임이 ID 토큰의 `preferred_username` 클레임에서 매핑되도록 합니다.

구성 외에도 을 호출할 `AddMicrosoftIdentityPlatformAuthentication`때 구성 섹션의 이름을 지정할 수 있습니다. 기본적으로 . `AzureAd`

OpenId Connect 미들웨어 이벤트를 추적하면 인증이 작동하지 않는 경우 웹 응용 프로그램 문제를 해결하는 데 도움이 될 수 있습니다. `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` 설정은 `true` 에서 사용자의 ID에 대한 HTTP 응답에서 진행되는 ASP.NET Core 미들웨어 집합에 `HttpContext.User`의해 정보가 정교해지는 방법을 보여줍니다.

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

클래스를 `AadIssuerValidator` 사용하면 토큰 발급자의 유효성을 검사할 수 있습니다. 이 클래스는 v1.0 또는 v2.0 토큰, 단일 테넌트 또는 다중 테넌트 응용 프로그램 또는 Azure 퍼블릭 클라우드 또는 국가 클라우드에서 개인 Microsoft 계정으로 사용자에 서명하는 응용 프로그램과 함께 작동합니다. [Microsoft.Identity.Web/리소스/AadIssuerValidator.cs에서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)사용할 수 있습니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 웹 앱 및 웹 API의 인증과 관련된 코드는 [App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) 파일에 있습니다.

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Java 샘플은 스프링 프레임워크를 사용합니다. 각 HTTP 응답을 가로채는 필터를 구현하기 때문에 응용 프로그램이 보호됩니다. Java 웹 앱의 빠른 시작에서 `AuthFilter` 이 `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`필터는 에 있습니다.

필터는 OAuth 2.0 권한 부여 코드 흐름을 처리하고 사용자가`isAuthenticated()` 인증되었는지 확인합니다(메서드). 사용자가 인증되지 않은 경우 Azure AD 권한 부여 끝점의 URL을 계산하고 브라우저를 이 URI로 리디렉션합니다.

응답이 도착하면 권한 부여 코드를 포함하면 MSAL Java를 사용하여 토큰을 획득합니다. 마지막으로 토큰 끝점(리디렉션 URI)에서 토큰을 받으면 사용자가 로그인됩니다.

자세한 내용은 `doFilter()` [AuthFilter.java의](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)메서드를 참조하십시오.

> [!NOTE]
> 의 `doFilter()` 코드는 약간 다른 순서로 작성되지만 흐름은 설명된 코드입니다.

이 메서드가 트리거하는 권한 부여 코드 흐름에 대한 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 권한 부여 코드 흐름을](v2-oauth2-auth-code-flow.md)참조하십시오.

# <a name="python"></a>[Python](#tab/python)

파이썬 샘플은 플라스크를 사용합니다. 플라스크와 MSAL 파이썬의 초기화는 [app.py#L1-L28에서](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)수행됩니다.

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>다음 단계

다음 문서에서는 로그인 및 로그아웃을 트리거하는 방법을 배웁니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
