---
title: 디먼 앱 호출 web Api (응용 프로그램에 대 한 토큰을 얻기)-Microsoft id 플랫폼
description: 디먼 앱을 작성 하는 방법은 웹 Api (토큰을 얻기)를 호출 되는
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
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075372"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>토큰을 획득 하는 웹 Api를 호출 하는 데몬 앱

기밀 클라이언트 응용 프로그램 생성 되 고 나면 호출 하 여 앱에 대 한 토큰을 얻을 수 있습니다 ``AcquireTokenForClient``, 범위 및 강제 또는 토큰의 새로 고침 되지를 전달 합니다.

## <a name="scopes-to-request"></a>요청에 범위

뒤에 클라이언트 자격 증명 흐름은 리소스의 이름에 대 한 요청에 범위 `/.default`합니다. 이 표기법을 사용 하 여 Azure AD를 나타냅니다 합니다 **응용 프로그램 수준 권한을** 응용 프로그램 등록 중에 정적으로 선언 합니다. 또한 앞서 살펴보았듯이 이러한 API 권한은 부여 해야 테 넌 트 관리자가

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

MSAL에서. Python 구성 파일을 다음 코드 조각 처럼 보입니다.

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>모두

클라이언트 자격 증명에 사용할 범위는 항상 resourceId + "/.default"

### <a name="case-of-v10-resources"></a>V1.0 리소스의 경우

> [!IMPORTANT]
> Azure AD v1.0 액세스 토큰을 수락 하는 리소스에 대 한 액세스 토큰을 요청 MSAL ((v2.0 끝점)에 대 한 마지막 슬래시 앞에 나오는 모든 가져오고 리소스 식별자로 사용 하 여 요청된 된 범위에서 원하는 대상을 구문 분석 합니다.
> 따라서 Azure SQL과 같은 경우 ( **https://database.windows.net** ) 리소스는 슬래시로 끝나는 대상 (Azure SQL에 대 한: `https://database.windows.net/`), 범위를 요청 해야 `https://database.windows.net//.default` (이중 슬래시 참고). MSAL.NET를 참조 하세요. 문제가 [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Sql 인증 실패를 발생 시킨 리소스 url의 후행 슬래시는 생략 됩니다.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

### <a name="net"></a>.NET

```CSharp
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

#### <a name="application-token-cache"></a>응용 프로그램 토큰 캐시

MSAL.NET을에서 `AcquireTokenForClient` 사용 하는 **응용 프로그램 토큰 캐시** (다른 모든 AcquireTokenXX 메서드는 사용자 토큰 캐시를 사용 하는 데 사용)를 호출 하지 마세요 `AcquireTokenSilent` 호출 하기 전에 `AcquireTokenForClient` 으로 `AcquireTokenSilent` 사용 합니다 **사용자** 토큰 캐시입니다. `AcquireTokenForClient` 확인 합니다 **응용 프로그램** 토큰 자체를 캐시 하 고 업데이트 합니다.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

없는 아직 프로토콜을 직접 사용 하려는 선택한 언어에 대 한 라이브러리:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 비밀을 사용하여 액세스 토큰 요청

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용하여 액세스 토큰 요청

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

### <a name="learn-more-about-the-protocol"></a>프로토콜에 자세히 알아보기

자세한 내용은 프로토콜 설명서를 참조 하세요. [Azure Active Directory v2.0 및 OAuth 2.0 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="did-you-use-the-resourcedefault-scope"></a>리소스/.default 범위 사용 했나요?

잘못 된 범위를 사용 하는, 아마도 사용 하지 않은 사용자에 게 알리는 오류 메시지가 표시 되는 경우는 `resource/.default` 범위입니다.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>관리자 동의 제공 하 시겠습니까? 디먼 앱이 필요합니다.

API를 호출할 때 오류가 발생 하는 경우 **작업을 완료할 수 있는 권한이**, 테 넌 트 관리자가 응용 프로그램에 권한을 부여 해야 합니다. 위의 클라이언트 앱 등록의 6 단계를 참조 하세요.
일반적으로 표시 하 고 오류 같은 오류 설명:

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

> [!div class="nextstepaction"]
> [디먼 앱-web API 호출](scenario-daemon-call-api.md)