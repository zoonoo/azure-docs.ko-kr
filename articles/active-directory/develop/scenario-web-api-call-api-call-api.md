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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6063d143e2f217426bdf1db217fde46f8542d314
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965214"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>웹 api를 호출 하는 web API-API 호출

토큰이 있으면 보호 된 웹 API를 호출할 수 있습니다. ASP.NET/ASP.NET Core web API의 컨트롤러에서 수행 됩니다.

## <a name="controller-code"></a>컨트롤러 코드

보호 된 웹 API에 표시 된 예제 코드의 연속은 API 컨트롤러의 작업에서 호출 되는 토큰을 획득 하 고 다운스트림 API (todolist)를 호출 하는 [웹 api를 호출](scenario-web-api-call-api-acquire-token.md)합니다.

토큰을 얻은 후에는이 토큰을 전달자 토큰으로 사용 하 여 다운스트림 API를 호출 합니다.

```CSharp
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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-web-api-call-api-production.md)
