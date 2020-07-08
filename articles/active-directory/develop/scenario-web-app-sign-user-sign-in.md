---
title: 사용자를 로그인/로그 아웃 하는 웹 앱 작성-Microsoft identity platform | Microsoft
description: 사용자를 로그인/로그 아웃 하는 웹 앱을 빌드하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, tracking-python
ms.openlocfilehash: 41124e7237c2c16034fe8cce1fa89fa0132d09b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84558929"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>사용자 로그인 및 로그 아웃 하는 웹 앱

사용자가 로그인 하는 웹 앱에 대 한 코드에 로그인을 추가 하는 방법에 대해 알아봅니다. 그런 다음 로그 아웃 하는 방법을 알아봅니다.

## <a name="sign-in"></a>로그인

로그인은 다음 두 부분으로 구성 됩니다.

- HTML 페이지의 로그인 단추
- 컨트롤러의 코드 숨김으로 된 로그인 동작

### <a name="sign-in-button"></a>로그인 단추

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core에서 Microsoft identity platform 응용 프로그램의 경우 **로그인** 단추는 `Views\Shared\_LoginPartial.cshtml` (MVC 앱의 경우) 또는 `Pages\Shared\_LoginPartial.cshtm` (Razor 앱의 경우)에 표시 됩니다. 사용자가 인증 되지 않은 경우에만 표시 됩니다. 즉, 사용자가 아직 로그인 하지 않았거나 로그 아웃 한 경우 표시 됩니다. 이와 반대로 사용자가 이미 로그인 한 경우에는 **로그 아웃** 단추가 표시 됩니다. 계정 컨트롤러는 **MicrosoftIdentity** 이라는 영역에 있는 **Microsoft Identity.** ui&gt 패키지에 정의 되어 있습니다.

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC에서 로그 아웃 단추는에서 노출 됩니다 `Views\Shared\_LoginPartial.cshtml` . 인증 된 계정이 있는 경우에만 표시 됩니다. 즉, 사용자가 이전에 로그인 한 경우 표시 됩니다.

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

# <a name="java"></a>[Java](#tab/java)

Java 빠른 시작에서 로그인 단추는 [기본/리소스/템플릿/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) 파일에 있습니다.

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

# <a name="python"></a>[Python](#tab/python)

Python 빠른 시작에는 로그인 단추가 없습니다. 코드 숨김이 웹 앱의 루트에 도달 하는 경우 사용자에 게 로그인 하 라는 메시지를 자동으로 표시 합니다. [Py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)를 참조 하세요.

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`컨트롤러의 동작

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET에서 웹 앱의 **로그인** 단추를 선택 하면 `SignIn` 컨트롤러에서 작업이 트리거됩니다 `AccountController` . 이전 버전의 ASP.NET core 템플릿에서는 `Account` 컨트롤러가 웹 앱에 포함 되었습니다. 이제 컨트롤러가 이제는이는 더 이상 **Microsoft의 Microsoft. Identity.** uinuget 패키지의 일부 이기 때문입니다. 자세한 내용은 [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) 를 참조 하세요.

또한이 컨트롤러는 Azure AD B2C 응용 프로그램을 처리 합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 로그 아웃은 `SignOut()` 컨트롤러의 메서드에서 트리거됩니다 (예를 들어 [accountcontroller. Cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). 이 메서드는 ASP.NET 프레임 워크의 일부가 아닙니다 (ASP.NET Core에서 발생 하는 것과 반대). 리디렉션 URI를 제안 하 고 나면 Openid connect 로그인 챌린지를 보냅니다.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Java에서 로그 아웃은 Microsoft id 플랫폼 끝점을 직접 호출 하 고 값을 제공 하 여 처리 됩니다 `logout` `post_logout_redirect_uri` . 자세한 내용은 [AuthPageController # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)를 참조 하세요.

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

# <a name="python"></a>[Python](#tab/python)

다른 플랫폼과 달리 MSAL Python은 로그인 페이지에서 사용자 로그인을 허용 합니다. [Py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)를 참조 하세요.

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

메서드는 다음과 `_build_msal_app()` 같이 [Py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) 에 정의 됩니다.

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
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

사용자가 앱에 로그인 한 후 로그 아웃 하도록 설정 하는 것이 좋습니다.

## <a name="sign-out"></a>로그아웃

웹 앱에서 로그 아웃 하려면 웹 앱의 상태에서 로그인 한 계정에 대 한 정보를 제거 해야 합니다.
또한 웹 앱은 사용자를 Microsoft id 플랫폼 끝점으로 리디렉션하여 `logout` 로그 아웃 해야 합니다.

웹 앱이 사용자를 끝점으로 리디렉션하는 경우 `logout` 이 끝점은 브라우저에서 사용자의 세션을 지웁니다. 앱이 끝점으로 이동 하지 `logout` 않은 경우 사용자는 자격 증명을 다시 입력 하지 않고 앱에 다시 인증 합니다. 그 이유는 Microsoft id 플랫폼 끝점과의 유효한 단일 로그인 세션이 있기 때문입니다.

자세히 알아보려면 [Microsoft id 플랫폼 및 Openid connect Connect 프로토콜](v2-protocols-oidc.md) 설명서에서 [로그 아웃 요청 보내기](v2-protocols-oidc.md#send-a-sign-out-request) 섹션을 참조 하세요.

### <a name="application-registration"></a>애플리케이션 등록

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

응용 프로그램을 등록 하는 동안 사후 로그 아웃 URI를 등록 합니다. 이 자습서에서는 `https://localhost:44321/signout-oidc` **인증** 페이지의 **고급 설정** 섹션에 있는 **로그 아웃 URL** 필드에 등록 했습니다. 자세한 내용은 [webApp 앱 등록](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)을 참조 하세요.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

