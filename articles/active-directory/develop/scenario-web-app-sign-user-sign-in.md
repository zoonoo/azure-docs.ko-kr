---
title: 사용자 에/아웃 서명하는 웹 앱 작성 - Microsoft ID 플랫폼 | Azure
description: 사용자를 인/아웃하는 웹 앱을 빌드하는 방법 알아보기
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
ms.openlocfilehash: 2ab5697ceff612e65174fdb7f9ef6137e2c8b9a5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537069"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>사용자에 로그인하는 웹 앱: 로그인 및 로그아웃

사용자에게 로그인하는 웹 앱의 코드에 로그인을 추가하는 방법을 알아봅니다. 그런 다음 로그아웃하도록 하는 방법을 알아봅니다.

## <a name="sign-in"></a>로그인

로그인은 두 부분으로 구성됩니다.

- HTML 페이지의 로그인 단추
- 컨트롤러의 코드 숨미기에서 로그인 작업

### <a name="sign-in-button"></a>로그인 버튼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 코어에서 로그인 버튼이 `Views\Shared\_LoginPartial.cshtml`에 노출됩니다. 인증된 계정이 없는 경우에만 표시됩니다. 즉, 사용자가 아직 로그인하지 않았거나 로그아웃한 경우 표시됩니다.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC에서 로그아웃 버튼이 `Views\Shared\_LoginPartial.cshtml`에 노출됩니다. 인증된 계정이 있는 경우에만 표시됩니다. 즉, 사용자가 이전에 로그인했을 때 표시됩니다.

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

Java 빠른 시작에서 로그인 버튼은 [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) 파일에 있습니다.

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

