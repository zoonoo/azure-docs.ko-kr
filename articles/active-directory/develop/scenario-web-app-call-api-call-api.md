---
title: 웹 앱 호출 웹 Api (web API 호출)-되는 Microsoft id 플랫폼
description: 웹 Api (web API 호출)를 호출 하는 웹 앱을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074637"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>웹 Api를 호출 하는 웹 앱에 web API 호출

토큰을 설정 했으므로 보호 된 web API를 호출할 수 있습니다.

## <a name="aspnet-core"></a>ASP.NET Core

다음은 작업의 간소화 된 코드는 `HomeController`합니다. 이 코드는 Microsoft Graph를 호출 하는 토큰을 가져옵니다. 이 시간 코드는 REST API로 Microsoft Graph를 호출 하는 방법을 보여 주는 추가 되었습니다.

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

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> 웹 API를 호출 하는 동일한 원칙을 사용할 수 있습니다.
>
> 대부분의 Azure 웹 Api 호출을 간소화 하는 SDK를 제공 합니다. Microsoft Graph의 경우 이기도합니다. 이러한 측면을 보여 주는 자습서를 찾을 수 있는 위치는 다음 기사에서 설명 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-web-app-call-api-production.md)
