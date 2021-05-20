---
title: 보호된 웹 API 앱 구성 | Azure
titleSuffix: Microsoft identity platform
description: 보호된 웹 API를 빌드하고 애플리케이션의 코드를 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5206c2295ee7c01b4a2908e59da1cfdd8782bccd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517721"
---
# <a name="protected-web-api-code-configuration"></a>보호된 웹 API: 코드 구성

보호된 웹 API에 대한 코드를 구성하려면 다음을 이해합니다.

- API를 보호된 항목으로 정의하는 것
- 전달자 토큰을 구성하는 방법
- 토큰의 유효성을 검사하는 방법

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>ASP.NET 및 ASP.NET Core API를 보호된 항목으로 정의하는 것은 무엇인가요?

웹 앱과 마찬가지로 ASP.NET 및 ASP.NET Core 웹 API도 해당 컨트롤러 작업에 **[권한 부여]** 특성이 접두사로 추가되기 때문에 보호됩니다. 권한 있는 ID를 사용하여 API를 호출하는 경우에만 컨트롤러 작업을 호출할 수 있습니다.

다음과 같은 질문을 고려해보세요.

- 앱만 웹 API를 호출할 수 있습니다. API를 호출하는 앱의 ID를 API가 어떻게 알 수 있나요?
- 앱이 사용자를 대신하여 API를 호출하는 경우 사용자의 ID는 무엇인가요?

## <a name="bearer-token"></a>전달자 토큰

앱이 호출될 때 헤더에 설정된 전달자 토큰에는 앱 ID에 대한 정보가 포함됩니다. 웹앱이 디먼 앱에서 서비스 간 호출을 허용하지 않는 한, 사용자에 대한 정보도 포함됩니다.

다음은 .NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하여 토큰을 얻은 후 API를 호출하는 클라이언트를 보여 주는 C# 코드 예제입니다.

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> 클라이언트 애플리케이션은 *웹 API의* Microsoft ID 플랫폼에 전달자 토큰을 요청합니다. 웹 API는 토큰을 확인하고 포함된 클레임을 확인하는 유일한 애플리케이션입니다. 클라이언트 앱은 토큰의 클레임을 조사하려고 해서는 안 됩니다.
>
> 이후에 웹 API에서 토큰 암호화를 요구할 수 있습니다. 이 요구 사항은 액세스 토큰을 볼 수 있는 클라이언트 앱의 액세스를 방지합니다.

## <a name="jwtbearer-configuration"></a>JwtBearer 구성

이 섹션에서는 전달자 토큰을 구성하는 방법을 설명합니다.

### <a name="config-file"></a>Config 파일

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>웹 API에 사용자 지정 앱 ID URI를 사용한 경우

앱 등록 포털에서 제안한 앱 ID URI를 수락한 경우 대상 그룹을 지정할 필요가 없습니다([애플리케이션 ID URI 및 범위](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes) 참조). 그렇지 않은 경우에는 웹 API의 앱 ID URI가 값으로 지정된 `Audience` 속성을 추가해야 합니다.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>코드 초기화

**[권한 부여]** 특성이 포함된 컨트롤러 작업에 대해 앱이 호출되면 ASP.NET 및 ASP.NET Core가 권한 부여 헤더의 전달자 토큰에서 액세스 토큰을 추출합니다. 그런 다음, 액세스 토큰이 JwtBearer 미들웨어로 전달되고, 이 미들웨어에서 .NET용 Microsoft IdentityModel 확장을 호출합니다.

#### <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

ASP.NET Core를 사용하여 웹 API를 개발하는 경우에는 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet 패키지를 사용하는 것이 좋습니다.

_Microsoft.Identity.Web_ 은 ASP.NET Core, 인증 미들웨어 및 .NET용 [MSAL(Microsoft 인증 라이브러리)](msal-overview.md) 간의 붙이기를 제공합니다. 더 명확하고, 더 강력한 개발자 환경을 지원하고 Microsoft ID 플랫폼과 Azure AD B2C의 강점을 활용합니다.

#### <a name="using-microsoftidentityweb-templates"></a>Microsoft.Identity.Web 템플릿 사용

Microsoft.Identity.Web 프로젝트 템플릿을 사용하여 웹 API를 처음부터 새로 만들 수 있습니다. 자세한 내용은 [Microsoft.Identity.Web - Web API 프로젝트 템플릿](https://aka.ms/ms-id-web/webapi-project-templates)을 참조하세요.

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>기존 ASP.NET Core 3.1 애플리케이션에서 시작

현재 ASP.NET Core 3.1은 Microsoft.AspNetCore.AzureAD.UI 라이브러리를 사용합니다. 미들웨어는 Startup.cs 파일에서 초기화됩니다.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

미들웨어는 다음 명령을 통해 웹 API에 추가됩니다.

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 현재 ASP.NET Core 템플릿은 사용자의 조직 또는 모든 조직 내에서 사용자를 로그인시키는 Azure AD(Azure Active Directory) 웹 API를 만듭니다. 개인 계정을 가진 사용자는 로그인시키지 않습니다. 하지만 *Startup.cs* 의 코드를 바꿔서 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)을 통해 Microsoft ID 플랫폼을 사용하도록 템플릿을 변경할 수 있습니다.

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