파이썬 퀵스타트에는 로그인 버튼이 없습니다. 코드 숨는 웹 앱의 루트에 도달할 때 로그인하라는 메시지가 자동으로 표시됩니다. [app.py#L14-L18을](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)참조하십시오.

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

ASP.NET 웹 앱에서 **로그인** 단추를 선택하면 `SignIn` `AccountController` 컨트롤러의 작업이 트리거됩니다. 이전 버전의 ASP.NET 핵심 템플릿에서는 `Account` 컨트롤러가 웹 앱에 포함되었습니다. 컨트롤러가 이제 ASP.NET 코어 프레임워크의 일부이기 때문에 더 이상 그렇지 않습니다.

에 대 `AccountController` 한 코드는 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)ASP.NET 코어 리포지토리에서 사용할 수 있습니다. 계정 제어는 Microsoft ID 플랫폼 끝점으로 리디렉션하여 사용자에게 문제를 해결합니다. 자세한 내용은 ASP.NET 코어의 일부로 제공되는 [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) 방법을 참조하십시오.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 컨트롤러의 `SignOut()` 메서드에서 로그아웃이 트리거됩니다(예: [AccountController.cs#L16-L23).](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23) 이 메서드는 ASP.NET 프레임워크의 일부가 ASP.NET 코어에서 발생하는 것과는 반대입니다. 리디렉션 URI를 제안한 후 OpenID 로그인 챌린지를 보냅니다.

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

Java에서는 Microsoft ID 플랫폼 `logout` 끝점을 직접 호출하고 값을 제공하여 `post_logout_redirect_uri` 로그아웃을 처리합니다. 자세한 내용은 [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)을 참조하십시오.

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

다른 플랫폼과 달리 MSAL Python은 사용자가 로그인 페이지에서 로그인하도록 하는 것을 처리합니다. [app.py#L20-L28을](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)참조하십시오.

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

메서드는 `_build_msal_app()` [app.py#L81-L88에](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) 다음과 같이 정의되어 있습니다.

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

사용자가 앱에 로그인한 후 로그아웃하도록 설정해야 합니다.

## <a name="sign-out"></a>로그아웃

웹 앱에서 로그아웃하려면 웹 앱의 상태에서 로그인한 계정에 대한 정보를 제거하는 것 이상의 작업이 포함됩니다.
웹 앱은 로그아웃하려면 사용자를 Microsoft `logout` ID 플랫폼 끝점으로 리디렉션해야 합니다.

웹 앱이 사용자를 `logout` 끝점으로 리디렉션하면 이 끝점에서 브라우저에서 사용자의 세션이 지워지게 됩니다. 앱이 `logout` 끝점으로 이동하지 않은 경우 사용자는 자격 증명을 다시 입력하지 않고 앱에 다시 인증됩니다. 그 이유는 Microsoft ID 플랫폼 끝점을 가진 유효한 단일 로그인 세션이 있기 때문입니다.

자세한 내용은 Microsoft ID 플랫폼의 [로그아웃 요청 보내기](v2-protocols-oidc.md#send-a-sign-out-request) [섹션및 OpenID Connect 프로토콜 설명서를](v2-protocols-oidc.md) 참조하십시오.

### <a name="application-registration"></a>애플리케이션 등록

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

응용 프로그램 등록 중에 로그아웃 후 URI를 등록합니다. 자습서에서는 **인증** 페이지의 `https://localhost:44321/signout-oidc` 고급 **설정** 섹션의 **로그아웃 URL** 필드에 등록했습니다. 자세한 내용은 [웹앱 앱 등록을](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)참조하십시오.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

응용 프로그램 등록 중에 로그아웃 후 URI를 등록합니다. 자습서에서는 **인증** 페이지의 `https://localhost:44308/Account/EndSession` 고급 **설정** 섹션의 **로그아웃 URL** 필드에 등록했습니다. 자세한 내용은 [웹앱 앱 등록을](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)참조하십시오.

# <a name="java"></a>[Java](#tab/java)

응용 프로그램 등록 중에 로그아웃 후 URI를 등록합니다. 자습서에서는 **인증** 페이지의 `http://localhost:8080/msal4jsample/sign_out` 고급 **설정** 섹션의 **로그아웃 URL** 필드에 등록했습니다.

# <a name="python"></a>[Python](#tab/python)

응용 프로그램 등록 중에 추가 로그아웃 URL을 등록할 필요가 없습니다. 앱은 기본 URL에서 다시 호출됩니다.

---

### <a name="sign-out-button"></a>로그아웃 버튼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 코어에서 로그아웃 버튼이 `Views\Shared\_LoginPartial.cshtml`에 노출됩니다. 인증된 계정이 있는 경우에만 표시됩니다. 즉, 사용자가 이전에 로그인했을 때 표시됩니다.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC에서 로그아웃 버튼이 `Views\Shared\_LoginPartial.cshtml`에 노출됩니다. 인증된 계정이 있는 경우에만 표시됩니다. 즉, 사용자가 이전에 로그인했을 때 표시됩니다.

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

Java 빠른 시작에서 로그아웃 버튼은 main/resources/templates/auth_page.html 파일에 있습니다.

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

파이썬 퀵스타트에서 로그아웃 버튼은 [템플릿/index.html#L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) 파일에 있습니다.

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

ASP.NET 웹 앱에서 **로그아웃** 단추를 선택하면 `SignOut` 컨트롤러의 `AccountController` 작업이 트리거됩니다. 이전 버전의 ASP.NET Core 템플릿에서는 `Account` 컨트롤러가 웹 앱에 포함되었습니다. 컨트롤러가 이제 ASP.NET 코어 프레임워크의 일부이기 때문에 더 이상 그렇지 않습니다.

에 대한 `AccountController` 코드는 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)ASP.NET 코어 리포지토리에서 사용할 수 있습니다. 계정 제어:

- Azure AD가 로그아웃을 완료할 `/Account/SignedOut` 때 컨트롤러가 다시 호출되도록 OpenID 리디렉션 URI를 설정합니다.
- 호출은 `Signout()`OpenID Connect 미들웨어가 Microsoft ID `logout` 플랫폼 엔드포인트에 연결할 수 있도록 합니다. 그런 다음 끝점:

  - 브라우저에서 세션 쿠키를 지웁습니다.
  - 로그아웃 URL을 다시 호출합니다. 기본적으로 로그아웃 URL에는 로그아웃 보기 [페이지가 표시됩니다.](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) 이 페이지는 ASP.NET 코어의 일부로도 제공됩니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 컨트롤러의 `SignOut()` 메서드에서 로그아웃이 트리거됩니다(예: [AccountController.cs#L25-L31).](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31) 이 메서드는 ASP.NET 프레임 워크의 일부가 아닙니다., ASP.NET 코어에서 일어나는 반대. 그것은 :

- OpenID 로그아웃 챌린지를 보냅니다.
- 캐시를 지웁니까?
- 원하는 페이지로 리디렉션됩니다.

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

Java에서는 Microsoft ID 플랫폼 `logout` 끝점을 직접 호출하고 값을 제공하여 `post_logout_redirect_uri` 로그아웃을 처리합니다. 자세한 내용은 [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)을 참조하십시오.

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

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>`logout` 끝점에 대한 호출 가로채기

로그아웃 후 URI를 사용하면 응용 프로그램이 전역 로그아웃에 참여할 수 있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 코어 OpenID 연결 미들웨어를 사용하면 앱에서 OpenID `logout` Connect 이벤트라는 이름을 `OnRedirectToIdentityProviderForSignOut`제공하여 Microsoft ID 플랫폼 끝점에 대한 호출을 가로챌 수 있습니다. 토큰 캐시를 지우려면 이 이벤트를 구독하는 방법에 대한 예는 [Microsoft.Identity.Web/WebAppServiceCollectionExtension.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)을 참조하십시오.

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 미들웨어에 위임하여 로그아웃을 실행하여 세션 쿠키를 지운다.

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

Java 빠른 시작에서 로그아웃 후 리디렉션 URI는 index.html 페이지만 표시합니다.

# <a name="python"></a>[Python](#tab/python)

Python 빠른 시작에서 포스트 로그아웃 리디렉션 URI는 index.html 페이지를 표시합니다.

---

## <a name="protocol"></a>프로토콜

로그아웃에 대해 자세히 알아보려면 [Open ID Connect](./v2-protocols-oidc.md)에서 사용할 수 있는 프로토콜 설명서를 읽어보십시오.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션 환경으로 이동](scenario-web-app-sign-user-production.md)
