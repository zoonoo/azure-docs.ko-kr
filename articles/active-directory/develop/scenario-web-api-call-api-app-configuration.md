---
title: 웹 API를 호출하는 웹 API 구성하기 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 웹 API 빌드 방법 알아보기(앱 코드 구성)
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
ms.openlocfilehash: 5072ae58d3a9412237e70a9bc98970296ce1e1fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686583"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>웹 API를 호출하는 웹 API: 코드 구성

웹 API를 등록하고 나면 애플리케이션에 대한 코드를 구성할 수 있습니다.

웹 API 구성에 사용하여 다운스트림 웹 API 빌드를 웹 API 보호에 사용된 코드 외에 호출하도록 하는 코드. 자세한 내용은 [보호된 웹 API: 앱 구성](scenario-protected-web-api-app-configuration.md)을 참조하세요.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

Microsoft에서는 다운스트림 웹 API를 호출하는 ASP.NET Core 보호 API를 개발할 때 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet 패키지를 사용하는 것을 권장합니다. 웹 API의 컨텍스트 내 해당 라이브러리를 빠르게 표시하려면 [보호된 웹 API: 코드 구성 | Microsoft.Identity.Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb)을 참조하세요.

## <a name="client-secrets-or-client-certificates"></a>클라이언트 암호 또는 클라이언트 인증서

웹 API가 이제 다운스트림 웹 API를 호출하므로 *appsettings.json* 파일에 클라이언트 암호나 클라이언트 인증서를 제공합니다. 다음을 지정하는 섹션을 추가할 수도 있습니다.

- 다운스트림 웹 API의 URL
- 해당 API를 호출하는 데 필요한 범위

다음 예제에서는 `GraphBeta` 섹션이 해당 설정을 지정합니다.

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

클라이언트 암호 대신 클라이언트 인증서를 제공할 수 있습니다. 다음 코드 조각은 Azure Key Vault에 저장된 인증서를 사용하는 방법을 보여 줍니다.

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

Microsoft.Identity.Web에는 구성별 및 코드별로 인증서를 설명하는 몇 가지 방법이 있습니다. 자세한 내용은 [Microsoft.Identity.Web wiki - GitHub에서 인증서 사용하기](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)를 참조하세요.

## <a name="startupcs"></a>Startup.cs

웹 API는 다운스트림 API용 토큰을 획득하여야 합니다. `.AddMicrosoftIdentityWebApi(Configuration)` 뒤에 `.EnableTokenAcquisitionToCallDownstreamApi()` 줄을 추가하여 이를 지정합니다. 이 줄은 컨트롤러/페이지 동작에서 사용할 수 있는 `ITokenAcquisition` 서비스를 공개합니다. 그렇지만 다음 두 개의 글머리 기호에 적힌 내용과 같이, 이를 더 간단하게 수행할 수 있습니다. *Startup.cs* 에서 토큰 캐시 구현(예:`.AddInMemoryTokenCaches()`)을 선택하여야 합니다.

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

토큰을 직접 획득하지 않으려는 경우, *Microsoft.Identity.Web* 에서는 다른 API에서 다운스트림 웹 API를 호출하는 두 가지 메커니즘을 제공합니다. 호출하려는 것이 Microsoft Graph인지 다른 API인지에 따라 선택하는 옵션이 다릅니다.

### <a name="option-1-call-microsoft-graph"></a>옵션 1: Microsoft Graph 호출

Microsoft Graph를 호출하려는 경우, Microsoft.Identity.Web에서는 `GraphServiceClient`(Microsoft Graph SDK에 공개)를 API 작업에 직접 사용할 수 있게 합니다. Microsoft Graph를 공개하려면:

