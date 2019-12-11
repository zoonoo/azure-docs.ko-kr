---
title: 보호 된 웹 API 앱 구성 | Microsoft
titleSuffix: Microsoft identity platform
description: 보호 된 웹 API를 빌드하고 응용 프로그램의 코드를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 7f78fa35096b7e17d3736190bfa49619c2c81520
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965401"
---
# <a name="protected-web-api-code-configuration"></a>보호 된 웹 API: 코드 구성

보호 된 web API에 대 한 코드를 구성 하려면 보호 된 Api를 정의 하는 항목, 전달자 토큰을 구성 하는 방법 및 토큰의 유효성을 검사 하는 방법을 이해 해야 합니다.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>ASP.NET/ASP.NET Core Api를 보호 된 것으로 정의 하는 것은 무엇 인가요?

웹 앱과 마찬가지로 ASP.NET/ASP.NET 핵심 웹 Api는 해당 컨트롤러 작업에 `[Authorize]` 특성이 접두사로 추가 되기 때문에 "보호" 됩니다. 따라서 인증 된 id를 사용 하 여 API를 호출 하는 경우에만 컨트롤러 작업을 호출할 수 있습니다.

A következő kérdéseket kell figyelembe venni:

- Web API는이를 호출 하는 앱의 id를 어떻게 인식 하나요? 앱만 웹 API를 호출할 수 있습니다.
- 앱이 사용자를 대신 하 여 web API를 호출 하는 경우 사용자의 id는 무엇 인가요?

## <a name="bearer-token"></a>전달자 토큰

앱 id에 대 한 정보 및 사용자에 대 한 정보 (웹 앱이 디먼 앱에서 서비스 간 호출을 허용 하지 않는 경우)는 앱이 호출 될 때 헤더에 설정 된 전달자 토큰에 저장 됩니다.

다음은 .NET C# 용 Microsoft 인증 라이브러리 (MSAL.NET)를 사용 하 여 토큰을 얻은 후 API를 호출 하는 클라이언트를 보여 주는 코드 예제입니다.

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> 클라이언트 응용 프로그램에서 *웹 API에 대 한*Microsoft id 플랫폼 끝점으로 전달자 토큰을 요청 했습니다. 웹 API는 토큰을 확인 하 고 포함 된 클레임을 확인 해야 하는 유일한 응용 프로그램입니다. 클라이언트 앱은 토큰의 클레임을 검사 해 서 사용해 서는 안 됩니다. 웹 API는 나중에 토큰을 암호화 해야 할 수 있습니다. 이 요구 사항은 액세스 토큰을 볼 수 있는 클라이언트 앱에 대 한 액세스를 방지 합니다.)

## <a name="jwtbearer-configuration"></a>JwtBearer 구성

이 섹션에서는 전달자 토큰을 구성 하는 방법을 설명 합니다.

### <a name="config-file"></a>구성 파일

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

`[Authorize]` 특성을 보유 하는 컨트롤러 작업에 대해 앱이 호출 되 면 ASP.NET/ASP.NET Core는 호출 요청의 권한 부여 헤더에서 전달자 토큰을 보고 액세스 토큰을 추출 합니다. 그런 다음 토큰은 .NET 용 Microsoft System.identitymodel Extensions를 호출 하는 JwtBearer 미들웨어에 전달 됩니다.

ASP.NET Core에서이 미들웨어는 Startup.cs 파일에서 초기화 됩니다.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

미들웨어는 다음 명령으로 web API에 추가 됩니다.

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 현재 ASP.NET Core 템플릿에서는 개인 계정이 아닌 조직 또는 조직 내에서 사용자를 로그인 하는 Azure Active Directory (Azure AD) 웹 Api를 만듭니다. 그러나이 코드를 Startup.cs 파일에 추가 하 여 Microsoft id 플랫폼 끝점을 사용 하도록 쉽게 변경할 수 있습니다.

