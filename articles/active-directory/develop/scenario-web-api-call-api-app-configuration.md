---
title: 웹 Api를 호출 하는 web API 구성 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 web API를 빌드하는 방법 알아보기 (앱 코드 구성)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80991010"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>웹 Api를 호출 하는 웹 API: 코드 구성

Web API를 등록 한 후에는 응용 프로그램에 대 한 코드를 구성할 수 있습니다.

웹 api를 구성 하는 데 사용 하는 코드는 web API를 보호 하는 데 사용 되는 코드를 기반으로 하 여 다운스트림 웹 api를 호출 합니다. 자세한 내용은 [보호 된 웹 API: 앱 구성](scenario-protected-web-api-app-configuration.md)을 참조 하세요.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

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

## <a name="on-behalf-of-flow"></a>On-Behalf-Of 흐름

AddAccountToCacheFromJwt () 메서드는 다음을 수행 해야 합니다.

- MSAL (Microsoft 인증 라이브러리) 기밀 클라이언트 응용 프로그램을 인스턴스화합니다.
- `AcquireTokenOnBehalf` 메서드를 호출합니다. 이 호출은 클라이언트에서 web API에 대해 획득 한 전달자 토큰을 동일한 사용자의 전달자 토큰에 대해 교환 하지만 API 호출을 다운스트림 API로 사용 합니다.

### <a name="instantiate-a-confidential-client-application"></a>기밀 클라이언트 응용 프로그램 인스턴스화

이 흐름은 기밀 클라이언트 흐름 에서만 사용할 수 있으므로 보호 된 웹 API는 또는 메서드를 통해 [ConfidentialClientApplicationBuilder 클래스](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) 에 클라이언트 자격 증명 (클라이언트 암호 또는 인증서)을 제공 `WithClientSecret` 합니다 `WithCertificate` .

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

인터페이스에서 [AcquireTokenOnBehalf 메서드](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) 를 호출 하 여 obo (주문형) 호출을 수행 합니다 `IConfidentialClientApplication` .

`UserAssertion`클래스는 자체 클라이언트에서 WEB API가 수신 하는 전달자 토큰을 기반으로 빌드됩니다. 다음과 같은 [두 가지 생성자](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)가 있습니다.
* JWT (JSON Web Token) 전달자 토큰을 사용 하는 하나
* 다른 종류의 보안 토큰을 사용 하는 다른 종류의 보안 토큰을 사용 하 여 이름이 인 추가 매개 변수에서 지정 됩니다.`assertionType`

![UserAssertion 속성 및 메서드](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

실제로 OBO 흐름은 다운스트림 API에 대 한 토큰을 획득 하 고 MSAL.NET 사용자 토큰 캐시에 저장 하는 데 주로 사용 됩니다. 이렇게 하면 웹 API의 다른 부분이 나중에의 [재정의](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) 를 호출 ``AcquireTokenOnSilent`` 하 여 다운스트림 api를 호출할 수 있습니다. 필요한 경우이 호출은 토큰을 새로 고치는 효과를 가집니다.

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
# <a name="java"></a>[Java](#tab/java)

(OBO) 흐름은 다운스트림 웹 API를 호출 하는 토큰을 가져오는 데 사용 됩니다. 이 흐름에서 web API는 클라이언트 응용 프로그램에서 사용자 위임 권한이 있는 전달자 토큰을 받은 다음 다른 액세스 토큰에 대해이 토큰을 교환 하 여 다운스트림 웹 API를 호출 합니다.

아래 코드는 web API의 스프링 보안 프레임 워크를 사용 하 여 `SecurityContextHolder` 유효성이 검사 된 전달자 토큰을 가져옵니다. 그런 다음 MSAL Java 라이브러리를 사용 하 여 호출을 통해 다운스트림 API에 대 한 토큰을 가져옵니다 `acquireToken` `OnBehalfOfParameters` . MSAL은 API에 대 한 후속 호출을 사용 하 여 캐시 된 토큰을 가져올 수 있도록 토큰을 캐시 합니다 `acquireTokenSilently` .

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

(OBO) 흐름은 다운스트림 웹 API를 호출 하는 토큰을 가져오는 데 사용 됩니다. 이 흐름에서 web API는 클라이언트 응용 프로그램에서 사용자 위임 권한이 있는 전달자 토큰을 받은 다음 다른 액세스 토큰에 대해이 토큰을 교환 하 여 다운스트림 웹 API를 호출 합니다.

Python web API는 클라이언트에서 받은 전달자 토큰의 유효성을 검사 하기 위해 일부 미들웨어를 사용 해야 합니다. Web API는 메서드를 호출 하 여 MSAL Python 라이브러리를 사용 하 여 다운스트림 API에 대 한 액세스 토큰을 가져올 수 있습니다 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . 이 API를 사용 하는 예제는 GitHub의 [microsoft-인증 라이브러리-python 용 테스트 코드](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472)를 참조 하세요. 중간 계층 응용 프로그램의 요구를 우회 하는 방법에 대해서도 동일한 리포지토리의 [문제 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) 에 대 한 설명을 참조 하세요.

---

[Node.js 및 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)에서 obo flow 구현의 예제를 확인할 수도 있습니다.

## <a name="protocol"></a>프로토콜

OBO 프로토콜에 대 한 자세한 내용은 [Microsoft id 플랫폼 및 OAuth 2.0 On flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 web API: 응용 프로그램에 대 한 토큰 획득](scenario-web-api-call-api-acquire-token.md)
