---
title: 사용자를 로그인 하는 웹 앱 (코드 구성)-Microsoft identity platform
description: 사용자를 로그인 하는 웹 앱을 빌드하는 방법 알아보기 (코드 구성)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f558ecf583c96f36b8bbee19c7c9cbb2ee57aa31
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596721"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>사용자를 로그인 하는 웹 앱-코드 구성

사용자가 로그인 하는 웹 앱에 대 한 코드를 구성 하는 방법을 알아봅니다.

## <a name="libraries-used-to-protect-web-apps"></a>Web Apps를 보호 하는 데 사용 되는 라이브러리

<!-- This section can be in an include for Web App and Web APIs -->
웹 앱 및 웹 API를 보호 하는 데 사용 되는 라이브러리는 다음과 같습니다.

| 플랫폼 | 라이브러리 | 설명 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [.NET 용 id 모델 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | ASP.NET 및 ASP.NET Core에서 직접 사용 되는 .NET 용 Microsoft Identity Extensions는 .NET Framework와 .NET Core 둘 다에서 실행 되는 Dll 집합을 제안 합니다. ASP.NET/ASP.NET Core 웹 앱에서 **Tokenvalidationparameters** 클래스를 사용 하 여 토큰 유효성 검사를 제어할 수 있습니다 (특히 일부 ISV 시나리오의 경우). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Java 용 MSAL-현재 공개 미리 보기로 제공 됩니다. |
| ![파이썬](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Python 용 MSAL-현재 공개 미리 보기로 제공 됩니다. |

관심 있는 플랫폼에 해당 하는 탭을 선택 합니다.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

이 문서 및 다음 항목의 코드 조각은 [ASP.NET Core 웹 앱 증분 자습서의 1 장](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)에서 추출 되었습니다.

전체 구현에 대 한 자세한 내용은이 자습서를 참조 하는 것이 좋습니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

이 문서 및 다음 항목의 코드 조각은 [ASP.NET 웹 앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) 에서 추출 됩니다.

전체 구현에 대 한 자세한 내용은이 샘플을 참조 하는 것이 좋습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

이 문서의 코드 조각과 다음은 Microsoft graph msal4j 웹 앱 샘플을 호출 하는 [Java 웹 응용 프로그램](https://github.com/Azure-Samples/ms-identity-java-webapp) 에서 추출 됩니다.

전체 구현에 대 한 자세한 내용은이 샘플을 참조 하는 것이 좋습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

이 문서의 코드 조각과 다음은 Microsoft graph msal을 호출 하는 [Python 웹 응용 프로그램](https://github.com/Azure-Samples/ms-identity-python-webapp) 에서 추출 됩니다. Python 웹 앱 샘플

전체 구현에 대 한 자세한 내용은이 샘플을 참조 하는 것이 좋습니다.

---

## <a name="configuration-files"></a>구성 파일

Microsoft id 플랫폼을 사용 하 여 사용자를 로그인 하는 웹 응용 프로그램은 일반적으로 구성 파일을 통해 구성 됩니다. 입력 해야 하는 설정은 다음과 같습니다.

- 앱을 실행 하려는 경우 클라우드 `Instance` (국가 클라우드)
- `tenantId`의 대상
- Azure Portal에서 복사 된 응용 프로그램에 대 한 `clientId`입니다.

경우에 따라 `instance`를 연결 하는 `authority` 응용 프로그램을 매개 변수가 있는 수 있습니다 `tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core 이러한 설정은 [appsettings](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) 파일의 "AzureAD" 섹션에 있습니다.

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

ASP.NET Core에는 응용 프로그램에 대 한 URL (`applicationUrl`) 및 SSL 포트 (`sslPort`)와 다양 한 프로필을 포함 하는 다른 파일 [properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) 있습니다.

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

Azure Portal에서 응용 프로그램에 대 한 **인증** 페이지에 등록 해야 하는 회신 uri는 이러한 url과 일치 해야 합니다. 즉, 위의 두 구성 파일에 대해 applicationUrl이 `http://localhost:3110` 되었지만 `sslPort` (44321)가 지정 된 것 처럼 `https://localhost:44321/signin-oidc` 되며 `/signin-oidc`에 정의 된 대로 `CallbackPath` `appsettings.json` 됩니다.
  
동일한 방식으로 로그 아웃 URI는 `https://localhost:44321/signout-callback-oidc`로 설정 됩니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 응용 프로그램은 Web.config 파일 줄 12-15을 통해 구성 됩니다 [.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15)

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
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

Azure Portal에서 응용 프로그램에 대 한 **인증** 페이지에 등록 해야 하는 회신 uri는 이러한 url과 일치 해야 합니다. `https://localhost:44326/`입니다.

# <a name="javatabjava"></a>[Java](#tab/java)

Java에서 구성은 `src/main/resources` 아래에 있는 [응용 프로그램. 속성](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) 파일에 있습니다.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

Azure Portal에서 응용 프로그램에 대 한 **인증** 페이지에 등록 해야 하는 회신 uri는 응용 프로그램에 정의 된 redirecturis와 일치 해야 합니다 (`http://localhost:8080/msal4jsample/secure/aad` 및 `http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

다음은 app_config의 Python 구성 파일입니다 [. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> 이 빠른 시작은 간소화를 위해 클라이언트 암호를 구성 파일에 저장 하는 것을 제안 합니다. 프로덕션 앱에서 다른 방법을 사용 하 여 KeyVault와 같은 암호를 저장 하거나 Flask의 설명서에 설명 된 대로 환경 변수를 사용 하는 것이 좋습니다 https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>초기화 코드

초기화 코드는 플랫폼에 따라 다릅니다. ASP.NET Core 및 ASP.NET의 경우 사용자 로그인은 OpenIDConnect 미들웨어에 위임 됩니다. 현재 ASP.NET/ASP.NET Core 템플릿은 Azure AD v1.0 끝점에 대 한 웹 응용 프로그램을 생성 합니다. 따라서 v2.0 (Microsoft identity platform) 끝점에 맞게 조정 하려면 약간의 구성이 필요 합니다. Java의 경우 응용 프로그램의 협력을 통해 스프링에 의해 처리 됩니다.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Web Apps (및 Web Api)에서는 컨트롤러나 컨트롤러 작업에 `[Authorize]` 특성이 있으므로 응용 프로그램이 보호 됩니다. 이 특성은 사용자가 인증 되었는지 확인 합니다. 응용 프로그램 초기화를 수행 하는 코드는 `Startup.cs` 파일에 있으며, Microsoft id 플랫폼 (이전의 Azure AD v2.0)으로 인증을 추가 하려면 다음 코드를 추가 해야 합니다. 코드의 주석은 설명이 필요 하지 않습니다.

  > [!NOTE]
  > Visual studio 내에서 기본 ASP.NET core 웹 프로젝트를 사용 하 여 프로젝트를 시작 하거나 `dotnet new mvc`를 사용 하는 경우 관련 패키지가 자동으로 로드 되기 때문에 `AddAzureAD` 메서드를 기본적으로 사용할 수 있습니다.
  > 그러나 프로젝트를 처음부터 빌드하여 아래 코드를 사용 하려는 경우 `AddAzureAD` 메서드를 사용할 수 있도록 프로젝트에 NuGet 패키지 **"AspNetCore"** 를 추가 하는 것이 좋습니다.
  
다음 코드는 시작에서 사용할 수 있습니다 [. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
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

@No__t_0는 [WebAppServiceCollectionExtensions/L23 #](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)에 정의 된 확장 메서드입니다. 메서드

- 인증 서비스를 추가 합니다.
- 구성 파일을 읽는 옵션 구성
- 사용 된 권한이 Microsoft id 플랫폼 (이전의 Azure AD v2.0) 끝점 인 Openid connect connect 옵션을 구성 합니다.
- 토큰 발급자의 유효성을 검사 합니다.
- 이름에 해당 하는 클레임은 ID 토큰의 "preferred_username" 클레임에서 매핑됩니다. 

구성 외에도 `AddMicrosoftIdentityPlatformAuthentication`를 호출할 때 지정할 수 있습니다.

- 구성 섹션의 이름입니다 (기본적으로 AzureAD).
- 인증이 작동 하지 않는 경우 웹 응용 프로그램의 문제를 해결 하는 데 도움이 될 수 있는 OpenIdConnect 미들웨어 이벤트를 추적 하려는 경우 `true` `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` 설정 하면 ASP.NET Core 미들웨어 집합에 따라 정보가 구체화 되는 방법을 보여 줍니다. 는 HTTP 응답에서 `HttpContext.User` 사용자의 id로 진행 됩니다.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
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
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
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

@No__t_0 클래스를 사용 하면 토큰의 발급자가 대부분의 경우 (v1.0 또는 v2.0 토큰, 단일 테 넌 트 또는 다중 테 넌 트 응용 프로그램 또는 사용자에 게 개인 Microsoft 계정을 사용 하 여 로그인 하는 Azure 공용 클라우드 또는 국가별)에서 유효성을 검사할 수 있습니다. 클라우드). [AadIssuerValidator/Resource/](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs) 에서 사용할 수 있습니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 웹 앱/웹 Api의 인증과 관련 된 코드는 [App_Start/Startup](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) 파일에 있습니다.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java 샘플에서는 스프링 프레임 워크를 사용 합니다. 응용 프로그램은 각 HTTP 응답을 가로채는 `Filter`을 구현 하기 때문에 보호 됩니다. Java 웹 앱 빠른 시작에서이 필터는 `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` `AuthFilter` 됩니다. 이 필터는 OAuth 2.0 인증 코드 흐름을 처리 하므로 다음과 같은 작업을 수행 합니다.

- 사용자가 인증 되었는지 확인 합니다 (`isAuthenticated()` 메서드).
- 사용자가 인증 되지 않은 경우 Azure AD 권한 부여 끝점의 url을 계산 하 고 브라우저를이 URI로 리디렉션합니다.
- 응답이 도착 하면 인증 코드 흐름을 포함 하 여 토큰을 msal4j 수 있습니다.
- 마지막으로 토큰 끝점에서 토큰을 수신 하면 (리디렉션 URI) 사용자에 게 로그인 됩니다.

자세한 내용은 Authfilter의 `doFilter()` 메서드를 참조 [하세요.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> @No__t_0 코드는 약간 다른 순서로 작성 되지만 흐름은 설명 된 것과 같습니다.

이 메서드에 의해 트리거되는 인증 코드 흐름에 대 한 자세한 내용은 [Microsoft id 플랫폼 및 OAuth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md) 을 참조 하세요.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 샘플에서는 Flask를 사용 합니다. Flask 및 MSAL 초기화 Python은 [py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28) 에서 수행 됩니다.

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

다음 문서에서는 로그인 및 로그 아웃을 트리거하는 방법을 알아봅니다.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [로그인 및 로그 아웃](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [로그인 및 로그 아웃](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [로그인 및 로그 아웃](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [로그인 및 로그 아웃](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