```CSharp
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

이 코드 조각은 [WebApiServiceCollectionExtensions/L50 #-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)의 ASP.NET Core Web Api 증분 자습서에서 추출 됩니다. 훨씬 더 많은 `AddProtectedWebApi` 메서드가 Startup.cs에서 호출 됩니다.

## <a name="token-validation"></a>토큰 유효성 검사

웹 앱의 Openid connect Connect 미들웨어와 같은 JwtBearer 미들웨어는 토큰의 유효성을 검사 하기 위해 `TokenValidationParameters`에 의해 전달 됩니다. 토큰은 암호 해독 됩니다 (필요에 따라). 클레임을 추출 하 고 서명을 확인 합니다. 그러면 미들웨어는 다음 데이터를 확인 하 여 토큰의 유효성을 검사 합니다.

- 웹 API (대상)를 대상으로 합니다.
- 웹 API (sub)를 호출할 수 있는 앱에 대해 발급 되었습니다.
- 신뢰할 수 있는 STS (보안 토큰 서비스)에서 발급 되었습니다.
- 수명이 범위 (만료)입니다.
- (서명)이 손상 되지 않았습니다.

특별 한 유효성 검사를 수행할 수도 있습니다. 예를 들어, 서명 키 (토큰에 포함 된 경우)를 신뢰할 수 있고 토큰이 재생 되 고 있지 않은지 확인할 수 있습니다. 마지막으로 일부 프로토콜에는 특정 유효성 검사가 필요 합니다.

### <a name="validators"></a>유효성 검사기

유효성 검사 단계는 유효성 검사기에서 캡처됩니다 .이는 모든 [.net 용 Microsoft System.identitymodel Extensions](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) 오픈 소스 라이브러리의 한 소스 파일 ( [system.identitymodel/validation. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs))에 있습니다.

유효성 검사기는 다음 표에 설명 되어 있습니다.

| 유효성 검사기 | Leírás |
|---------|---------|
| `ValidateAudience` | 토큰의 유효성을 검사 하는 응용 프로그램에 대 한 토큰을 확인 합니다. |
| `ValidateIssuer` | 신뢰할 수 있는 STS에서 토큰을 발급 했는지 확인 합니다 (다른 사람이 신뢰 함). |
| `ValidateIssuerSigningKey` | 토큰의 유효성을 검사 하는 응용 프로그램에서 토큰을 서명 하는 데 사용 된 키를 신뢰 하는지 확인 합니다. 토큰에 키가 포함 되는 특수 한 경우입니다. 일반적으로 필요 하지 않습니다.) |
| `ValidateLifetime` | 토큰이 아직 (또는 이미) 유효한 지 확인 합니다. 유효성 검사기는 토큰의 수명 (`notbefore` 및 `expires` 클레임)이 범위 내에 있는지 확인 합니다. |
| `ValidateSignature` | 토큰이 변조 되지 않았는지 확인 합니다. |
| `ValidateTokenReplay` | 토큰을 재생 하지 않도록 합니다. Onetime 프로토콜을 사용 하는 특수 한 경우입니다. |

유효성 검사기는 모두 ASP.NET/ASP.NET Core 구성에서 초기화 되는 `TokenValidationParameters` 클래스의 속성과 연결 됩니다. 대부분의 경우 매개 변수를 변경할 필요가 없습니다. 단일 테 넌 트가 아닌 앱에 대 한 한 가지 예외가 있습니다. 즉, 모든 조직이 나 개인 Microsoft 계정의 사용자를 수락 하는 웹 앱입니다. 이 경우 발급자의 유효성을 검사 해야 합니다.

## <a name="token-validation-in-azure-functions"></a>Azure Functions에서 토큰 유효성 검사

또한 Azure 함수에서 들어오는 액세스 토큰의 유효성을 검사할 수 있습니다. [Dotnet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [Nodejs](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)및 [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)의 Azure 함수에서 토큰의 유효성을 검사 하는 예제를 찾을 수 있습니다.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [코드에서 범위 및 앱 역할 확인](scenario-protected-web-api-verification-scope-app-roles.md)
