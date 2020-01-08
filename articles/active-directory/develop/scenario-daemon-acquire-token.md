---
title: 웹 API를 호출 하는 토큰 얻기 (디먼 앱)-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 디먼 앱을 빌드하는 방법 알아보기 (토큰 획득)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58952bdb58619693b31ee4705b6f9f704431657d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423948"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>웹 Api를 호출 하는 디먼 앱-토큰 획득

기밀 클라이언트 응용 프로그램을 생성 한 후에는 ``AcquireTokenForClient``를 호출 하 고, 범위를 전달 하 고, 토큰을 강제로 새로 고치지 않도록 하 여 앱에 대 한 토큰을 가져올 수 있습니다.

## <a name="scopes-to-request"></a>요청할 범위

클라이언트 자격 증명 흐름에 대해 요청할 범위는 리소스 이름 뒤에 `/.default`입니다. 이 표기법은 Azure AD에서 응용 프로그램 등록 중에 정적으로 선언 된 **응용 프로그램 수준 권한을** 사용 하도록 지시 합니다. 또한 앞서 살펴본 것 처럼 테 넌 트 관리자가 이러한 API 권한을 부여 해야 합니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python에서 구성 파일은 다음 코드 조각과 같습니다.

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD (v1.0) 리소스의 사례

클라이언트 자격 증명에 사용 되는 범위는 항상 resourceId + "/.default" 여야 합니다.

> [!IMPORTANT]
> V 1.0 액세스 토큰을 수락 하는 리소스에 대 한 액세스 토큰을 요청 하는 MSAL의 경우, Azure AD는 마지막 슬래시 앞에 있는 모든 항목을 사용 하 고 리소스 식별자로 사용 하 여 요청 된 범위에서 원하는 대상 그룹을 구문 분석 합니다.
> 따라서 Azure SQL ( **https://database.windows.net** )와 마찬가지로 리소스에서 슬래시 (azure sql: `https://database.windows.net/` )로 끝나는 대상 그룹을 예상 하는 경우 `https://database.windows.net//.default` 범위를 요청 해야 합니다 (이중 슬래시). 참고 항목: MSAL.NET issue [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): 리소스 url의 후행 슬래시가 생략 되었으며이로 인해 sql 인증 오류가 발생 했습니다.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

앱에 대 한 토큰을 얻기 위해 플랫폼에 따라 `AcquireTokenForClient` 또는이에 해당 하는를 사용 합니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

[Msal Java dev 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)에서 추출한 것입니다.

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>프로토콜

선택한 언어에 대 한 라이브러리가 아직 없는 경우 프로토콜을 직접 사용 하는 것이 좋습니다.

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용한 액세스 토큰 요청

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용한 액세스 토큰 요청

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

자세한 내용은 프로토콜 설명서 ( [Microsoft id 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md))를 참조 하세요.

## <a name="application-token-cache"></a>응용 프로그램 토큰 캐시

MSAL.NET에서는 **응용 프로그램 토큰 캐시** 를 사용 `AcquireTokenForClient` (다른 모든 AcquireTokenXX 메서드는 사용자 토큰 캐시를 사용 함) `AcquireTokenSilent` **사용자** 토큰 캐시를 사용 하기 때문에 `AcquireTokenForClient`를 호출 하기 전에 `AcquireTokenSilent` 호출 하지 않습니다. `AcquireTokenForClient`는 **응용 프로그램** 토큰 캐시 자체를 확인 하 고 업데이트 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="did-you-use-the-resourcedefault-scope"></a>리소스/. 기본 범위를 사용 했나요?

잘못 된 범위를 사용 했음을 나타내는 오류 메시지가 표시 되 면 `resource/.default` 범위를 사용 하지 않았을 수 있습니다.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>관리자 동의를 제공 해야 하나요? 디먼 앱에 필요 합니다!

API를 호출 **하 여 작업을 완료할 수**없는 경우 오류가 발생 하는 경우 테 넌 트 관리자는 응용 프로그램에 권한을 부여 해야 합니다. 위의 클라이언트 앱 등록 6 단계를 참조 하세요.
일반적으로 다음과 같은 오류 설명과 같은 오류가 표시 됩니다.

```JSon
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

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [디먼 앱-web API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [디먼 앱-web API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [디먼 앱-web API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