다음과 같은 동일한 코드를 작성할 수도 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Microsoft.Identity.Web을 사용하고 *appsettings.json* 에 `Audience`를 설정하지 않는 경우 다음을 사용합니다.
> -  `$"{ClientId}"` - [액세스 토큰 수락 버전](scenario-protected-web-api-app-registration.md#accepted-token-version)을 `2`로 설정한 경우 또는 Azure AD B2C 웹 API의 경우
> - `$"api://{ClientId}` - 다른 모든 경우(v1.0 [액세스 토큰](access-tokens.md)의 경우)
> 자세한 내용은 Microsoft.Identity.Web [소스 코드](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83)를 참조하세요.

위의 코드 조각은 [ASP.NET Core 웹 API 증분 자습서](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28)에서 추출한 것입니다. **AddMicrosoftIdentityWebApiAuthentication** 의 세부 정보는 [Microsoft.Identity.Web](microsoft-identity-web.md)에서 확인할 수 있습니다. 이 메서드는 토큰의 유효성을 검사하는 방법을 미들웨어에 지시하는 [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi)를 호출합니다.

## <a name="token-validation"></a>토큰 유효성 검사

위의 코드 조각에서 웹앱의 OpenID Connect 미들웨어와 같은 JwtBearer 미들웨어는 `TokenValidationParameters`의 값을 기반으로 토큰의 유효성을 검사합니다. 필요에 따라 토큰의 암호가 해독되고, 클레임이 추출되고, 서명이 확인됩니다. 그런 다음, 미들웨어가 다음 데이터를 확인하여 토큰의 유효성을 검사합니다.

- 대상 그룹: 토큰은 웹 API를 대상으로 합니다.
- 하위: 웹 API를 호출할 수 있는 앱에 대해 발급 되었습니다.
- 발급자: 신뢰할 수 있는 STS(보안 토큰 서비스)가 발급했습니다.
- 만료: 수명이 범위 내에 있습니다.
- 서명: 변조되지 않았습니다.

특별한 유효성 검사를 수행할 수도 있습니다. 예를 들어 토큰에 포함된 경우 서명 키를 신뢰할 수 있고, 토큰이 재생되고 있지 않은지 확인할 수 있습니다. 마지막으로, 일부 프로토콜에는 특정 유효성 검사가 필요합니다.

### <a name="validators"></a>유효성 검사기

유효성 검사 단계는 [.NET용 Microsoft IdentityModel 예외](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) 오픈 소스 라이브러리에서 제공하는 유효성 검사기에서 캡처됩니다. 유효성 검사기는 라이브러리 소스 파일 [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)에 정의되어 있습니다.

다음 표에서는 유효성 검사기에 대해 설명합니다.

| 유효성 검사기 | 설명 |
|---------|---------|
| **ValidateAudience** | 자동으로 토큰의 유효성을 검사하는 애플리케이션용 토큰인지 확인합니다. |
| **ValidateIssuer** | 신뢰할 수 있는 STS(즉, 신뢰할 수 있는 기관)에서 토큰을 발급했는지 확인합니다. |
| **ValidateIssuerSigningKey** | 토큰의 유효성을 검사하는 애플리케이션에서 토큰을 서명하는 데 사용된 키를 신뢰하는지 확인합니다. 키가 토큰에 포함되는 특별한 경우가 있습니다. 하지만 이 경우는 일반적으로 발생하지 않습니다. |
| **ValidateLifetime** | 토큰을 아직 유효한지, 아니면 이미 유효한지 확인합니다. 유효성 검사기는 토큰의 수명이 **notbefore** 및 **expires** 클레임으로 지정된 범위 내에 있는지 확인합니다. |
| **ValidateSignature** | 토큰이 변조되지 않았는지 확인합니다. |
| **ValidateTokenReplay** | 토큰이 재생되지 않았는지 확인합니다. 일회용 프로토콜을 사용하는 특별한 경우가 있습니다. |

#### <a name="customizing-token-validation"></a>토큰 유효성 검사 사용자 지정

유효성 검사기는 **TokenValidationParameters** 클래스의 속성과 연결되어 있습니다. 속성은 ASP.NET 및 ASP.NET Core 구성에서 초기화됩니다.

대부분의 경우 매개 변수를 변경할 필요가 없습니다. 단일 테넌트가 아닌 앱은 예외입니다. 이러한 웹앱은 모든 조직이나 개인 Microsoft 계정의 사용자를 허용합니다. 이 경우 발급자의 유효성을 검사해야 합니다. Microsoft.Identity.Web은 발급자 유효성 검사도 처리합니다. 자세한 내용은 Microsoft.Identity.Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)를 참조하세요.

ASP.NET Core에서 토큰 유효성 검사 매개 변수를 사용자 지정하려는 경우 *Startup.cs* 에서 다음 코드 조각을 사용합니다.

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  };
});
```

다음 코드 샘플은 ASP.NET MVC에서 사용자 지정 토큰 유효성 검사를 수행하는 방법을 보여 줍니다.

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Azure Functions에서 토큰 유효성 검사

Azure Functions에서 들어오는 액세스 토큰의 유효성을 검사할 수도 있습니다. GitHub의 다음 코드 샘플에서 이러한 유효성 검사의 예제를 찾을 수 있습니다.

- .NET: [Azure-Samples/ms-identity-dotnet-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-Samples/ms-identity-nodejs-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/ms-identity-python-webapi-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서로 이동하여 [코드의 범위 및 앱 역할을 확인](scenario-protected-web-api-verification-scope-app-roles.md)합니다.
