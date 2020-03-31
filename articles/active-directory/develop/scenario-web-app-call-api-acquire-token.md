---
title: 웹 API를 호출하는 웹 앱에서 토큰 받기 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 웹 앱에 대한 토큰을 획득하는 방법 알아보기
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759077"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>웹 API를 호출하는 웹 앱: 앱에 대한 토큰 획득

클라이언트 응용 프로그램 개체를 빌드했습니다. 이제 이를 사용하여 토큰을 획득하여 웹 API를 호출합니다. ASP.NET 또는 ASP.NET 코어에서는 컨트롤러에서 웹 API 호출이 수행됩니다.

- 토큰 캐시를 사용하여 웹 API에 대한 토큰을 가져옵니다. 이 토큰을 얻으려면 메서드를 호출합니다. `AcquireTokenSilent`
- 보호된 API를 호출하여 액세스 토큰을 매개 변수로 전달합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

컨트롤러 메서드는 사용자가 `[Authorize]` 웹 앱을 사용하도록 인증되는 특성에 의해 보호됩니다. 다음은 Microsoft Graph를 호출하는 코드입니다.

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

서비스는 `ITokenAcquisition` 종속성 주입을 사용하여 ASP.NET 의해 주입됩니다.

다음은 Microsoft Graph를 호출하는 토큰을 얻는 `HomeController`의 작업에 대한 단순화된 코드입니다.

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

이 시나리오에 필요한 코드를 더 잘 이해하려면 [ms-identity-aspnetcore-webapp-자습서](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 자습서의[2단계(2-1-웹 앱 호출 Microsoft Graph)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)단계를 참조하십시오.

다음과 같은 다른 복잡한 변형이 있습니다.

- 여러 API를 호출합니다.
- 증분 동의 및 조건부 액세스 처리.

이러한 고급 단계는 [3-WebApp 다중 API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) 자습서의 3장에서 다룹니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 대한 코드는 ASP.NET Core에 표시된 코드와 유사합니다.

- [권한 부여] 특성으로 보호되는 컨트롤러 작업은 컨트롤러 멤버의 테넌트 ID 및 사용자 ID를 `ClaimsPrincipal` 추출합니다. (ASP.NET `HttpContext.User`사용 .)
- 거기에서 MSAL.NET `IConfidentialClientApplication` 개체를 빌드합니다.
- 마지막으로 기밀 클라이언트 `AcquireTokenSilent` 응용 프로그램의 메서드를 호출합니다.

# <a name="java"></a>[Java](#tab/java)

Java 샘플에서 API를 호출하는 코드는 [AuthPageController.java#L62의](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)getUsersFromGraph 메서드에 있습니다.

메서드는 을 `getAuthResultBySilentFlow`호출하려고 시도합니다. 사용자가 더 많은 범위에 동의해야 하는 경우 `MsalInteractionRequiredException` 코드는 개체를 처리하여 사용자에게 이의를 제기합니다.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

파이썬 샘플에서 Microsoft Graph를 호출하는 코드는 [app.py#L53-L62에](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)있습니다.

코드는 토큰 캐시에서 토큰을 얻으려고 시도합니다. 그런 다음 권한 부여 헤더를 설정하면 웹 API를 호출합니다. 토큰을 얻을 수 없는 경우 사용자에게 다시 서명합니다.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API 호출](scenario-web-app-call-api-call-api.md)
