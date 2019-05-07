---
title: 앱 코드 구성-web API 보호 | Azure
description: 보호 된 Web API를 빌드하고 응용 프로그램의 코드를 구성 하는 방법을 알아봅니다.
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
ms.openlocfilehash: e206cb29338445e30a7462bcbaf0079236e75510
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074967"
---
# <a name="protected-web-api---code-configuration"></a>보호 된 web API-코드 구성

보호 된 web API에 대 한 코드를 성공적으로 구성 하려면 보호 된 Api를 사용 하면 어떤, 전달자 토큰을 구성 해야 하는 항목 및 토큰의 유효성을 검사 하는 방법을 이해 해야 합니다.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>보호 되는 ASP.NET/ASP.NET 코어 Api 이유?

웹 앱에서의 ASP.NET/ASP.NET 핵심 웹 Api는 "보호" 하므로 해당 컨트롤러 작업 붙습니다 같은 `[Authorize]` 특성입니다. 따라서 권한이 부여 된 id를 사용 하 여 API를 호출 하면 컨트롤러 작업을 호출할만 있습니다.

다음 질문을 고려 합니다.

- Web API (응용 프로그램 에서만 웹 API를 호출할 수 있습니다) 호출 하는 응용 프로그램의 id를 확인 하는 방법
- 응용 프로그램을 사용자 대신 web API를 호출 하는 경우 사용자의 id는 무엇입니까?

## <a name="bearer-token"></a>전달자 토큰

사용자 및 응용 프로그램의 id에 대 한 정보 (하지 않는 한 디먼 응용 프로그램에서 서비스 간 호출을 수락 하는 웹 앱), 응용 프로그램을 호출할 때 헤더에 설정 되는 전달자 토큰에 보관 됩니다.

다음은 C# MSAL.NET 사용 하 여 토큰을 확보 한 후 API를 호출 하는 클라이언트를 보여 주는 코드 예제입니다.

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
> Microsoft id 플랫폼 끝점에 클라이언트 응용 프로그램에서 전달자 토큰을 요청한 **web API에 대 한**합니다. Web API가 토큰을 확인 하 고 포함 된 클레임을 확인 해야 할 유일한 응용 프로그램입니다. 클라이언트 응용 프로그램 토큰의 클레임에 표시 되지 않습니다 됩니다 (web API 결정할 수 있습니다, 나중에 토큰을 암호화 하는 것을 요구 하 고 해독할 수 있는 클라이언트 응용 프로그램을 중단 액세스 토큰을 엽니다).

## <a name="jwtbearer-configuration"></a>JwtBearer 구성

이 섹션에서는 전달자 토큰을 구성 해야 하는 내용을 다룹니다.

### <a name="config-file"></a>Config 파일

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

응용 프로그램 보관 하는 컨트롤러 작업에서 호출 되는 경우는 `[Authorize]` 특성인 ASP.NET/ASP.NET core 호출 요청의 인증 헤더에서 전달자 토큰을 검색 및 다음은 JwtBearer에 전달 되는 액세스 토큰을 추출 합니다. .NET 용 Microsoft Id 모델 확장을 호출 하는 미들웨어입니다.

ASP.NET Core,이 미들웨어에서 초기화 되는 `Startup.cs` 파일입니다.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

미들웨어는 다음 명령에서 웹 API에 추가 됩니다.

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 현재 ASP.NET 코어 템플릿에 Azure AD v1.0 웹 Api를 만듭니다. 그러나 쉽게 변경할 수 있습니다 다음 코드를 추가 하 여 Microsoft id 플랫폼 끝점을 사용 하 여 `Startup.cs` 파일입니다.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>토큰 유효성 검사

Web apps에서 OpenID Connect 미들웨어와 같은 JwtBearer 미들웨어에서 전달 되는 `TokenValidationParameters` 토큰 유효성 검사 합니다. 토큰 (필요한 경우)의 암호가 해독 됩니다 하 하며 클레임을 추출한 서명을 확인 합니다. 그런 다음 토큰은 다음 데이터를 검사 하 여 유효성이 검사 됩니다.

- Web API (대상)에 대 한 대상
- Web API (sub)를 호출 하도록 허용 하는 응용 프로그램에 대 한 발급
- 여는 신뢰할 Server STS (보안 토큰) (발급자) 발급
- 토큰의 수명은 범위 (만료)
- (서명)를 사용 하 여 손상 되지 않았습니다.

특별 한 유효성 검사도 있을 수 있습니다. 예를 들어, 서명 키 (토큰에 포함 됨) 하는 경우 신뢰할 수 있는 되며 토큰을 재생 중인 되지 유효성을 검사 하는 것이 같습니다. 마지막으로, 일부 프로토콜 특정 유효성 검사 필요합니다.

### <a name="validators"></a>유효성 검사기

유효성 검사 단계를 모두에 유효성 검사기에 캡처되는 [.NET 용 Microsoft Id 모델 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) 하나의 소스 파일에서 오픈 소스 라이브러리: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

유효성 검사기는 다음 표에 설명 되어 있습니다.

| 유효성 검사기 | 설명 |
|---------|---------|
| `ValidateAudience` | 토큰의 유효성을 검사 토큰 (me)는 응용 프로그램에 대 한 하는지 확인 합니다. |
| `ValidateIssuer` | (한 사람 으로부터 신뢰) 토큰이 신뢰할 수 있는 STS에서 발급 된 것을 확인 합니다. |
| `ValidateIssuerSigningKey` | 토큰 트러스트 유효성을 검사 하 여 응용 프로그램 (일반적으로 없어도 토큰에서 키를 포함 되어 있는 특별 한 경우)는 토큰 서명에 사용 된 키를 확인 합니다. |
| `ValidateLifetime` | 토큰이 여전히 (또는 이미) 유효한 지 확인 합니다. 토큰의 수명은 확인 하 여 (`notbefore`, `expires` 클레임) 범위 내에 있습니다. |
| `ValidateSignature` | 토큰을 손상 되지 않았는지 확인 합니다. |
| `ValidateTokenReplay` | 토큰 재생 되지 않습니다 보장 (몇 번만 사용 하 여 프로토콜에 대 한 특별 한 경우). |

유효성 검사기의 속성을 사용 하 여 모든 연관 된 `TokenValidationParameters` ASP.NET/ASP.NET 핵심 구성에서 초기화 자체 클래스. 대부분의 경우에서 매개 변수를 변경할 필요가 없습니다. 한 가지 예외가 단일 테 넌 트에 없는 응용 프로그램에 대 한 (즉, 개인 Microsoft 계정 또는 조직에서 사용자를 허용 하는 웹 앱)--이 경우 발급자 유효성을 검사 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-protected-web-api-production.md)