응용 프로그램을 등록 하는 동안 사후 로그 아웃 URI를 등록 합니다. 이 자습서에서는 `https://localhost:44308/Account/EndSession` **인증** 페이지의 **고급 설정** 섹션에 있는 **로그 아웃 URL** 필드에 등록 했습니다. 자세한 내용은 [webApp 앱 등록](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)을 참조 하세요.

# <a name="java"></a>[Java](#tab/java)

응용 프로그램을 등록 하는 동안 사후 로그 아웃 URI를 등록 합니다. 이 자습서에서는 `http://localhost:8080/msal4jsample/sign_out` **인증** 페이지의 **고급 설정** 섹션에 있는 **로그 아웃 URL** 필드에 등록 했습니다.

# <a name="python"></a>[Python](#tab/python)

응용 프로그램 등록 중에는 추가 로그 아웃 URL을 등록할 필요가 없습니다. 앱이 주 URL로 다시 호출 됩니다.

---

### <a name="sign-out-button"></a>로그 아웃 단추

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET에서 웹 앱의 **로그 아웃** 단추를 선택 하면 `SignOut` 컨트롤러에서 작업을 트리거합니다 `AccountController` (아래 참조).

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC에서 로그 아웃 단추는에서 노출 됩니다 `Views\Shared\_LoginPartial.cshtml` . 인증 된 계정이 있는 경우에만 표시 됩니다. 즉, 사용자가 이전에 로그인 한 경우 표시 됩니다.

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

# <a name="java"></a>[Java](#tab/java)

Java 빠른 시작에서 로그 아웃 단추는 기본/리소스/템플릿/auth_page.html 파일에 있습니다.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

Python 빠른 시작에서 로그 아웃 단추는 [templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) 파일에 있습니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut`컨트롤러의 동작

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이전 버전의 ASP.NET core 템플릿에서는 `Account` 컨트롤러가 웹 앱에 포함 되었습니다. 이제 컨트롤러가 이제는이는 더 이상 **Microsoft의 Microsoft. Identity.** uinuget 패키지의 일부 이기 때문입니다. 자세한 내용은 [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) 를 참조 하세요.

- Openid connect 리디렉션 URI를로 설정 하 여 `/Account/SignedOut` AZURE AD가 로그 아웃을 완료 하면 컨트롤러가 다시 호출 되도록 합니다.
- `Signout()`Openid connect 연결 미들웨어가 Microsoft id 플랫폼 끝점에 연결할 수 있도록 하는를 호출 합니다 `logout` . 그러면 끝점은 다음과 같습니다.

  - 브라우저에서 세션 쿠키를 지웁니다.
  - 로그 아웃 URL을 다시 호출 합니다. 기본적으로 로그 아웃 URL은 [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)로 로그 아웃 된 보기 페이지를 표시 합니다. 이 페이지는 또한 MIcrosoft. Identity. Web의 일부로 제공 됩니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 로그 아웃은 `SignOut()` 컨트롤러의 메서드에서 트리거됩니다 (예를 들어 [accountcontroller. Cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). 이 메서드는 ASP.NET Core에 발생 하는 것과 달리 ASP.NET 프레임 워크의 일부가 아닙니다. 메서드

- Openid connect 로그 아웃 챌린지를 보냅니다.
- 캐시를 지웁니다.
- 원하는 페이지로 리디렉션합니다.

```csharp
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

# <a name="java"></a>[Java](#tab/java)

Java에서 로그 아웃은 Microsoft id 플랫폼 끝점을 직접 호출 하 고 값을 제공 하 여 처리 됩니다 `logout` `post_logout_redirect_uri` . 자세한 내용은 [AuthPageController # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)를 참조 하세요.

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

# <a name="python"></a>[Python](#tab/python)

사용자를 로그 아웃 하는 코드는 [py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)에 있습니다.

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>끝점에 대 한 호출 가로채기 `logout`

사후 로그 아웃 URI를 사용 하면 응용 프로그램이 전역 로그 아웃에 참여할 수 있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Openid connect 연결 미들웨어를 사용 하면 앱에서 `logout` 라는 Openid connect Connect 이벤트를 제공 하 여 Microsoft id 플랫폼 끝점에 대 한 호출을 가로챌 수 있습니다 `OnRedirectToIdentityProviderForSignOut` . 이는 웹 앱이 web api를 호출 하는 경우에는 Microsoft에서 자동으로 처리 합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 미들웨어에 위임 하 여 로그 아웃을 실행 하 고 세션 쿠키를 지웁니다.

```csharp
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

# <a name="java"></a>[Java](#tab/java)

Java 빠른 시작에서 후 로그 아웃 리디렉션 URI는 index.html 페이지만 표시 합니다.

# <a name="python"></a>[Python](#tab/python)

Python 빠른 시작에서 사후 로그 리디렉션 URI는 index.html 페이지만 표시 합니다.

---

## <a name="protocol"></a>프로토콜

로그 아웃에 대해 자세히 알아보려면 [OPEN ID Connect](./v2-protocols-oidc.md)에서 사용할 수 있는 프로토콜 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-web-app-sign-user-production.md)
