---
title: 웹 API를 호출하는 웹 API 구성 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API(앱의 코드 구성)를 호출하는 웹 API를 빌드하는 방법 알아보기
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
ms.openlocfilehash: 4129f1a89575c9a0e7cd6a0090168df659356c1b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885109"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>웹 API를 호출하는 웹 API: 코드 구성

웹 API를 등록한 후 응용 프로그램에 대한 코드를 구성할 수 있습니다.

다운스트림 웹 API를 호출하도록 웹 API를 구성하는 데 사용하는 코드는 웹 API를 보호하는 데 사용되는 코드 위에 빌드됩니다. 자세한 내용은 [보호된 웹 API: 앱 구성을](scenario-protected-web-api-app-configuration.md)참조하십시오.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>OnTokenValidated에 가입된 코드

보호된 웹 API에 대한 코드 구성 외에도 API가 호출될 때 받는 베어러 토큰의 유효성 검사를 구독해야 합니다.

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

AddAccountToCacheFromJwt() 메서드는 다음을 수행해야 합니다.

- MSAL(기밀 클라이언트 응용 프로그램) Microsoft 인증 라이브러리를 인스턴스화합니다.
- `AcquireTokenOnBehalf` 메서드를 호출합니다. 이 호출은 동일한 사용자에 대 한 베어러 토큰에 대 한 웹 API에 대 한 클라이언트에 의해 수집 된 베어러 토큰을 교환 하지만 API 는 다운 스트림 API를 호출 합니다.

### <a name="instantiate-a-confidential-client-application"></a>기밀 클라이언트 응용 프로그램 인스턴스화

이 흐름은 보호된 웹 API가 `WithClientSecret` Client `WithCertificate` 자격 증명(클라이언트 비밀 또는 인증서)을 [ConfidentialClientApplicationBuilder 클래스에](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) 제공하거나 메서드를 통해 제공하도록 기밀 클라이언트 흐름에서만 사용할 수 있습니다.

![I기밀클라이언트응용 프로그램 목록](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

마지막으로, 클라이언트 보안 또는 인증서를 통해 자신의 ID를 증명하는 대신 기밀 클라이언트 응용 프로그램은 클라이언트 어설션을 사용하여 자신의 ID를 증명할 수 있습니다.
이 고급 시나리오에 대한 자세한 내용은 [기밀 클라이언트 어설션을](msal-net-client-assertions.md)참조하십시오.

### <a name="how-to-call-on-behalf-of"></a>대신 전화하는 방법

인터페이스에서 [AcquireTokenOnBehalf 메서드를](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) 호출하여 OBO(OBO)를 대신하여 `IConfidentialClientApplication` 호출합니다.

클래스는 `UserAssertion` 자체 클라이언트에서 웹 API에 의해 수신되는 베어러 토큰에서 빌드됩니다. 두 개의 [생성자가 있습니다.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)
* JSON 웹 토큰(JWT) 베어러 토큰을 받는 분
* 모든 종류의 사용자 어설션을 취하는 사용자 어설션, 다른 종류의 보안 토큰을 사용하며, 그 형식은 명명된 추가 매개 변수에 지정됩니다.`assertionType`

![사용자 어설션 속성 및 메서드](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

실제로 OBO 흐름은 다운스트림 API에 대한 토큰을 획득하고 MSAL.NET 사용자 토큰 캐시에 저장하는 데 자주 사용됩니다. 이렇게 하면 웹 API의 다른 부분에서 나중에 다운스트림 API를 ``AcquireTokenOnSilent`` 호출하는 [재정의를](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) 호출할 수 있습니다. 이 호출은 필요한 경우 토큰을 새로 고치는 효과가 있습니다.

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

OBO(대신) 흐름은 다운스트림 웹 API를 호출하는 토큰을 가져오는 데 사용됩니다. 이 흐름에서 웹 API는 클라이언트 응용 프로그램에서 사용자 위임 된 권한이있는 베어러 토큰을 받은 다음 이 토큰을 다른 액세스 토큰으로 교환하여 다운스트림 웹 API를 호출합니다.

아래 코드는 웹 API에서 `SecurityContextHolder` Spring Security 프레임워크를 사용하여 검증된 베어러 토큰을 가져옵니다. 그런 다음 MSAL Java 라이브러리를 사용하여 `acquireToken` `OnBehalfOfParameters`를 사용하여 다운스트림 API에 대한 토큰을 가져옵니다. MSAL은 API에 대한 후속 호출이 캐시된 토큰을 얻는 데 사용할 `acquireTokenSilently` 수 있도록 토큰을 캐시합니다.

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

OBO(대신) 흐름은 다운스트림 웹 API를 호출하는 토큰을 가져오는 데 사용됩니다. 이 흐름에서 웹 API는 클라이언트 응용 프로그램에서 사용자 위임 된 권한이있는 베어러 토큰을 받은 다음 이 토큰을 다른 액세스 토큰으로 교환하여 다운스트림 웹 API를 호출합니다.

파이썬 웹 API는 클라이언트에서 받은 베어러 토큰의 유효성을 검사하기 위해 일부 미들웨어를 사용해야 합니다. 그런 다음 웹 API는 메서드를 호출하여 MSAL Python 라이브러리를 사용하여 다운스트림 API에 대한 액세스 토큰을 가져올 수 있습니다. [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) MSAL 파이썬을 사용하여 이 흐름을 보여 주는 샘플은 아직 사용할 수 없습니다.

---

또한 [Node.js 및 Azure 함수에서](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)OBO 흐름 구현의 예를 볼 수 있습니다.

## <a name="protocol"></a>프로토콜

OBO 프로토콜에 대한 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 On-For-of flow를](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)참조하십시오.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API를 호출하는 웹 API: 앱에 대한 토큰 획득](scenario-web-api-call-api-acquire-token.md)
