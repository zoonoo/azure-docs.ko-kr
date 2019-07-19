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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c962e95b3d213c4089b51f58139cab17a3332cbd
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853061"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>사용자를 로그인 하는 웹 앱-코드 구성

사용자가 로그인 하는 웹 앱에 대 한 코드를 구성 하는 방법을 알아봅니다.

## <a name="libraries-used-to-protect-web-apps"></a>Web Apps를 보호 하는 데 사용 되는 라이브러리

<!-- This section can be in an include for Web App and Web APIs -->
웹 앱 및 웹 API를 보호 하는 데 사용 되는 라이브러리는 다음과 같습니다.

| 플랫폼 | Library | 설명 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [.NET 용 id 모델 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | ASP.NET 및 ASP.NET Core에서 직접 사용 되는 .NET 용 Microsoft Identity Extensions는 .NET Framework와 .NET Core 둘 다에서 실행 되는 Dll 집합을 제안 합니다. ASP.NET/ASP.NET Core 웹 앱에서 **Tokenvalidationparameters** 클래스를 사용 하 여 토큰 유효성 검사를 제어할 수 있습니다 (특히 일부 ISV 시나리오의 경우). |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 구성

이 문서 및 다음 항목의 코드 조각은 [ASP.NET Core 웹 앱 증분 자습서의 1 장](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)에서 추출 되었습니다. 전체 구현에 대 한 자세한 내용은 해당 자습서를 참조 하는 것이 좋습니다.

### <a name="application-configuration-files"></a>응용 프로그램 구성 파일

ASP.NET Core에서 Microsoft id 플랫폼을 사용 하는 웹 응용 프로그램 로그인 사용자는 `appsettings.json` 파일을 통해 구성 됩니다. 입력 해야 하는 설정은 다음과 같습니다.

- 앱이 `Instance` 국가 클라우드에서 실행 되도록 하려면 클라우드
- 대상 사용자`tenantId`
- Azure Portal `clientId` 에서 복사 된 응용 프로그램에 대 한입니다.

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

ASP.NET Core에는 응용 프로그램에 대 한 URL (`applicationUrl`) 및 SSL 포트 (`sslPort`)와 다양 한 프로필을 포함 하는 다른 파일이 있습니다.

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

Azure Portal에서 응용 프로그램에 대 한 **인증** 페이지에 등록 해야 하는 회신 uri는 이러한 url과 일치 해야 합니다. 즉, `https://localhost:44321/signin-oidc` 위의 두 구성 파일에 대해 applicationurl은이 `/signin-oidc` `http://localhost:3110` `sslPort` 고는 (44321)가 지정 되 고 `CallbackPath` 는에 정의 `appsettings.json`된 것입니다.
  
동일한 방식으로, 로그 아웃 URI는로 `https://localhost:44321/signout-callback-oidc`설정 됩니다.

### <a name="initialization-code"></a>초기화 코드

ASP.NET Core Web Apps (및 Web api)에서 응용 프로그램 초기화를 수행 하는 코드는 `Startup.cs` 파일에 있으며, Microsoft id 플랫폼 (이전의 Azure AD) v 2.0을 사용 하 여 인증을 추가 하려면 다음 코드를 추가 해야 합니다. 코드의 주석은 설명이 필요 하지 않습니다.

  > [!NOTE]
  > Visual studio 내에서 기본 ASP.NET core 웹 프로젝트를 사용 하 여 프로젝트를 시작 `dotnet new mvc` 하거나 메서드 `AddAzureAD` 를 사용 하는 경우 관련 패키지가 자동으로 로드 되기 때문에 기본적으로 사용할 수 있습니다. 그러나 프로젝트를 처음부터 빌드하여 아래 코드를 사용 하려는 경우에는 NuGet 패키지 **"AspNetCore"** 를 프로젝트에 추가 하 여 메서드를 `AddAzureAD` 사용할 수 있도록 하는 것이 좋습니다.
  
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

ASP.NET에서 응용 프로그램은 `Web.Config` 파일을 통해 구성 됩니다.

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

ASP.NET 웹 앱/웹 api의 인증과 관련 된 코드는 `App_Start/Startup.Auth.cs` 파일에 있습니다.

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