1. [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet 패키지를 프로젝트에 추가합니다.
1. *Startup.cs* 파일에서 `.AddMicrosoftGraph()`를 `.EnableTokenAcquisitionToCallDownstreamApi()` 뒤에 추가합니다. `.AddMicrosoftGraph()`에는 몇 가지 재정의가 있습니다. 구성 섹션을 매개 변수로써 사용하는 재정의를 사용하면 코드가 다음과 같아집니다.

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

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>옵션 2: Microsoft Graph 대신 다운스트림 웹 API 호출

Microsoft Graph 대신 다운스트림 웹 API를 호출하려면, *Microsoft.Identity.Web* 은 토큰을 요구하며 다운스트림 웹 API를 호출하는 `.AddDownstreamWebApi()`를 제공합니다.

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

웹 앱과 마찬가지로 다양한 토큰 캐시 구현을 선택할 수 있습니다. 자세한 내용은 [Microsoft ID 웹 - GitHub의 토큰 캐시 serialization](https://aka.ms/ms-id-web/token-cache-serialization)을 참조하세요.

다음 이미지는 *Microsoft.Identity.Web* 의 다양한 가능성 및 *Startup.cs* 파일에 주는 영향을 보여 줍니다.

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="웹 API 호출 및 토큰 캐시 구현 지정을 위한 Startup.cs의 서비스 구성 옵션을 보여 주는 블록 다이어그램":::

> [!NOTE]
> 여기의 코드 예제를 완전히 이해하려면 [ASP.NET Core 기본](/aspnet/core/fundamentals)에 대하여 잘 알고 있어야 하며, 특히 [종속성 주입](/aspnet/core/fundamentals/dependency-injection) 및 [옵션](/aspnet/core/fundamentals/configuration/options)에 익숙하여야 합니다.

# <a name="java"></a>[Java](#tab/java)

OBO(On-behalf-of) 흐름은 다운스트림 웹 API를 호출하기 위한 토큰을 획득하는 데 사용됩니다. 해당 흐름에서 웹 API는 클라이언트 애플리케이션으로부터 사용자 위임 권한이 있는 전달자 토큰을 받아 다운스트림 웹 API 호출에 필요한 다른 액세스 토큰으로 교환합니다.

아래 코드는 유효성이 확인된 전달자 토큰을 가져오기 위하여 웹 API에서 Spring Security 프레임워크의 `SecurityContextHolder`를 이용합니다. 이어서, MSAL Java 라이브러리를 사용하여 `OnBehalfOfParameters`가 있는 `acquireToken`을 사용하는 다운스트림 API용 토큰을 획득합니다. MSAL은 해당 토큰을 캐시하여 API에 대한 후속 호출이 `acquireTokenSilently`를 사용하여 캐시된 토큰을 얻을 수 있도록 합니다.

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

OBO(On-behalf-of) 흐름은 다운스트림 웹 API를 호출하기 위한 토큰을 획득하는 데 사용됩니다. 해당 흐름에서 웹 API는 클라이언트 애플리케이션으로부터 사용자 위임 권한이 있는 전달자 토큰을 받아 다운스트림 웹 API 호출에 필요한 다른 액세스 토큰으로 교환합니다.

Python 웹 API는 몇 가지 미들웨어를 사용하여 클라이언트에서 받은 전달자 토큰의 유효성을 검사하여야 합니다. 이후, 웹 API는 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 메서드를 호출하여 MSAL Python 라이브러리를 사용하는 다운스트림 API를 위한 액세스 토큰을 획득할 수 있습니다. 해당 API 사용에 대한 예제는 [GitHub의 Python용 Microsoft 인증 라이브러리 테스트 코드](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472)를 참조하세요. 동일한 리포지토리의 [문제 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) 토론에서 중간 계층 애플리케이션 요구를 우회하는 방법에 대하여서도 확인하세요.

[ms-identity-python-on-behalf-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of) 샘플에서 OBO 흐름 구현의 예시도 확인할 수 있습니다.

---

[Node.js 및 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61)에서도 OBO 흐름 구현의 예시를 확인할 수 있습니다.

## <a name="protocol"></a>프로토콜

OBO 프로토콜에 대한 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 On-Behalf-Of 흐름](./v2-oauth2-on-behalf-of-flow.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

본 시나리오의 다음 문서인 [앱용 토큰 획득](scenario-web-api-call-api-acquire-token.md)으로 이동합니다.
