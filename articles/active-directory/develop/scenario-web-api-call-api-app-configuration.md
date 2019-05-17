---
title: 웹 API는 호출 다운스트림 웹 Api (응용 프로그램의 코드 구성)-Microsoft id 플랫폼
description: Web API 호출 web Api (응용 프로그램의 코드 구성)를 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: f62cf65e275d8a9b909bf60103ccbd84e91e4574
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785065"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>웹 API 호출 웹 Api 코드 구성 되는

Web API를 등록 한 후에 응용 프로그램에 대 한 코드를 구성할 수 있습니다.

웹 API를 보호 하는 데 사용 된 코드를 기반으로 다운스트림 웹 Api 호출에 web API를 구성 하는 코드입니다. 자세한 내용은 참조 하세요. [보호 된 웹 API-앱 구성](scenario-protected-web-api-app-configuration.md)합니다.

## <a name="code-subscribed-to-ontokenvalidated"></a>OnTokenValidated 구독할 코드

모든 보호 된 web Api에 대 한 코드 구성을 기반으로 구독에 API를 호출할 때 수신 되는 전달자 토큰의 유효성을 검사 해야 합니다.

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
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
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>On-Behalf-Of 흐름

AddAccountToCacheFromJwt() 메서드 해야합니다.

- MSAL 기밀 클라이언트 응용 프로그램을 인스턴스화하십시오.
- 호출 `AcquireTokenOnBehalf` 동일한 사용자에 있지만 다운스트림 API를 호출 하는 API에 대 한 전달자 토큰에 대 한 web API 용 클라이언트에서 획득 하는 전달자 토큰을 교환할 수 있습니다.

### <a name="instantiate-a-confidential-client-application"></a>기밀 클라이언트 응용 프로그램 인스턴스화

보호 된 web API를 클라이언트 자격 증명 (클라이언트 암호 또는 인증서)를 제공 하므로이 흐름은 기밀 클라이언트 흐름에서 사용할 수 있습니다만 합니다 [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) 를 통해를 `WithClientSecret` 또는 `WithCertificate`메서드를 각각.

![Image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
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

### <a name="how-to-call-on-behalf-of"></a>대신-의 호출 하는 방법

대신-의 (OBO) 호출을 호출 하 여 수행할 합니다 [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) 메서드를 `IConfidentialClientApplication` 인터페이스입니다.

`ClientAssertion` 자체 클라이언트에서 web API에서 받은 전달자 토큰에서 빌드됩니다. 가지 [두 명의 생성자](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), 및 모든 종류의 사용자 어설션 사용 하는 JWT 전달자 토큰을 사용 하는 (다른 유형의 보안 토큰 형식을 다음 라는 추가 매개 변수에서 지정 `assertionType`).

![Image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

실제로 OBO 흐름은 자주 사용 하 여 다운스트림 API에 대 한 토큰을 획득 하 여 web API의 다른 부분에서 나중에 호출할 수 있도록 MSAL.NET 사용자 토큰 캐시에 저장 합니다 [재정의](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) 의 ``AcquireTokenOnSilent`` 다운스트림 Api를 호출 합니다. 필요한 경우이 토큰을 새로 고치 효과가 있습니다.

```CSharp
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

  // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
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

## <a name="protocol"></a>Protocol

대신-의 프로토콜에 대 한 자세한 내용은 참조 하세요. [Microsoft id 플랫폼 및 OAuth 2.0 On-Behalf-Of 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱에 대 한 토큰 가져오기](scenario-web-api-call-api-acquire-token.md)
