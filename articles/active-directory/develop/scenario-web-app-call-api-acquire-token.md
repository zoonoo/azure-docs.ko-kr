---
title: 웹 Api (응용 프로그램에 대 한 토큰을 획득)-Microsoft id 플랫폼을 호출 하는 웹 앱
description: 웹 Api (응용 프로그램에 대 한 토큰 가져오기)를 호출 하는 웹 앱을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074802"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>앱에 대 한 토큰을 획득 하는 웹 Api를 호출 하는 웹 앱

클라이언트 응용 프로그램 개체 만든, 이제 다음 사용 하 여 web Api를 호출 하는 토큰 획득을 사용 합니다. ASP.NET 또는 ASP.NET Core web API 컨트롤러에서 다음 이루어집니다 호출 합니다. 에 대 한 것:

- 웹 토큰 캐시를 사용 하 여 API에 대 한 토큰을 가져오는 중입니다. 이 대 한 호출 `AcquireTokenSilent`
- 액세스 토큰을 사용 하 여 보호 된 API를 호출합니다.

## <a name="aspnet-core"></a>ASP.NET Core

컨트롤러 메서드에 의해 보호 되는 `[Authorize]` 웹 앱을 사용 하도록 인증에서 사용자를 강제로 실행 하는 특성입니다. Microsoft Graph를 호출 하는 코드는 같습니다.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Microsoft Graph를 호출 하는 토큰을 가져오는 HomeController의 작업의 간소화 된 코드는 다음과 같습니다.

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

이 시나리오에 필요한 코드의 전체 세부 정보에서 이해 하려는 경우 2 단계를 참조 하세요 [2-1-웹 앱 호출 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) 의 단계는 [ms-id-aspnetcore-webapp-자습서](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 자습서

와 같은 다양 한 추가 복잡성을 가지 있습니다.

- (이 자습서는 몇 가지 구현이 제공 하는 데 사용) 웹 앱에 대 한 토큰 캐시를 구현
- 사용자 로그인 체크 아웃할 때 캐시에서 계정 제거
- 증분 동의 것을 비롯 한 여러 Api를 호출 합니다.

## <a name="aspnet"></a>ASP.NET

ASP.NET에서 유사한 사항은 다음과 같습니다.

- [Authorize] 특성으로 보호 하는 컨트롤러 작업 ID 및 사용자의 ID 테 넌 트를 추출 합니다는 `ClaimsPrincipal` 컨트롤러의 구성원 (asp `HttpContext.User`)
- 여기에서는 MSAL.NET 작성 `IConfidentialClientApplication`
- IT 호출을 `AcquireTokenSilent` 기밀 클라이언트 응용 프로그램의 메서드 

코드를 ASP.NET Core에 대 한 표시 된 코드에 비슷합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API 호출](scenario-web-app-call-api-call-api.md)
