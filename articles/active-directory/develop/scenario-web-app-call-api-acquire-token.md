---
title: 웹 Api를 호출 하는 웹 앱 (앱에 대 한 토큰 획득)-Microsoft identity platform
description: 웹 Api를 호출 하는 웹 앱을 빌드하는 방법 알아보기 (앱에 대 한 토큰 획득)
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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860633"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>웹 Api를 호출 하는 웹 앱-앱에 대 한 토큰을 가져옵니다.

클라이언트 응용 프로그램 개체를 빌드 했으므로 이제이를 사용 하 여 웹 API를 호출 하는 토큰을 가져옵니다. ASP.NET 또는 ASP.NET Core에서 web API 호출은 컨트롤러에서 수행 됩니다. 정보는 다음과 같습니다.

- 토큰 캐시를 사용 하 여 web API에 대 한 토큰을 가져옵니다. 이 토큰을 가져오려면를 호출 `AcquireTokenSilent`합니다.
- 액세스 토큰을 사용 하 여 보호 된 API 호출

## <a name="aspnet-core"></a>ASP.NET Core

컨트롤러 메서드는 웹 앱을 사용 `[Authorize]` 하도록 사용자를 인증 하도록 하는 특성으로 보호 됩니다. Microsoft Graph를 호출 하는 코드는 다음과 같습니다.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Microsoft Graph를 호출 하는 토큰을 가져오는 HomeController 동작의 간소화 된 코드는 다음과 같습니다.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

이 시나리오에 필요한 코드를 보다 철저 하 게 이해 하려면 [aspnetcore-webapp](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 자습서의 2 단계 ([2-1-웹 앱 호출 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) 단계를 참조 하세요.

다음과 같은 다양 한 추가 복잡성이 있습니다.

- 웹 앱에 대 한 토큰 캐시 구현 (자습서에서는 여러 구현을 제공 합니다.)
- 사용자가 로그 아웃할 때 캐시에서 계정 제거
- 증분 동의를 포함 하 여 여러 Api 호출

## <a name="aspnet"></a>ASP.NET

ASP.NET에서 다음과 유사한 항목이 있습니다.

- [권한 부여] 특성으로 보호 되는 컨트롤러 작업은 컨트롤러 `ClaimsPrincipal` 구성원의 테 넌 트 id 및 사용자 id를 추출 합니다. (ASP.NET는 `HttpContext.User`를 사용 합니다.)
- 여기에서 MSAL.NET `IConfidentialClientApplication`을 빌드합니다.
- 마지막으로 기밀 클라이언트 응용 `AcquireTokenSilent` 프로그램의 메서드를 호출 합니다.

코드는 ASP.NET Core에 대해 표시 되는 코드와 비슷합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API 호출](scenario-web-app-call-api-call-api.md)
