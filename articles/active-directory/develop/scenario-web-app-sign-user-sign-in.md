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
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086469"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>사용자가 로그인 하는 웹 앱-로그인

사용자가 로그인 하는 웹 앱에 대 한 코드에 로그인을 추가 하는 방법에 대해 알아봅니다.

## <a name="sign-in"></a>로그인

이전 문서 [응용 프로그램의 코드 구성](scenario-web-app-sign-user-app-configuration.md) 에서 검토 한 코드는 모두 로그인을 구현 하는 데 필요 합니다.
사용자가 앱에 로그인 한 후에는 로그 아웃 하도록 설정 하는 것이 좋습니다. ASP.NET core는 로그 아웃을 처리 합니다.

## <a name="what-sign-out-involves"></a>로그 아웃 관련 내용

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

응용 프로그램 등록 중에는 로그 아웃 URL을 등록할 필요가 없습니다. 이 샘플에서는 전역 로그 아웃을 구현 하지 않습니다.

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

Python 빠른 시작에서 로그 아웃 단추는 [templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20) 파일에 있습니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`컨트롤러의 동작

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET에서 웹 앱의 **로그 아웃** 단추를 누르면 `SignOut` `AccountController` 컨트롤러에서 작업이 트리거됩니다. 이전 버전의 ASP.NET core 템플릿에서 컨트롤러는 `Account` 웹 앱에 포함 되었지만 이제는 ASP.NET Core 프레임 워크 자체의 일부가 아닙니다.

의 코드 `AccountController` 는 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)의 ASP.NET core 리포지토리에서 사용할 수 있습니다. 계정 컨트롤:

- Openid connect 리디렉션 URI를로 설정 `/Account/SignedOut` 하 여 Azure AD에서 로그 아웃을 수행한 경우 컨트롤러가 다시 호출 되도록 합니다.
- 를 `Signout()`호출 하면 openidconnect 미들웨어가 다음을 수행 하는 Microsoft `logout` id 플랫폼 끝점에 연결할 수 있습니다.

  - 브라우저에서 세션 쿠키를 지웁니다.
  - 마지막으로는 로그 아웃 된 보기 페이지를 표시 하는 **로그 아웃 URL**을 다시 호출 합니다 [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) 은 ASP.NET Core의 일부로도 제공 됩니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 로그 아웃은 컨트롤러의 `SignOut()` 메서드 (예를 들어 accountcontroller)에서 트리거됩니다. 이 메서드는 ASP.NET 프레임 워크의 일부가 아닙니다 (ASP.NET Core에서 발생 하는 것과 반대). 을 사용 `async`하지 않으므로 다음과 같은 이유 때문입니다.

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

Java에서는 Microsoft id 플랫폼 로그 아웃 끝점을 직접 호출 하 고 post_logout_redirect_uri를 제공 하 여 로그 아웃을 처리 합니다.

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

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
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

## <a name="protocol"></a>프로토콜

로그 아웃에 대해 자세히 알아보려면 [OPEN ID Connect](./v2-protocols-oidc.md)에서 사용할 수 있는 프로토콜 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-web-app-sign-user-production.md)
