---
title: 웹 Api를 호출 하는 web API 구성 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 web API를 빌드하는 방법 알아보기 (앱 코드 구성)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e54e26bba1618a3b5835480ed1b1fe698bc8db4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043423"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>웹 Api를 호출 하는 웹 API: 코드 구성

Web API를 등록 한 후에는 응용 프로그램에 대 한 코드를 구성할 수 있습니다.

웹 api를 구성 하는 데 사용 하는 코드는 web API를 보호 하는 데 사용 되는 코드를 기반으로 하 여 다운스트림 웹 api를 호출 합니다. 자세한 내용은 [보호 된 웹 API: 앱 구성](scenario-protected-web-api-app-configuration.md)을 참조 하세요.

## <a name="code-subscribed-to-ontokenvalidated"></a>OnTokenValidated 검사 된 코드

모든 보호 된 웹 Api에 대 한 코드 구성 위에는 API가 호출 될 때 수신 하는 전달자 토큰의 유효성 검사를 구독 해야 합니다.

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>흐름에 대 한

AddAccountToCacheFromJwt () 메서드는 다음을 수행 해야 합니다.

- MSAL (Microsoft 인증 라이브러리) 기밀 클라이언트 응용 프로그램을 인스턴스화합니다.
- `AcquireTokenOnBehalf` 메서드를 호출합니다. 이 호출은 클라이언트에서 web API에 대해 획득 한 전달자 토큰을 동일한 사용자의 전달자 토큰에 대해 교환 하지만 API 호출을 다운스트림 API로 사용 합니다.

### <a name="instantiate-a-confidential-client-application"></a>기밀 클라이언트 응용 프로그램 인스턴스화

이 흐름은 기밀 클라이언트 흐름 에서만 사용할 수 있으므로 보호 된 웹 API는 `WithClientSecret` 또는 `WithCertificate` 메서드를 통해 클라이언트 자격 증명 (클라이언트 암호 또는 인증서)을 [ConfidentialClientApplicationBuilder 클래스](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) 에 제공 합니다.

![IConfidentialClientApplication 메서드 목록](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

마지막으로 클라이언트 암호 또는 인증서를 통해 id를 증명 하는 대신, 기밀 클라이언트 응용 프로그램은 클라이언트 어설션을 사용 하 여 해당 id를 증명할 수 있습니다.
이 고급 시나리오에 대 한 자세한 내용은 [기밀 클라이언트 어설션](msal-net-client-assertions.md)을 참조 하세요.

### <a name="how-to-call-on-behalf-of"></a>대신를 호출 하는 방법

`IConfidentialClientApplication` 인터페이스에서 [AcquireTokenOnBehalf 메서드](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) 를 호출 하 여 obo (주문형) 호출을 수행 합니다.

`UserAssertion` 클래스는 자체 클라이언트에서 web API가 수신 하는 전달자 토큰을 기반으로 빌드됩니다. 다음과 같은 [두 가지 생성자](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)가 있습니다.
* JWT (JSON Web Token) 전달자 토큰을 사용 하는 하나
* 다른 종류의 사용자 어설션을 사용 하는 다른 종류의 보안 토큰을 사용 하 여 이라는 추가 매개 변수에서 형식을 지정 합니다 `assertionType`

![UserAssertion 속성 및 메서드](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

실제로 OBO 흐름은 다운스트림 API에 대 한 토큰을 획득 하 고 MSAL.NET 사용자 토큰 캐시에 저장 하는 데 주로 사용 됩니다. 이렇게 하면 웹 API의 다른 부분이 나중에 다운스트림 Api를 호출 하는 ``AcquireTokenOnSilent``의 [재정의](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) 에 대해를 호출할 수 있습니다. 필요한 경우이 호출은 토큰을 새로 고치는 효과를 가집니다.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

[Node.js 및 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)에서 obo flow 구현의 예제를 확인할 수도 있습니다.

## <a name="protocol"></a>프로토콜

OBO 프로토콜에 대 한 자세한 내용은 [Microsoft id 플랫폼 및 OAuth 2.0 On flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 web API: 응용 프로그램에 대 한 토큰 획득](scenario-web-api-call-api-acquire-token.md)
