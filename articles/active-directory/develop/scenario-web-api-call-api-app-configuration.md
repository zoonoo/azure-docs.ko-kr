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
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9fe4d5cfb4b0b31a257dfecdeeb191a9c7920772
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403173"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>웹 Api를 호출 하는 웹 API: 코드 구성

Web API를 등록 한 후에는 응용 프로그램에 대 한 코드를 구성할 수 있습니다.

웹 api를 구성 하는 데 사용 하는 코드는 web API를 보호 하는 데 사용 되는 코드를 기반으로 하 여 다운스트림 웹 api를 호출 합니다. 자세한 내용은 [보호 된 웹 API: 앱 구성](scenario-protected-web-api-app-configuration.md)을 참조 하세요.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. Identity. 웹

다운스트림 웹 Api를 호출 하는 ASP.NET Core 보호 API를 개발 하는 경우에는 [microsoft에서 Id 웹](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet 패키지를 사용 하는 것이 좋습니다. [보호 된 웹 API: 코드 구성을 참조 하세요. ](scenario-protected-web-api-app-configuration.md#microsoftidentityweb)WEB API의 컨텍스트에서 해당 라이브러리를 빠르게 표시 하기 위한 Microsoft. Identity.

## <a name="client-secrets-or-client-certificates"></a>클라이언트 암호 또는 클라이언트 인증서

이제 web API가 다운스트림 웹 API를 호출 하는 경우 파일의 *appsettings.js* 에 클라이언트 암호 또는 클라이언트 인증서를 제공 해야 합니다. 다음을 지정 하는 섹션을 추가할 수도 있습니다.

- 다운스트림 웹 API의 URL
- API를 호출 하는 데 필요한 범위

다음 예에서는 `GraphBeta` 섹션에서 이러한 설정을 지정 합니다.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

클라이언트 암호 대신 클라이언트 인증서를 제공할 수 있습니다. 다음 코드 조각에서는 Azure Key Vault에 저장 된 인증서를 사용 하는 방법을 보여 줍니다.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft. Identity는 구성 또는 코드를 통해 인증서를 설명 하는 여러 가지 방법을 제공 합니다. 자세한 내용은 [Microsoft Id 웹 wiki-GitHub에서 인증서 사용](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) 을 참조 하세요.

## <a name="startupcs"></a>Startup.cs

Web API는 다운스트림 API에 대 한 토큰을 획득 해야 합니다. 뒤에 줄을 추가 하 여 지정 `.EnableTokenAcquisitionToCallDownstreamApi()` `.AddMicrosoftIdentityWebApi(Configuration)` 합니다. 이 줄은 `ITokenAcquisition` 컨트롤러/페이지 동작에서 사용할 수 있는 서비스를 노출 합니다. 그러나 다음 두 가지 글머리 기호에서 볼 수 있듯이 훨씬 더 간단 하 게 수행할 수 있습니다. `.AddInMemoryTokenCaches()` *Startup.cs*에서와 같이 토큰 캐시 구현도 선택 해야 합니다.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

토큰을 직접 획득 하지 않으려는 경우에는 다른 API에서 다운스트림 웹 API를 호출 하는 두 가지 메커니즘 *을 제공 합니다.* 선택 하는 옵션은 Microsoft Graph 또는 다른 API를 호출 하는지 여부에 따라 달라 집니다.

### <a name="option-1-call-microsoft-graph"></a>옵션 1: Microsoft Graph 호출

Microsoft Graph를 호출 하려는 경우에는 `GraphServiceClient` API 작업에서 (MICROSOFT GRAPH SDK에 의해 노출 됨)를 직접 사용할 수 있습니다. Microsoft Graph를 노출 하려면:

1. [Microsoft.azure.webjobs.extensions.microsoftgraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet 패키지를 프로젝트에 추가 합니다.
1. `.AddMicrosoftGraph()` `.EnableTokenAcquisitionToCallDownstreamApi()` *Startup.cs* 파일에서 뒤에를 추가 합니다. `.AddMicrosoftGraph()` 에는 여러 가지 재정의가 있습니다. 구성 섹션을 매개 변수로 사용 하는 재정의를 사용 하면 코드가 다음과 같이 됩니다.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>옵션 2: Microsoft Graph 이외의 다운스트림 웹 API 호출

Microsoft Graph 이외의 다운스트림 API를 호출 하려면 토큰을 *Microsoft.Identity.Web* `.AddDownstreamWebApi()` 요청 하 고 다운스트림 웹 API를 호출 하는을 제공 합니다.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

웹 앱과 마찬가지로 다양 한 토큰 캐시 구현을 선택할 수 있습니다. 자세한 내용은 GitHub의 [Microsoft id 웹 토큰 캐시 serialization](https://aka.ms/ms-id-web/token-cache-serialization) 을 참조 하세요.

다음 이미지는 *Startup.cs* 파일에 대 한 다양 한 *Microsoft id* 및 해당 영향을 보여 줍니다.

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="웹 API를 호출 하 고 토큰 캐시 구현을 지정 하는 시작 점 C의 서비스 구성 옵션을 보여 주는 블록 다이어그램":::

> [!NOTE]
> 여기에서 코드 예제를 완전히 이해하려면 [ASP.NET Core 기본](/aspnet/core/fundamentals), 특히 [종속성 주입](/aspnet/core/fundamentals/dependency-injection) 및 [옵션](/aspnet/core/fundamentals/configuration/options)에 대해 잘 알고 있어야 합니다.

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

OBO 프로토콜에 대 한 자세한 내용은 [Microsoft id 플랫폼 및 OAuth 2.0 On flow](./v2-oauth2-on-behalf-of-flow.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 web API: 응용 프로그램에 대 한 토큰 획득](scenario-web-api-call-api-acquire-token.md)
