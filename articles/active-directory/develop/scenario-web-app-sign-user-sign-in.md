---
title: 사용자를 로그인 하는 웹 앱 (로그인)-Microsoft identity platform
description: 사용자를 로그인 하는 웹 앱을 빌드하는 방법에 대해 알아봅니다 (로그인).
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5c45005d6a54765458b463acb12c21a1f3b6d0c
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336773"
---
# <a name="web-app-that-signs-in-users---sign-in-and-sign-out"></a>사용자 로그인 및 로그 아웃 하는 웹 앱

로그인 사용자를 로그인 하는 웹 앱에 대 한 코드에 로그인을 추가 하는 방법에 대해 알아봅니다.

## <a name="sign-in"></a>로그인

로그인은 두 부분으로 구성 됩니다.

- HTML 페이지의 로그인 단추
- 컨트롤러에 있는 코드의 로그인 작업

### <a name="sign-in-button"></a>로그인 단추

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core에서 로그인 단추는 인증 된 계정이 없는 경우에 `Views\Shared\_LoginPartial.cshtml` 만 표시 됩니다. 즉, 사용자가 아직 로그인 하지 않았거나 로그 아웃 한 경우에만 표시 됩니다.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC에서 로그 아웃 단추는에 `Views\Shared\_LoginPartial.cshtml` 노출 되며 인증 된 계정이 있는 경우에만 표시 됩니다 (사용자가 이전에 로그인 한 경우).

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java 빠른 시작에서 로그 아웃 단추는 [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) 파일에 있습니다.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 빠른 시작에는 로그인 단추가 없습니다. 사용자는 웹 앱의 루트에 도달할 때 코드 숨김으로 로그인 하 라는 메시지를 자동으로 표시 합니다. [Py # L14-L18를](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18) 참조 하세요.

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="login-action-of-the-controller"></a>`Login`컨트롤러의 동작

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET에서 웹 앱의 **로그인** 단추를 누르면 `SignIn` `AccountController` 컨트롤러에서 작업이 트리거됩니다. 이전 버전의 ASP.NET core 템플릿에서 컨트롤러는 `Account` 웹 앱에 포함 되었지만 이제는 ASP.NET Core 프레임 워크 자체의 일부가 아닙니다.

의 코드 `AccountController` 는 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)의 ASP.NET core 리포지토리에서 사용할 수 있습니다. 계정 컨트롤은 Microsoft id 플랫폼 끝점으로 리디렉션하여 사용자에 게 문제를 해결 합니다. 자세한 내용은 ASP.NET Core의 일부로 제공 된 [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) 메서드를 참조 하세요.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 로그 아웃은 컨트롤러의 `SignOut()` 메서드에서 트리거됩니다 (예를 들어 [accountcontroller. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). 이 메서드는 ASP.NET 프레임 워크의 일부가 아닙니다 (ASP.NET Core에서 발생 하는 것과 반대). 메서드

- 리디렉션 URI를 제안 하 고 나면 Openid connect 로그인 챌린지를 보냅니다.

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java에서는 Microsoft id 플랫폼 로그 아웃 끝점을 직접 호출 하 고 post_logout_redirect_uri를 제공 하 여 로그 아웃을 처리 합니다. 자세한 내용은 [AuthPageController # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48) 를 참조 하세요.

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

다른 플랫폼, MSAL과는 다릅니다. Python은 로그인 페이지에서 사용자 로그인을 허용 합니다. [Py # L20-L28를](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28) 참조 하세요.

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use empty list [] to just sign in,
                           # here we choose to also collect end user consent upfront
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

다음과 같이 [py # L81](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) 에 정의 된 _build_msal_app () 메서드를 사용 합니다.

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all account(s) belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

사용자가 앱에 로그인 한 후에는 로그 아웃 하도록 설정 하는 것이 좋습니다.

## <a name="sign-out"></a>로그아웃

웹 앱에서 로그 아웃 하는 것은 웹 앱의 상태에서 로그인 한 계정에 대 한 정보를 제거 하는 것입니다.
또한 웹 앱은 사용자를 Microsoft id 플랫폼 `logout` 끝점으로 리디렉션하여 로그 아웃 해야 합니다. 웹 앱이 사용자를 `logout` 끝점으로 리디렉션하는 경우이 끝점은 브라우저에서 사용자의 세션을 지웁니다. 앱이 `logout` 끝점으로 이동 하지 않은 경우 사용자는 자격 증명을 다시 입력 하지 않고 앱에 다시 인증 합니다. Microsoft id 플랫폼 끝점과의 올바른 단일 로그인 세션이 있기 때문입니다.

자세히 알아보려면 [Microsoft id 플랫폼 및 Openid connect Connect 프로토콜](v2-protocols-oidc.md) 개념 설명서에서 [로그 아웃 요청 보내기](v2-protocols-oidc.md#send-a-sign-out-request) 섹션을 참조 하세요.

### <a name="application-registration"></a>애플리케이션 등록

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

응용 프로그램을 등록 하는 동안 **사후 로그 아웃 URI**를 등록 합니다. 이 자습서에서는 **인증** 페이지의 `https://localhost:44321/signout-oidc` **고급 설정** 섹션에 있는 **로그 아웃 URL** 필드에 등록 했습니다. 자세한 내용은 [webApp 앱 등록](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp) 을 참조 하세요.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

응용 프로그램을 등록 하는 동안 **사후 로그 아웃 URI**를 등록 합니다. 이 자습서에서는 **인증** 페이지의 `https://localhost:44308/Account/EndSession` **고급 설정** 섹션에 있는 **로그 아웃 URL** 필드에 등록 했습니다. 자세한 내용은 [webApp 앱 등록](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet) 을 참조 하세요.

# <a name="javatabjava"></a>[Java](#tab/java)

응용 프로그램을 등록 하는 동안 **사후 로그 아웃 URI**를 등록 합니다. 이 자습서에서는 **인증** 페이지의 `http://localhost:8080/msal4jsample/` **고급 설정** 섹션에 있는 **로그 아웃 URL** 필드에 등록 했습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

응용 프로그램 등록 중에는 추가 로그 아웃 URL을 등록할 필요가 없습니다. 앱이 주 URL로 다시 호출 됩니다.

---

### <a name="sign-out-button"></a>로그 아웃 단추

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core에서 로그 아웃 단추는에 `Views\Shared\_LoginPartial.cshtml` 노출 되며 인증 된 계정이 있는 경우에만 표시 됩니다 (사용자가 이전에 로그인 한 경우).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC에서 로그 아웃 단추는에 `Views\Shared\_LoginPartial.cshtml` 노출 되며 인증 된 계정이 있는 경우에만 표시 됩니다 (사용자가 이전에 로그인 한 경우).

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java 빠른 시작에서 로그 아웃 단추는 main/resources/templates/auth_page.html 파일에 있습니다.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 빠른 시작에서 로그 아웃 단추는 [템플릿/인덱스. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) 파일에 있습니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout`컨트롤러의 동작

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET에서 웹 앱의 **로그 아웃** 단추를 누르면 `SignOut` `AccountController` 컨트롤러에서 작업이 트리거됩니다. 이전 버전의 ASP.NET core 템플릿에서 컨트롤러는 `Account` 웹 앱에 포함 되었지만 이제는 ASP.NET Core 프레임 워크 자체의 일부가 아닙니다.

의 코드 `AccountController` 는 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)의 ASP.NET core 리포지토리에서 사용할 수 있습니다. 계정 컨트롤:

- Openid connect 리디렉션 URI를로 설정 `/Account/SignedOut` 하 여 Azure AD에서 로그 아웃을 완료 하면 컨트롤러가 다시 호출 되도록 합니다.
- 를 `Signout()`호출 하면 openidconnect 미들웨어가 다음을 수행 하는 Microsoft `logout` id 플랫폼 끝점에 연결할 수 있습니다.

  - 브라우저에서 세션 쿠키를 지웁니다.
  - 마지막으로는 로그 아웃 된 보기 페이지를 표시 하는 **로그 아웃 URL**을 다시 호출 합니다 [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) 은 ASP.NET Core의 일부로도 제공 됩니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 로그 아웃은 컨트롤러의 `SignOut()` 메서드에서 트리거됩니다 (예를 들어 [accountcontroller. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). 이 메서드는 ASP.NET 프레임 워크의 일부가 아닙니다 (ASP.NET Core에서 발생 하는 것과 반대). 메서드

- Openid connect 로그 아웃 챌린지를 보냅니다.
- 캐시를 지웁니다.
- 원하는 페이지로 리디렉션

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java에서는 Microsoft id 플랫폼 로그 아웃 끝점을 직접 호출 하 고 post_logout_redirect_uri를 제공 하 여 로그 아웃을 처리 합니다. 자세한 내용은 [AuthPageController # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60) 를 참조 하세요.

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

사용자를 로그 아웃 하는 코드는 [py # L46-L52에 있습니다.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out user and its token cache from session
    return redirect(  # Also need to logout from Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>`logout` 끝점에 대 한 호출 가로채기

사후 로그 아웃 URI를 사용 하면 응용 프로그램이 전역 로그 아웃에 참여할 수 있습니다.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core openidconnect 미들웨어를 사용 하면 앱에서 라는 `logout` `OnRedirectToIdentityProviderForSignOut`openidconnect 이벤트를 제공 하 여 Microsoft id 플랫폼 끝점에 대 한 호출을 가로챌 수 있습니다. 이 이벤트를 구독 하는 방법에 대 한 예제는 [WebAppServiceCollectionExtensions/L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) (토큰 캐시를 지우는 방법)를 참조 하세요.

```CSharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 미들웨어에 위임 하 여 로그 아웃을 실행 하 고 세션 쿠키를 지웁니다.

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java 빠른 시작에서 로그 아웃 리디렉션 URI는 단순히 인덱스 .html 페이지를 표시 합니다. 

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 빠른 시작에서 로그 아웃 리디렉션 URI는 단순히 인덱스 .html 페이지를 표시 합니다.

---

## <a name="protocol"></a>Protocol

로그 아웃에 대해 자세히 알아보려면 [OPEN ID Connect](./v2-protocols-oidc.md)에서 사용할 수 있는 프로토콜 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-web-app-sign-user-production.md)
