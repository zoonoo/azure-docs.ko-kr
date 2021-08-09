---
title: 웹 API를 호출하는 웹앱에서 토큰을 가져오기 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 웹앱의 토큰을 가져오는 방법을 알아보기
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfd479382cb69e7355b033312e165699223fdbf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756302"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>웹 API를 호출하는 웹앱: 앱에 대한 토큰을 가져오기

클라이언트 애플리케이션 개체를 빌드했습니다. 이제 이 개체를 사용하여 웹 API를 호출하는 토큰을 가져옵니다. ASP.NET 또는 ASP.NET Core에서 웹 API 호출은 컨트롤러에서 수행됩니다.

- 토큰 캐시를 사용하여 웹 API에 대한 토큰을 가져옵니다. 이 토큰을 가져오려면 MSAL `AcquireTokenSilent` 메서드(또는 Microsoft.Identity.Web 메서드)를 호출합니다.
- 보호된 API를 호출하여 액세스 토큰을 매개 변수로 전달합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* 은 Microsoft Graph 또는 다운스트림 웹 API를 호출하기 위한 편리한 서비스를 제공하는 확장 메서드를 추가합니다. 해당 메서드는 [웹 API를 호출하는 웹앱: API 호출](scenario-web-app-call-api-call-api.md)에 자세히 설명되어 있습니다. 이러한 도우미 메서드를 사용하면 토큰을 수동으로 가져올 필요가 없습니다.

그러나 토큰을 수동으로 가져오려는 경우 다음 코드는 home 컨트롤러에서 *Microsoft.Identity.Web* 을 사용하여 이 작업을 수행하는 예를 보여 줍니다. 이 코드는 Microsoft Graph SDK 대신 REST API를 사용하여 Microsoft Graph를 호출합니다. 다운스트림 API를 호출하는 토큰을 가져오려면 컨트롤러의 생성자(Blazor를 사용하는 경우에는 페이지 생성자)에서 종속성 주입을 사용하여 `ITokenAcquisition` 서비스를 삽입하고 컨트롤러 작업에서 이를 사용하여 디먼 시나리오에서 사용자(`GetAccessTokenForUserAsync`) 또는 애플리케이션 자체(`GetAccessTokenForAppAsync`)에 대한 토큰을 가져옵니다.

컨트롤러 메서드는 인증된 사용자만 웹앱을 사용할 수 있도록 하는 `[Authorize]` 특성에 의해 보호됩니다.

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

`ITokenAcquisition` 서비스는 종속성 주입을 사용하여 ASP.NET을 통해 삽입됩니다.

다음은 Microsoft Graph를 호출할 토큰을 가져오는 `HomeController` 동작의 간소화된 코드입니다.

```csharp
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

이 시나리오에 필요한 코드를 더 잘 이해하려면 [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 자습서의 2단계([2-1-웹앱에서 Microsoft Graph를 호출)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)를 참조하세요.

컨트롤러 작업 위에 있는 `AuthorizeForScopes` 특성(Razor 템플릿을 사용하는 경우에는 Razor 템플릿)은 Microsoft.Identity.Web에서 제공합니다. 필요한 경우 사용자에게 증분 방식으로 동의를 요청합니다.

다음과 같은 복잡한 변형도 있습니다.

- 여러 API 호출
- 증분 동의 및 조건부 액세스 처리

이러한 고급 단계는 [3-WebApp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) 자습서의 3장에서 다룹니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 코드는 ASP.NET Core에서 살펴본 코드와 비슷합니다.

- [Authorize] 특성으로 보호되는 컨트롤러 작업은 컨트롤러 `ClaimsPrincipal` 멤버의 테넌트 ID와 사용자 ID를 추출합니다. (ASP.NET은 `HttpContext.User`를 사용합니다.)
- 컨트롤러 작업은 이 위치에서 MSAL.NET `IConfidentialClientApplication` 개체를 빌드합니다.
- 마지막으로 컨트롤러 작업은 기밀 클라이언트 애플리케이션의 `AcquireTokenSilent` 메서드를 호출합니다.
- 상호 작용이 필요한 경우 웹앱은 사용자에게 챌린지(다시 로그인)를 발행하고 더 많은 클레임을 요청해야 합니다.

다음 코드 조각은 [ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) ASP.NET MVC 코드 샘플의 [HomeController.cs#L157-L192](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Controllers/HomeController.cs#L157-L192)에서 추출됩니다.

```C#
public async Task<ActionResult> ReadMail()
{
    IConfidentialClientApplication app = MsalAppBuilder.BuildConfidentialClientApplication();
    AuthenticationResult result = null;
    var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());
    string[] scopes = { "Mail.Read" };

    try
    {
        // try to get token silently
        result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync().ConfigureAwait(false);
    }
    catch (MsalUiRequiredException)
    {
        ViewBag.Relogin = "true";
        return View();
    }

    // More code here
    return View();
}
```

자세한 내용은 코드 샘플에서 [BuildConfidentialClientApplication()](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)과 [GetMsalAccountId](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Utils/ClaimPrincipalExtension.cs#L38)의 코드를 참조하세요.


# <a name="java"></a>[Java](#tab/java)

Java 샘플에서 API를 호출하는 코드는 [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)의 getUsersFromGraph 메서드에 있습니다.

메서드는 `getAuthResultBySilentFlow`를 호출하려고 시도합니다. 사용자가 더 많은 범위에 동의해야 하는 경우 코드는 `MsalInteractionRequiredException` 개체를 처리하여 사용자에게 챌린지를 발행합니다.

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

Python 샘플에서 Microsoft Graph를 호출하는 코드는 [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)에 있습니다.

이 코드는 토큰 캐시에서 토큰을 얻으려고 시도합니다. 그러고 나서 인증 헤더를 설정한 다음 웹 API를 호출합니다. 토큰을 얻을 수 없는 경우 사용자를 다시 로그인시킵니다.

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

---

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서로 이동하여 [웹 API를 호출](scenario-web-app-call-api-call-api.md)합니다.
