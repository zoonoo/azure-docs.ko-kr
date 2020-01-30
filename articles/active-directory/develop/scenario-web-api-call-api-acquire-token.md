---
title: Web Api를 호출 하는 웹 API에 대 한 토큰을 가져옵니다. Microsoft
titleSuffix: Microsoft identity platform
description: 앱에 대 한 토큰을 획득 해야 하는 웹 Api를 호출 하는 web API를 빌드하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834113"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>웹 Api를 호출 하는 web API: 응용 프로그램에 대 한 토큰 획득

클라이언트 응용 프로그램 개체를 빌드한 후에는이 개체를 사용 하 여 web API를 호출 하는 데 사용할 수 있는 토큰을 가져옵니다.

## <a name="code-in-the-controller"></a>컨트롤러의 코드

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

API 컨트롤러의 작업에서 호출 되는 코드의 예는 다음과 같습니다. *Todolist*이라는 다운스트림 API를 호출 합니다.

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

`BuildConfidentialClient()` [웹 api를 호출 하는 웹 api](scenario-web-api-call-api-app-configuration.md)의 시나리오와 비슷합니다. 앱 구성. `BuildConfidentialClient()`는 한 계정에 대 한 정보만 포함 하는 캐시를 사용 하 여 `IConfidentialClientApplication`를 인스턴스화합니다. 계정은 `GetAccountIdentifier` 메서드에서 제공 합니다.

`GetAccountIdentifier` 메서드는 web API가 JSON Web Token (JWT)를 받은 사용자의 id와 연결 된 클레임을 사용 합니다.

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

# <a name="javatabjava"></a>[Java](#tab/java)
API 컨트롤러의 작업에서 호출 되는 코드의 예는 다음과 같습니다. 다운스트림 API Microsoft Graph를 호출 합니다.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Python web API는 클라이언트에서 받은 전달자 토큰의 유효성을 검사 하기 위해 일부 미들웨어를 사용 해야 합니다. Web API는 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 메서드를 호출 하 여 Msal Python 라이브러리를 사용 하 여 다운스트림 api에 대 한 액세스 토큰을 가져올 수 있습니다. MSAL Python을 사용 하 여이 흐름을 보여 주는 샘플은 아직 사용할 수 없습니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 api를 호출 하는 web API: API 호출](scenario-web-api-call-api-call-api.md)
