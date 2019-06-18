---
title: 웹 API를 호출 하는 웹 Api (Api 호출)-Microsoft id 플랫폼
description: Web API는 호출 다운스트림 웹 Api (web API 호출)를 빌드하는 방법을 알아봅니다.
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
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074727"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>웹 Api를 호출 하는 웹 API는 API를 호출 합니다.

토큰을 만든 후에 보호 된 web API를 호출할 수 있습니다. ASP.NET/ASP.NET Core web API 컨트롤러에서 수행 됩니다.

## <a name="controller-code"></a>컨트롤러 코드

에 표시 된 예제 코드를 연속 다음과 같습니다 [web API 호출 보호 된 web Api-토큰을 확보](scenario-web-api-call-api-acquire-token.md)라는 API 컨트롤러의 작업을 todolist 붙여진 다운스트림 API를 호출 합니다.

토큰을 획득 한 후 사용 전달자 토큰으로 다운스트림 API를 호출 합니다.

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-web-api-call-api-production.md)
