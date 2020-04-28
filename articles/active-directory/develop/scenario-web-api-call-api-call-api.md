---
title: 웹 Api를 호출 하는 web API-Microsoft id 플랫폼 | Microsoft
description: Web Api를 호출 하는 web API를 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885092"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>웹 api를 호출 하는 web API: API 호출

토큰이 있으면 보호 된 웹 API를 호출할 수 있습니다. 웹 API의 컨트롤러에서이 작업을 수행 합니다.

## <a name="controller-code"></a>컨트롤러 코드

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

다음 코드는 web Api를 호출 하는 web API에 표시 되는 예제 코드를 계속 합니다 [. 앱에 대 한 토큰을 가져옵니다](scenario-web-api-call-api-acquire-token.md). 코드는 API 컨트롤러의 작업에서 호출 됩니다. *Todolist*이라는 다운스트림 API를 호출 합니다.

토큰을 가져온 후에는이 토큰을 전달자 토큰으로 사용 하 여 다운스트림 API를 호출 합니다.

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

다음 코드는 web Api를 호출 하는 web API에 표시 되는 예제 코드를 계속 합니다 [. 앱에 대 한 토큰을 가져옵니다](scenario-web-api-call-api-acquire-token.md). 코드는 API 컨트롤러의 작업에서 호출 됩니다. 다운스트림 API MS Graph를 호출 합니다.

토큰을 가져온 후에는이 토큰을 전달자 토큰으로 사용 하 여 다운스트림 API를 호출 합니다.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
MSAL Python을 사용 하 여이 흐름을 보여 주는 샘플은 아직 사용할 수 없습니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 web API: 프로덕션으로 이동](scenario-web-api-call-api-production.md)
