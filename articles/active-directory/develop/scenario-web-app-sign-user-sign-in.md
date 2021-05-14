---
title: 사용자를 로그인/로그아웃하는 웹앱 쓰기 | Azure
titleSuffix: Microsoft identity platform
description: 사용자를 로그인/로그아웃하는 웹앱을 빌드하는 방법 알아보기
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 10ddee404de21c5bc04672fdb6dd32c30f481ba3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578246"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>사용자를 로그인하는 웹앱: 로그인 및 로그아웃

사용자를 로그인하는 웹앱의 코드에 로그인을 추가하는 방법을 알아봅니다. 그런 다음, 로그아웃하는 방법을 알아봅니다.

## <a name="sign-in"></a>로그인

로그인은 다음과 같은 두 부분으로 구성됩니다.

- HTML 페이지의 로그인 단추
- 컨트롤러에서 코드 숨김의 로그인 동작

### <a name="sign-in-button"></a>로그인 단추

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core에서 Microsoft ID 플랫폼 애플리케이션의 경우 **로그인** 단추가 `Views\Shared\_LoginPartial.cshtml`(MVC 앱) 또는 `Pages\Shared\_LoginPartial.cshtm`(Razor 앱)에 표시됩니다. 사용자가 인증되지 않은 경우에만 표시됩니다. 즉, 사용자가 아직 로그인하지 않았거나 로그아웃한 경우 표시됩니다. 이와 반대로 사용자가 이미 로그인한 경우에는 **로그아웃** 단추가 표시됩니다. 계정 컨트롤러는 **MicrosoftIdentity** 라는 영역에 있는 **Microsoft.Identity.Web.UI** NuGet 패키지에 정의되어 있습니다.

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

ASP.NET MVC에서 로그아웃 단추는 `Views\Shared\_LoginPartial.cshtml`에 표시됩니다. 인증된 계정이 있는 경우에만 표시됩니다. 즉, 사용자가 이전에 로그인한 경우 표시됩니다.

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

Java 빠른 시작에서 로그인 단추는 [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) 파일에 있습니다.

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

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Node.js 빠른 시작에는 로그인 단추가 없습니다. 웹앱의 루트에 도달하면 코드 숨김이 사용자에게 로그인하라는 메시지를 자동으로 표시합니다.

```javascript
app.get('/', (req, res) => {
    // authentication logic
});
```

# <a name="python"></a>[Python](#tab/python)

Python 빠른 시작에는 로그인 단추가 없습니다. 웹앱의 루트에 도달하면 코드 숨김이 사용자에게 로그인하라는 메시지를 자동으로 표시합니다. [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)을 참조하세요.

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>컨트롤러의 `SignIn` 동작

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET에서 웹앱의 **로그인** 단추를 선택하면 `AccountController` 컨트롤러에서 `SignIn` 동작이 트리거됩니다. 이전 버전의 ASP.NET 코어 템플릿에서는 `Account` 컨트롤러가 웹앱에 포함되어 있었습니다. 이제는 컨트롤러가 **Microsoft.Identity.Web.UI** NuGet 패키지의 일부이기 때문에 더 이상 포함되지 않습니다. 자세한 내용은 [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)를 참조하세요.

또한 이 컨트롤러는 Azure AD B2C 애플리케이션을 처리합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 컨트롤러의 `SignOut()` 메서드(예: [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23))로부터 로그아웃이 트리거됩니다. 이 메서드는 ASP.NET Core에서 발생하는 것과 달리 ASP.NET 프레임워크의 일부가 아닙니다. 리디렉션 URI를 제안한 후 OpenID 로그인 챌린지를 보냅니다.

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

Java에서는 Microsoft ID 플랫폼 `logout` 엔드포인트를 직접 호출하고 `post_logout_redirect_uri` 값을 제공하여 로그아웃을 처리합니다. 자세한 내용은 [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)을 참조하세요.

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

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

다른 플랫폼과 달리, 여기서는 MSAL 노드가 로그인 페이지에서 사용자가 로그인할 수 있도록 합니다.

```javascript

// 1st leg of auth code flow: acquire a code
app.get('/', (req, res) => {
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // get url to sign user in and consent to scopes needed for application
    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

// 2nd leg of auth code flow: exchange code for token
app.get('/redirect', (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

# <a name="python"></a>[Python](#tab/python)

다른 플랫폼과 달리, MSAL Python이 로그인 페이지에서 사용자가 로그인할 수 있도록 합니다. [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)을 참조하세요.

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

`_build_msal_app()` 메서드는 다음과 같이 [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88)에 정의되어 있습니다.

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

사용자가 앱에 로그인한 후 로그아웃하도록 설정하려고 합니다.

## <a name="sign-out"></a>로그아웃

웹앱에서 로그아웃하는 것은 웹앱의 상태에서 로그인한 계정에 대한 정보를 제거하는 것 이상을 포함합니다.
또한 웹앱은 로그아웃할 사용자를 Microsoft ID 플랫폼 `logout` 엔드포인트로 리디렉션해야 합니다.

웹앱이 사용자를 `logout` 엔드포인트에 리디렉션하면 이 엔드포인트가 브라우저에서 사용자의 세션을 지웁니다. 앱이 `logout` 엔드포인트로 이동하지 않은 경우 사용자는 자격 증명을 다시 입력하지 않고 앱에 다시 인증합니다. 그 이유는 Microsoft ID 플랫폼을 사용하는 유효한 단일 로그인 세션이 있기 때문입니다.

자세히 알아보려면 [Microsoft ID 플랫폼 및 OpenID Connect 프로토콜](v2-protocols-oidc.md) 설명서에서 [로그아웃 요청 보내기](v2-protocols-oidc.md#send-a-sign-out-request) 섹션을 참조하세요.

### <a name="application-registration"></a>애플리케이션 등록

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

애플리케이션을 등록하는 동안 전면 채널 로그아웃 URL을 등록합니다. 이 자습서에서는 **인증** 페이지의 **전면 채널 로그아웃 URL** 필드에 `https://localhost:44321/signout-oidc`를 등록했습니다. 자세한 내용은 [WebApp 앱 등록](scenario-web-app-sign-user-app-registration.md#register-an-app-by-using-the-azure-portal)을 참조하세요.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

