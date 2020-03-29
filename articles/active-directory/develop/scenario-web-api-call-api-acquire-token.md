---
title: 웹 API를 호출하는 웹 API에 대한 토큰 받기 | Azure
titleSuffix: Microsoft identity platform
description: 앱에 대한 토큰을 획득해야 하는 웹 API를 호출하는 웹 API를 빌드하는 방법을 알아봅니다.
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
ms.openlocfilehash: 2721837459af24f39bb15ee17d394345cbb37eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834113"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>웹 API를 호출하는 웹 API: 앱에 대한 토큰 획득

클라이언트 응용 프로그램 개체를 빌드한 후 이 개체를 사용하여 웹 API를 호출하는 데 사용할 수 있는 토큰을 획득합니다.

## <a name="code-in-the-controller"></a>컨트롤러의 코드

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

다음은 API 컨트롤러의 작업에서 호출되는 코드의 예입니다. *todolist라는*다운스트림 API를 호출합니다.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()`웹 [API: 앱 구성을 호출하는 웹 API의](scenario-web-api-call-api-app-configuration.md)시나리오와 유사합니다. `BuildConfidentialClient()`하나의 계정에 `IConfidentialClientApplication` 대한 정보가 들어 있는 캐시로 인스턴스화됩니다. 계정은 메서드에 `GetAccountIdentifier` 의해 제공됩니다.

이 `GetAccountIdentifier` 메서드는 웹 API가 JSON 웹 토큰(JWT)을 받은 사용자의 ID와 연결된 클레임을 사용합니다.

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
다음은 API 컨트롤러의 작업에서 호출되는 코드의 예입니다. 그것은 다운 스트림 API를 호출 - 마이크로 소프트 그래프.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

파이썬 웹 API는 클라이언트에서 받은 베어러 토큰의 유효성을 검사하기 위해 일부 미들웨어를 사용해야 합니다. 그런 다음 웹 API는 메서드를 호출하여 MSAL Python 라이브러리를 사용하여 다운스트림 API에 대한 액세스 토큰을 가져올 수 있습니다. [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) MSAL 파이썬을 사용하여 이 흐름을 보여 주는 샘플은 아직 사용할 수 없습니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API를 호출하는 웹 API: API 호출](scenario-web-api-call-api-call-api.md)
