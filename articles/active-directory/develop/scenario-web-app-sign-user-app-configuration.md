---
title: 웹 앱 (코드 구성)-사용자가 로그인 하는 Microsoft id 플랫폼
description: (코드 구성) 사용자가 로그인 하는 웹 앱을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: 82e6cbcd01c87ddffb7eac8d0ea0faef85f41a13
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254000"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>웹 앱 로그인 사용자-코드 구성

로그인 사용자가 웹 앱에 대 한 코드를 구성 하는 방법에 알아봅니다.

## <a name="libraries-used-to-protect-web-apps"></a>웹 앱을 보호 하는 데 사용 되는 라이브러리

<!-- This section can be in an include for Web App and Web APIs -->
웹 앱과 Web API를 보호 하기 위해 사용 하는 라이브러리가 다음과 같습니다.

| 플랫폼 | 라이브러리 | 설명 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [.NET에 대 한 id 모델 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | .NET 용 Microsoft Id 확장을 사용 하는 직접 ASP.NET 및 ASP.NET Core 모두.NET Framework 및.NET Core에서 실행 되는 Dll 집합을 제안 합니다. ASP.NET/ASP.NET Core 웹 앱에서 제어할 수 있습니다. 토큰 유효성 검사를 사용 하 여 **TokenValidationParameters** 클래스 (일부 ISV 시나리오에서 특히) |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 구성

### <a name="application-configuration-files"></a>응용 프로그램 구성 파일

ASP.NET Core 웹 응용 프로그램에 로그인 사용자는 Microsoft id 플랫폼과 상호은 통해 구성 된 `appsettings.json` 파일입니다. 입력 해야 하는 설정은 다음과 같습니다.

- 클라우드 `Instance` 국가별 클라우드에에서 실행 되도록 앱을 원하는 경우
- 대상 그룹 `tenantId`
- `clientId` 응용 프로그램에 대해 Azure portal에서 복사 합니다.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
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

ASP.NET Core에서는 URL이 포함 된 다른 파일 (`applicationUrl`) 및 SSL 포트 (`sslPort`) 응용 프로그램 뿐만 아니라 다양 한 프로필에 대 한 합니다.

```JSon
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

회신 Uri에 등록 해야 하는 Azure portal에는 **인증** 이러한 Url과 일치 해야 하는 응용 프로그램 페이지, 즉, 위의 두 구성 파일에 대 한 것 `https://localhost:44321/signin-oidc` 는 applicationUrl으로 `http://localhost:3110` 하지만 `sslPort` 지정된 (44321)는 및 `CallbackPath` 됩니다 `/signin-oidc` 에 정의 된 대로 `appsettings.json`합니다.
  
같은 방법으로 로그 아웃 URI로 설정 됩니다 `https://localhost:44321/signout-callback-oidc`합니다.

### <a name="initialization-code"></a>초기화 코드

응용 프로그램 초기화를 수행 하는 코드에 ASP.NET Core 웹 앱과 Web Api에는 `Startup.cs` 파일 및 Microsoft Id 플랫폼 (이전 Azure AD) v2.0 사용 하 여 인증을 추가 하려면 다음 코드를 추가 해야 합니다. 코드의 주석을 쉽게 이해할 수 있어야 합니다.

  > [!NOTE]
  > Visual studio 또는 사용 하 여 내에서 기본 ASP.NET core 웹 프로젝트를 사용 하 여 프로젝트를 시작 하는 경우 `dotnet new mvc` 메서드를 `AddAzureAD` 관련된 패키지를 자동으로 로드 되기 때문에 기본적으로 사용할 수 있습니다. 하지만 처음부터 프로젝트 작성 하 고 사용 하려는 경우를 아래 코드 제안 NuGet 패키지를 추가할 수 있습니다 **"Microsoft.AspNetCore.Authentication.AzureAD.UI"** 있도록 프로젝트에는 `AddAzureAD` 메서드를 사용할 수 있습니다.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>ASP.NET 구성

ASP.NET에서 응용 프로그램을 통해 구성 됩니다는 `Web.Config` 파일

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

ASP.NET 웹 앱에서 인증 코드와 관련 된 / 웹 Api에는 `App_Start/Startup.Auth.cs` 파일입니다.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로그인 및 로그 아웃](scenario-web-app-sign-user-sign-in.md)
