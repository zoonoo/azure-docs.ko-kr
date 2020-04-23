---
title: 웹 API (데몬 앱)를 호출하는 토큰을 획득 - 마이크로 소프트 ID 플랫폼 | Azure
description: 웹 API를 호출하는 데몬 앱을 빌드하는 방법 알아보기(토큰 획득)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d755573b53eb63d85165fb73fe4b97298dbeff09
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868987"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>웹 API를 호출하는 데몬 앱 - 토큰 획득

기밀 클라이언트 응용 프로그램을 생성한 후 호출, `AcquireTokenForClient`범위 전달 및 선택적으로 토큰 새로 고침을 강제로 앱에 대한 토큰을 획득할 수 있습니다.

## <a name="scopes-to-request"></a>요청할 범위

클라이언트 자격 증명 흐름을 요청하는 범위는 리소스 의 이름 `/.default`다음에 있습니다. 이 표기법은 Azure Active Directory(Azure AD)가 응용 프로그램 등록 중에 정적으로 선언된 응용 프로그램 수준 권한을 사용하도록 *지시합니다.* 또한 이러한 API 권한은 테넌트 관리자가 부여해야 합니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python에서 구성 파일은 이 코드 조각처럼 보입니다.

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD(v1.0) 리소스

클라이언트 자격 증명에 사용되는 범위는 항상 리소스 `/.default`ID 다음에 이와 같은 범위여야 합니다.

> [!IMPORTANT]
> MSAL이 버전 1.0 액세스 토큰을 허용하는 리소스에 대한 액세스 토큰을 요청하면 Azure AD는 마지막 슬래시 전에 모든 것을 취하고 이를 리소스 식별자로 사용하여 요청된 범위에서 원하는 대상을 구문 분석합니다.
> 따라서 Azure SQL `https://database.windows.net/`Database(https: `https://database.windows.net//.default`**\//database.windows.net)와**같이 리소스가 슬래시로 끝나는 대상(Azure SQL Database의 경우)을 예상하는 경우 의 범위를 요청해야 합니다. (이중 슬래시를 확인합니다.) #747 MSAL.NET 문제 [참조: 리소스 url의 후행 슬래시가 생략되어 sql auth 오류가 발생했습니다.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)

## <a name="acquiretokenforclient-api"></a>인수토큰포클라이언트 API

앱에 대한 토큰을 획득하려면 플랫폼에 `AcquireTokenForClient` 따라 해당 토큰을 사용하거나 그에 상응하는 토큰을 사용합니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

이 코드는 [MSAL Java 개발자 샘플에서 추출됩니다.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>프로토콜

선택한 언어에 대한 라이브러리가 아직 없는 경우 프로토콜을 직접 사용할 수 있습니다.

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>첫 번째 경우: 공유 비밀을 사용하여 토큰 요청에 액세스

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>두 번째 경우: 인증서를 사용하여 토큰 요청에 액세스

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

자세한 내용은 프로토콜 설명서를 참조하십시오: [Microsoft ID 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름.](v2-oauth2-client-creds-grant-flow.md)

## <a name="application-token-cache"></a>응용 프로그램 토큰 캐시

MSAL.NET 응용 `AcquireTokenForClient` 프로그램 토큰 캐시를 사용합니다. (다른 모든 AcquireToken*XX* 메서드는 사용자 토큰 캐시를 사용합니다.) *호출하기* 전에 `AcquireTokenSilent` `AcquireTokenForClient` `AcquireTokenSilent` 호출하지 마십시오. `AcquireTokenForClient`응용 *프로그램* 토큰 캐시 자체를 검사하고 업데이트합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="did-you-use-the-resourcedefault-scope"></a>리소스/.기본 범위를 사용했습니까?

잘못된 범위를 사용했다는 오류 메시지가 나오면 `resource/.default` 범위를 사용하지 않았을 수 있습니다.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>관리자 동의를 제공하는 것을 잊었습니까? 데몬 애플 리케이션이 필요합니다!

API를 호출할 때 작업 오류를 **완료하기에 부족한 권한이** 있는 경우 테넌트 관리자는 응용 프로그램에 대한 권한을 부여해야 합니다. 위의 클라이언트 앱 등록 6단계를 참조하십시오.
일반적으로 다음과 같은 오류가 표시됩니다.

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [데몬 앱 - 웹 API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [데몬 앱 - 웹 API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [데몬 앱 - 웹 API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