애플리케이션을 등록하는 동안 추가 전면 채널 로그아웃 URL을 등록할 필요가 없습니다. 앱이 기본 URL로 다시 호출됩니다. 

# <a name="java"></a>[Java](#tab/java)

애플리케이션 등록에는 전면 채널 로그아웃 URL이 필요하지 않습니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

애플리케이션 등록에는 전면 채널 로그아웃 URL이 필요하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

애플리케이션을 등록하는 동안 추가 전면 채널 로그아웃 URL을 등록할 필요가 없습니다. 앱이 기본 URL로 다시 호출됩니다.

---

### <a name="sign-out-button"></a>로그아웃 단추

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET에서 웹앱의 **로그아웃** 단추를 선택하면 `AccountController` 컨트롤러에서 `SignOut` 동작이 트리거됩니다(아래 참조).

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

ASP.NET MVC에서 로그아웃 단추는 `Views\Shared\_LoginPartial.cshtml`에 표시됩니다. 인증된 계정이 있는 경우에만 표시됩니다. 즉, 사용자가 이전에 로그인한 경우 표시됩니다.

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

Java 빠른 시작에서 로그아웃 단추는 main/resources/templates/auth_page.html 파일에 있습니다.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 샘플 애플리케이션에서는 로그아웃을 구현하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python 빠른 시작에서 로그아웃 단추는 [templates/index.html#L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) 파일에 있습니다.

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

### <a name="signout-action-of-the-controller"></a>컨트롤러의 `SignOut` 동작

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이전 버전의 ASP.NET 코어 템플릿에서는 `Account` 컨트롤러가 웹앱에 포함되어 있었습니다. 이제는 컨트롤러가 **Microsoft.Identity.Web.UI** NuGet 패키지의 일부이기 때문에 더 이상 포함되지 않습니다. 자세한 내용은 [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)를 참조하세요.

- AZURE AD가 로그아웃을 완료하면 컨트롤러가 다시 호출되도록 OpenID 리디렉션 URI를 `/Account/SignedOut`으로 설정합니다.
- `Signout()`을 호출합니다. 이는 OpenID Connect 미들웨어가 Microsoft ID 플랫폼 `logout` 엔드포인트에 연결할 수 있도록 합니다. 그런 다음 엔드포인트는 다음을 수행합니다.

  - 브라우저에서 세션 쿠키를 지웁니다.
  - 사후 로그아웃 리디렉션 URI를 다시 호출합니다. 기본적으로 사후 로그아웃 리디렉션 URI는 로그아웃된 보기 페이지인 [SignedOut.cshtml.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs)를 표시합니다. 이 페이지는 Microsoft.Identity.Web의 일부로도 제공됩니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 컨트롤러의 `SignOut()` 메서드(예: [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31))로부터 로그아웃이 트리거됩니다. 이 메서드는 ASP.NET Core에서 발생하는 것과 달리 ASP.NET 프레임워크의 일부가 아닙니다. 이는

- OpenID 로그아웃 챌린지를 보냅니다.
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

Java에서는 Microsoft ID 플랫폼 `logout` 엔드포인트를 직접 호출하고 `post_logout_redirect_uri` 값을 제공하여 로그아웃을 처리합니다. 자세한 내용은 [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)을 참조하세요.

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

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 샘플 애플리케이션에서는 로그아웃을 구현하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

사용자를 로그아웃하는 코드는 [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)에 있습니다.

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>`logout` 엔드포인트에 대한 호출 가로채기

사후 로그아웃 URI를 사용하면 애플리케이션이 전체 로그아웃에 참여할 수 있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect 미들웨어를 사용하면 앱에서 `OnRedirectToIdentityProviderForSignOut`이라는 OpenID Connect 이벤트를 제공하여 Microsoft ID 플랫폼 `logout` 엔드포인트에 대한 호출을 가로챌 수 있습니다. 이는 Microsoft.Identity.Web에서 자동으로 처리됩니다(웹앱이 Web API를 호출하는 경우 계정을 지움).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서는 미들웨어에 로그아웃을 실행하도록 위임하여 세션 쿠키를 지웁니다.

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

Java 빠른 시작에서는 사후 로그아웃 리디렉션 URI가 index.html 페이지만 표시합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 샘플 애플리케이션에서는 로그아웃을 구현하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python 빠른 시작에서는 사후 로그아웃 리디렉션 URI가 index.html 페이지만 표시합니다.

---

## <a name="protocol"></a>프로토콜

로그아웃에 대해 자세히 알아보려면 [Open ID Connect](./v2-protocols-oidc.md)에서 사용할 수 있는 프로토콜 설명서를 읽습니다.

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서로 이동하여 [프로덕션으로 이동](scenario-web-app-sign-user-production.md)합니다.