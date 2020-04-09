---
title: 보호된 웹 API 앱 구성 | Azure
titleSuffix: Microsoft identity platform
description: 보호된 웹 API를 빌드하고 응용 프로그램의 코드를 구성하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882474"
---
# <a name="protected-web-api-code-configuration"></a>보호된 웹 API: 코드 구성

보호된 웹 API에 대한 코드를 구성하려면 다음을 이해해야 합니다.

- API를 보호된 것으로 정의하는 것
- 베어러 토큰을 구성하는 방법.
- 토큰의 유효성을 검사하는 방법.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>ASP.NET 및 ASP.NET 코어 API를 보호된 것으로 정의하는 것은 무엇입니까?

웹 앱과 마찬가지로 ASP.NET 및 ASP.NET Core 웹 API는 컨트롤러 작업이 **[권한 부여]** 특성과 접두사에 붙어 있기 때문에 보호됩니다. 컨트롤러 작업은 API가 승인된 ID로 호출되는 경우에만 호출할 수 있습니다.

다음 질문을 살펴보세요.

- 앱에서만 웹 API를 호출할 수 있습니다. API는 이를 호출하는 앱의 ID를 어떻게 알 수 있습니까?
- 앱이 사용자를 대신하여 API를 호출하는 경우 사용자의 ID는 무엇입니까?

## <a name="bearer-token"></a>전달자 토큰

앱이 호출될 때 헤더에 설정된 베어러 토큰에는 앱 ID에 대한 정보가 포함됩니다. 또한 웹 앱이 데몬 앱에서 서비스 간 호출을 수락하지 않는 한 사용자에 대한 정보도 보유합니다.

다음은 .NET(MSAL.NET 대한 Microsoft 인증 라이브러리를 통해 토큰을 획득한 후 API를 호출하는 클라이언트를 보여 주는 C# 코드 예제입니다.

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
> 클라이언트 응용 프로그램은 *웹 API에 대한*Microsoft ID 플랫폼 끝점에 베어러 토큰을 요청합니다. 웹 API는 토큰을 확인하고 포함된 클레임을 확인해야 하는 유일한 응용 프로그램입니다. 클라이언트 앱은 토큰의 클레임을 검사하려고 시도해서는 안 됩니다.
>
> 나중에 웹 API에서 토큰을 암호화해야 할 수 있습니다. 이 요구 사항은 액세스 토큰을 볼 수 있는 클라이언트 앱에 대한 액세스를 방지합니다.

## <a name="jwtbearer-configuration"></a>Jwt베어러 구성

이 섹션에서는 베어러 토큰을 구성하는 방법에 대해 설명합니다.

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

### <a name="code-initialization"></a>코드 초기화

**[권한 부여]** 특성을 포함하는 컨트롤러 동작에서 앱이 호출되면 ASP.NET 및 ASP.NET Core는 권한 부여 헤더의 베어러 토큰에서 액세스 토큰을 추출합니다. 그런 다음 액세스 토큰은 .NET에 대한 Microsoft IDModel 확장을 호출하는 JwtBearer 미들웨어로 전달됩니다.

ASP.NET 코어에서는 이 미들웨어가 Startup.cs 파일에 초기화됩니다.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

미들웨어는 다음 명령에 따라 웹 API에 추가됩니다.

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 현재 ASP.NET 핵심 템플릿은 조직 또는 조직 내의 사용자를 로그인하는 Azure Active Directory(Azure AD) 웹 API를 만듭니다. 개인 계정으로 로그인하지 않습니다. 그러나 다음 Startup.cs 이 코드를 추가하여 Microsoft ID 플랫폼 끝점을 사용하도록 템플릿을 변경할 수 있습니다.

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

위의 코드 조각은 [Microsoft.Identity.Web/WebApiServiceCollectionExtension.cs#L50-L63의](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)ASP.NET 코어 웹 API 증분 자습서에서 추출됩니다. 코드 조각에 표시되는 것보다 더 많은 작업을 수행하는 **AddProtectedWebApi** 메서드는 Startup.cs 호출됩니다.

## <a name="token-validation"></a>토큰 유효성 검사

앞의 코드 조각에서 JwtBearer 미들웨어는 웹 앱의 OpenID Connect 미들웨어와 같이 의 값을 `TokenValidationParameters`기반으로 토큰의 유효성을 검사합니다. 토큰은 필요에 따라 해독되고 클레임이 추출되고 서명이 확인됩니다. 그런 다음 미들웨어는 이 데이터를 확인하여 토큰의 유효성을 검사합니다.

- 대상: 토큰은 웹 API를 대상으로 합니다.
- 하위: 웹 API를 호출할 수 있는 앱에 대해 발급되었습니다.
- 발급자: 신뢰할 수 있는 보안 토큰 서비스(STS)에 의해 발급되었습니다.
- 만료: 수명이 범위입니다.
- 서명: 변조되지 않았습니다.

특별한 유효성 검사가 있을 수도 있습니다. 예를 들어 토큰에 포함된 서명 키가 신뢰할 수 있고 토큰이 재생되지 않는지 확인할 수 있습니다. 마지막으로 일부 프로토콜에는 특정 유효성 검사가 필요합니다.

### <a name="validators"></a>유효성 검사기

유효성 검사 단계는 .NET 오픈 소스 라이브러리에 대 한 [Microsoft IDModel 확장에서](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) 제공 하는 유효성 검사기에 캡처 됩니다. 유효성 검사기는 라이브러리 소스 파일 [Microsoft.IdentityModel.Tokens/Validators.cs에 정의되어](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)있습니다.

이 표는 유효성 검사기에 대해 설명합니다.

| Validator | Description |
|---------|---------|
| **검증 대상** | 토큰이 토큰의 유효성을 검사하는 응용 프로그램에 대한 지 확인합니다. |
| **유효성 검사 발급자** | 토큰이 신뢰할 수 있는 STS에 의해 발급되었는지 확인합니다. |
| **유효성 검사발급자 서명키** | 토큰유효성검사 응용 프로그램이 토큰에 서명하는 데 사용된 키를 트러스트하도록 합니다. 키가 토큰에 포함되는 특별한 경우가 있습니다. 그러나이 경우는 일반적으로 발생하지 않습니다. |
| **유효 수명 확인** | 토큰이 정지 또는 이미 유효한지 확인합니다. 유효성 검사기는 토큰의 수명이 **이전에** 지정된 범위에 있는지 확인하고 **클레임을 만료합니다.** |
| **유효성 검사 서명** | 토큰이 변조되지 않았는지 확인합니다. |
| **유효성 검사토큰 재생** | 토큰이 재생되지 않았는지 확인합니다. 일부 일회용 프로토콜에는 특별한 경우가 있습니다. |

유효성 검사기는 **TokenValidationParameters** 클래스의 속성과 연결됩니다. 속성은 ASP.NET 및 ASP.NET 코어 구성에서 초기화됩니다.

대부분의 경우 매개 변수를 변경할 필요가 없습니다. 단일 테넌트가 아닌 앱은 예외입니다. 이러한 웹 앱은 모든 조직 또는 개인 Microsoft 계정의 사용자를 허용합니다. 이 경우 발급자의 유효성을 검사해야 합니다.

## <a name="token-validation-in-azure-functions"></a>Azure 함수의 토큰 유효성 검사

Azure Functions에서 들어오는 액세스 토큰의 유효성을 검사할 수도 있습니다. 이러한 유효성 검사의 예는 [Microsoft .NET,](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)및 Python에서 찾을 수 [있습니다.](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [코드에서 범위 및 앱 역할 확인](scenario-protected-web-api-verification-scope-app-roles.md)
