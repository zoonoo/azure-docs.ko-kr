---
title: Microsoft id 플랫폼 (로그인)-사용자가 로그인 하는 웹 앱
description: 로그인 사용자 (로그인) 웹 앱을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074622"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>웹 앱 사용자를 로그인 하는-로그인

로그인 사용자가 웹 앱에 대 한 코드를 로그인에 추가 하는 방법에 알아봅니다.

## <a name="sign-in"></a>로그인

이전 문서에서 했습니다 코드 [앱의 코드 구성을](scenario-web-app-sign-user-app-configuration.md) 구현 해야 할 로그 아웃 됩니다. 사용자가 로그인 한 앱을 한 후 로그 아웃을 사용 하도록 설정 하려고 하는 것입니다. ASP.NET core를 로그 아웃을 처리합니다.

## <a name="what-sign-out-involves"></a>에서는 새로운 로그 아웃

웹 앱에서 로그 아웃 하는 것은 두 개 웹 앱의 상태에서 로그인 계정에 대 한 정보를 제거 하는 방법에 대 한 합니다.
웹 앱의 사용자 이름을 Microsoft identity 플랫폼 v2.0 리디렉션해야 `logout` 로그 아웃 끝점입니다. 웹 앱에 사용자를 리디렉션합니다 하는 경우는 `logout` 끝점에이 끝점 브라우저에서 사용자의 세션을 지웁니다. 앱으로 이동 하지 않은 경우는 `logout` 끝점, 사용자는 다시 인증 앱에 자격 증명을 다시 입력 하지 않고 유효한 단일 로그인 세션 Microsoft Id 플랫폼 v2.0 끝점과 있기 때문에 있습니다.

자세한 내용은 참조는 [로그 아웃 요청을 보냅니다](v2-protocols-oidc.md#send-a-sign-out-request) 섹션을 [Microsoft Id 플랫폼 v2.0 및 OpenID Connect 프로토콜을](v2-protocols-oidc.md) 개념 설명서.

## <a name="application-registration"></a>애플리케이션 등록

응용 프로그램 등록 중에서는 등록 된 **로그 아웃 URI를 게시**합니다. 이 자습서에 등록 된 `https://localhost:44321/signout-oidc` 에 **로그 아웃 URL** 필드를 **고급 설정** 섹션를 **인증** 페이지. 자세한 세부 정보를 참조 하십시오 [ webApp 앱 등록](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>ASP.NET Core 코드

### <a name="signout-button"></a>로그 아웃 단추

로그 아웃 단추에 노출 됩니다 `Views\Shared\_LoginPartial.cshtml` (즉, 사용자가 이전에 로그인 하는 경우)는 인증 된 계정이 있는 경우에 표시 합니다.

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` 작업을 `AccountController`

키를 눌러 합니다 **로그 아웃** 웹 앱 트리거 단추를 `SignOut` 작업에는 `Account` 컨트롤러. 이전 버전의 ASP.NET core 템플릿은 `Account` 컨트롤러는 웹 앱을 사용 하 여 포함 된 있지만이 경우 더 이상 이므로 이제 ASP.NET Core 프레임 워크 자체의 일부입니다. 

에 대 한 코드를 `AccountController` 리포지토리에서 ASP.NET core에서에서 가능 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)합니다. 계정 컨트롤:

- 집합 OpenID는 리디렉션 URI를 `/Account/SignedOut` 컨트롤러가 Azure AD가 로그 아웃을 수행 하는 경우에 다시 호출 되도록
- 호출 `Signout()`, Microsoft id 플랫폼에 게 문의 OpenIdConnect 미들웨어를 수 있는 `logout` 끝점입니다.

  - 브라우저에서 세션 쿠키를 지웁니다 및
  - 호출은 마지막으로 콜백 합니다 **로그 아웃 URL**는) 기본적으로 서명 된 뷰 페이지 표시 [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) ASP.NET Core의 일부로 제공 합니다.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>에 대 한 호출을 가로채는 `logout` 끝점

ASP.NET Core OpenIdConnect 미들웨어에 앱이 Microsoft id 플랫폼에 대 한 호출을 가로챌 수 있도록 `logout` 라는 OpenIdConnect 이벤트를 제공 하 여 끝점 `OnRedirectToIdentityProviderForSignOut`합니다. 웹 앱이를 사용 하 여 로그 아웃 하는 경우 사용자에 게 표시할 계정 선택 대화 상자를 방지 하려고 합니다. 이 인터 셉이 션 이루어집니다 합니다 `AddAzureAdV2Authentication` 의 `Microsoft.Identity.Web` 재사용 가능한 라이브러리입니다. 참조 [StartupHelpers.cs L58 L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET 코드

ASP.NET에서 컨트롤러 (예를 들어 AccountController)에서 signout () 메서드에서 트리거되는 로그 아웃 합니다. 이 메서드 (ASP.NET Core에서 조사할) 달리 ASP.NET 프레임 워크의 일부가 아닌 비동기를 사용 하지 않는 및 때문에 해당 합니다.

- OpenId 로그 아웃 챌린지를 보냅니다.
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
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>프로토콜

ASP.NET Core 또는 ASP.NET을 사용 하지 않으려는 경우에에서 사용할 수 있는 프로토콜 설명서를 살펴볼 수 있습니다 [Open ID Connect](./v2-protocols-oidc.md)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-web-app-sign-user-production.md)
