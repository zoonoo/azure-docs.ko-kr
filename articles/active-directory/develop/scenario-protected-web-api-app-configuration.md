---
title: 앱 코드 구성-web API 보호 | Azure
description: 보호 된 web API를 빌드하고 응용 프로그램의 코드를 구성 하는 방법을 알아봅니다.
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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536887"
---
# <a name="protected-web-api-code-configuration"></a>보호 된 web API: 코드 구성

보호 된 web API에 대 한 코드를 구성 하려면 protected로 Api 정의 전달자 토큰을 구성 하는 방법 및 토큰의 유효성을 검사 하는 방법을 이해 해야 합니다.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>보호 ASP.NET/ASP.NET Core Api 정의?

웹 앱 같은 ASP.NET/ASP.NET Core web Api "보호를 받습니다" 해당 컨트롤러 작업 접두사로 `[Authorize]` 특성입니다. 따라서 권한이 부여 된 id를 사용 하 여 API를 호출 하는 경우 컨트롤러 작업을 호출할 수 있습니다.

다음 질문을 고려 합니다.

- Web API 호출 하는 앱의 id를 확인 하는 방법 (앱만 web API를 호출할 수 있습니다.)
- 앱 사용자를 대신 하 여 web API를 호출 하는 경우 사용자의 id는 무엇입니까?

## <a name="bearer-token"></a>전달자 토큰

사용자 및 앱의 id에 대 한 정보 (하지 않는 한 디먼 앱에서 서비스 간 호출을 허용 하는 웹 앱), 앱에서 호출 될 때 헤더에 설정 된 전달자 토큰에 보관 됩니다.

다음은 C# .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 토큰을 획득 한 후 API를 호출 하는 클라이언트를 보여 주는 코드 예제:

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
> Microsoft id 플랫폼 끝점에 클라이언트 응용 프로그램에서 전달자 토큰을 요청한 *web API에 대 한*합니다. Web API가 토큰을 확인 하 고 포함 된 클레임을 확인 해야 할 유일한 응용 프로그램입니다. 클라이언트 앱 토큰의 클레임을 검사 하려고 하지 해야 합니다. (웹 API 필요할 수 있습니다, 나중에 토큰을 암호화 하는 것입니다. 이 요구 사항에 따라 액세스가 방지 액세스 토큰을 볼 수 있는 클라이언트 앱에 대 한 합니다.)

## <a name="jwtbearer-configuration"></a>JwtBearer 구성

이 섹션에서는 전달자 토큰을 구성 하는 방법을 설명 합니다.

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

### <a name="code-initialization"></a>초기화 코드

앱을 보유 하는 컨트롤러 작업에서 호출 될 때를 `[Authorize]` 특성 ASP.NET/ASP.NET Core 호출 요청의 인증 헤더에서 전달자 토큰을 검색 및 액세스 토큰을 추출 합니다. 토큰은.NET 용 Microsoft IdentityModel 확장을 호출 하는 JwtBearer 미들웨어로 전달 됩니다.

이 미들웨어는 ASP.NET Core에서 Startup.cs 파일에서 초기화 됩니다.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

미들웨어는이 명령에서 웹 API에 추가 됩니다.

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 현재 ASP.NET Core 템플릿은 조직 또는 개인 계정과 되지 모든 조직 내 사용자가 Azure Active Directory (Azure AD)는 웹 Api를 로그인을 만듭니다. 하지만 Startup.cs 파일에이 코드를 추가 하 여 Microsoft id 플랫폼 끝점을 사용 하도록 쉽게 변경할 수 있습니다.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>토큰 유효성 검사

Web apps에서 OpenID Connect 미들웨어와 같은 JwtBearer 미들웨어에서 전달 됩니다 `TokenValidationParameters` 토큰 유효성 검사 합니다. 필요에 따라 토큰 암호 해독 됩니다 하 하며 클레임을 추출한 서명을 확인 합니다. 그런 다음 미들웨어는 토큰의 유효성 검사이 데이터를 확인 하 여:

- Web API (대상)에 대 한 대상입니다.
- Web API (sub)를 호출 하도록 허용 하는 앱에 대 한 발급 합니다.
- 신뢰할 수 있는 보안 토큰 서비스 (STS) (발급자)에서 발급 된 것입니다.
- 수명은 (만료) 범위의 같습니다.
- (서명)를 사용 하 여 손상 되지 않은 것입니다.

특별 한 유효성 검사도 있을 수 있습니다. 예를 들어, 서명 키 (토큰에 포함 됨) 하는 경우 신뢰할 수 있는 되며 토큰을 재생 중인 되지 유효성을 검사 하는 것이 같습니다. 마지막으로, 일부 프로토콜 특정 유효성 검사 필요합니다.

### <a name="validators"></a>유효성 검사기

유효성 검사 단계를 모두에 유효성 검사기에 기록 되는 [.NET 용 Microsoft IdentityModel 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) 하나의 소스 파일에서 오픈 소스 라이브러리: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

유효성 검사기는이 표에 설명 되어 있습니다.

| 유효성 검사기 | 설명 |
|---------|---------|
| `ValidateAudience` | (적합)는 토큰 유효성을 검사 하는 응용 프로그램에 대 한 토큰을 확인 합니다. |
| `ValidateIssuer` | 토큰 (누군가로부터 신뢰) 신뢰할 수 있는 STS에서 발급 했는지 확인 합니다. |
| `ValidateIssuerSigningKey` | 토큰 트러스트 유효성을 검사 하 여 응용 프로그램은 토큰 서명에 사용 된 키를 확인 합니다. (키 토큰에 포함 되어 있는 특별 한 경우. 일반적으로 필요 하지 않음) |
| `ValidateLifetime` | 토큰이 유효 여전히 (또는 이미)를 확인 합니다. 유효성 검사기가 확인 하는 경우 토큰의 수명 (`notbefore` 및 `expires` 클레임) 범위 내에 있습니다. |
| `ValidateSignature` | 토큰이 손상 되지 않았는지 확인 합니다. |
| `ValidateTokenReplay` | 토큰 재생 되지 않습니다 보장 합니다. (일부 onetime 사용 프로토콜에 대 한 특별 한 경우) |

유효성 검사기의 속성을 사용 하 여 모든 연관 된 `TokenValidationParameters` ASP.NET/ASP.NET 핵심 구성에서 초기화 자체 클래스. 대부분의 경우에서 매개 변수를 변경할 필요가 없습니다. 단, 단일 테 넌 트에 없는 앱에 대 한 방법이 있습니다. (즉, 웹 앱 사용자 개인 Microsoft 계정 또는 조직에서 허용 하는.) 이 경우 발급자 유효성을 검사 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [범위 및 코드에서 앱 역할 확인](scenario-protected-web-api-verification-scope-app-roles.md)
