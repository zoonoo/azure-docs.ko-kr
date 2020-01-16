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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cf660cbf981079ca20111e34fcd34504d8dcbfb
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044124"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>웹 Api를 호출 하는 web API: 응용 프로그램에 대 한 토큰 획득

클라이언트 응용 프로그램 개체를 빌드한 후에는이 개체를 사용 하 여 web API를 호출 하는 데 사용할 수 있는 토큰을 가져옵니다.

## <a name="code-in-the-controller"></a>컨트롤러의 코드

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 api를 호출 하는 web API: API 호출](scenario-web-api-call-api-call-api.md)
