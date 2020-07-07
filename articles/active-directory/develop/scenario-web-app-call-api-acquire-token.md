---
title: 웹 Api를 호출 하는 웹 앱에서 토큰 가져오기-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 웹 앱에 대 한 토큰을 획득 하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 40e788099a159e1f60c0af02deccd7e3bef82744
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181735"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>웹 Api를 호출 하는 웹 앱: 앱에 대 한 토큰을 가져옵니다.

클라이언트 응용 프로그램 개체를 빌드 했습니다. 이제이를 사용 하 여 web API를 호출 하는 토큰을 가져옵니다. ASP.NET 또는 ASP.NET Core에서 웹 API 호출은 컨트롤러에서 수행 됩니다.

- 토큰 캐시를 사용 하 여 web API에 대 한 토큰을 가져옵니다. 이 토큰을 가져오려면 MSAL `AcquireTokenSilent` 메서드 (또는 Microsoft. Identity. Web)를 호출 합니다.
- 보호 된 API를 호출 하 여 액세스 토큰을 매개 변수로 전달 합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

컨트롤러 메서드는 `[Authorize]` 웹 앱을 사용 하도록 사용자를 인증 하도록 하는 특성으로 보호 됩니다. Microsoft Graph를 호출 하는 코드는 다음과 같습니다.

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

`ITokenAcquisition`서비스는 종속성 주입을 사용 하 여 ASP.NET에 의해 삽입 됩니다.

다음은 Microsoft Graph 호출할 토큰을 가져오는의 동작에 대 한 간소화 된 코드입니다 `HomeController` .

```csharp
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

이 시나리오에 필요한 코드를 더 잘 이해 하려면 [aspnetcore-webapp](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 자습서의 2 단계 ([2-1-웹 앱 호출 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) 단계를 참조 하세요.

다음과 같은 기타 복잡 한 변형이 있습니다.

- 여러 Api 호출
- 증분 동의 및 조건부 액세스를 처리 합니다.

이러한 고급 단계는 [WebApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) 자습서의 3 장에서 설명 합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에 대 한 코드는 ASP.NET Core에 대해 표시 되는 코드와 비슷합니다.

- [권한 부여] 특성으로 보호 되는 컨트롤러 작업은 컨트롤러 구성원의 테 넌 트 ID 및 사용자 ID를 추출 합니다 `ClaimsPrincipal` . (ASP.NET는 `HttpContext.User` 를 사용 합니다.)
- 여기에서 MSAL.NET 개체를 빌드합니다 `IConfidentialClientApplication` .
- 마지막으로 `AcquireTokenSilent` 기밀 클라이언트 응용 프로그램의 메서드를 호출 합니다.

# <a name="java"></a>[Java](#tab/java)

Java 샘플에서 API를 호출 하는 코드는 [AuthPageController # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)의 Getusers fromgraph 메서드에 있습니다.

메서드는를 호출 하려고 `getAuthResultBySilentFlow` 합니다. 사용자가 더 많은 범위에 동의 해야 하는 경우 코드에서 개체를 처리 `MsalInteractionRequiredException` 하 여 사용자를 시도 합니다.

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

Python 샘플에서 Microsoft Graph를 호출 하는 코드는 [py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)에 있습니다.

이 코드는 토큰 캐시에서 토큰을 가져오려고 시도 합니다. 그런 다음 권한 부여 헤더를 설정한 후 웹 API를 호출 합니다. 토큰을 가져올 수 없는 경우 사용자에 게 다시 로그인 합니다.

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

> [!div class="nextstepaction"]
> [웹 API 호출](scenario-web-app-call-api-call-api.md)
