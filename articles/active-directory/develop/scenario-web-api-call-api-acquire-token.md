---
title: 웹 API는 호출에는 다른 웹 Api에 (앱 토큰을 획득)-Microsoft id 플랫폼
description: Web API 호출에는 다른 웹 Api (응용 프로그램에 대 한 토큰 가져오기)를 빌드하는 방법에 알아봅니다.
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65231104"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>웹 Api를 호출 하는 web API 앱에 대 한 토큰을 획득

클라이언트 응용 프로그램을 작성 한 후 개체를 사용 하 여 web API를 호출 하는 데 사용할 수 있는 토큰을 획득 합니다.

## <a name="code-in-the-controller"></a>컨트롤러의 코드

API 컨트롤러를 todolist 붙여진 다운스트림 API 호출의 동작에서 호출 되는 코드의 예는 다음과 같습니다.

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
}
```

`BuildConfidentialClient()` 문서의 살펴봤습니다 비슷합니다 [Web API를 호출 하는 web Api-앱 구성](scenario-web-api-call-api-app-configuration.md)합니다. `BuildConfidentialClient()` 인스턴스화합니다 `IConfidentialClientApplication` 하나의 계정에 대 한 정보만 포함 하는 캐시를 사용 하 여 합니다. 계정에서 제공 되는 `GetAccountIdentifier` 메서드.

`GetAccountIdentifier` 메서드는 웹 API는 JWT를 받은 사용자의 id와 연결 된 클레임을 사용 합니다.

```CSharp
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
> [Web API 호출](scenario-web-api-call-api-call-api.md)
