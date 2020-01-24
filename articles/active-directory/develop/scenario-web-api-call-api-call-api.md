---
title: 웹 Api를 호출 하는 web API-Microsoft id 플랫폼 | Microsoft
description: Web Api를 호출 하는 web API를 빌드하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: c7c54c7c4718cea479a812574e961ef2338a6be6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701759"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>웹 api를 호출 하는 web API: API 호출

토큰이 있으면 보호 된 웹 API를 호출할 수 있습니다. ASP.NET 또는 ASP.NET Core web API의 컨트롤러에서이 작업을 수행 합니다.

## <a name="controller-code"></a>컨트롤러 코드

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 web API: 프로덕션으로 이동](scenario-web-api-call-api-production.md)
