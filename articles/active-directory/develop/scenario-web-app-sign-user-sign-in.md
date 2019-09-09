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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9766b530b4d795d0f35f097de20155cdd17687ca
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812389"
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

## <a name="application-registration"></a>애플리케이션 등록

응용 프로그램을 등록 하는 동안 **사후 로그 아웃 URI**를 등록 합니다. 이 자습서에서는 **인증** 페이지의 `https://localhost:44321/signout-oidc` **고급 설정** 섹션에 있는 **로그 아웃 URL** 필드에 등록 했습니다. 자세한 내용은 [webApp 앱 등록](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp) 을 참조 하세요.

## <a name="aspnet-core-code"></a>ASP.NET Core 코드

### <a name="signout-button"></a>Signout 단추

로그 아웃 단추는에 `Views\Shared\_LoginPartial.cshtml` 노출 되며 인증 된 계정이 있는 경우에만 표시 됩니다 (사용자가 이전에 로그인 한 경우).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`작업`AccountController`

웹 앱에서 **로그 아웃** 단추를 누르면 `SignOut` `Account` 컨트롤러에서 작업이 트리거됩니다. 이전 버전의 ASP.NET core 템플릿에서 컨트롤러는 `Account` 웹 앱에 포함 되었지만 이제는 ASP.NET Core 프레임 워크 자체의 일부가 아닙니다. 

의 코드 `AccountController` 는 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)의 ASP.NET core 리포지토리에서 사용할 수 있습니다. 계정 컨트롤:

- Openid connect 리디렉션 URI를로 설정 `/Account/SignedOut` 하 여 Azure AD에서 로그 아웃을 수행한 경우 컨트롤러가 다시 호출 되도록 합니다.
- 를 `Signout()`호출 하면 openidconnect 미들웨어가 다음을 수행 하는 Microsoft `logout` id 플랫폼 끝점에 연결할 수 있습니다.

  - 브라우저에서 세션 쿠키를 지웁니다.
  - 는 마지막으로를 호출 하 여 **로그 아웃 URL**을 다시 호출 합니다. 기본적으로는 로그 아웃 된 뷰 페이지를 표시 합니다 [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) 은 ASP.NET Core의 일부로도 제공 됩니다.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>`logout` 끝점에 대 한 호출 가로채기

ASP.NET Core openidconnect 미들웨어를 사용 하면 앱에서 라는 `logout` `OnRedirectToIdentityProviderForSignOut`openidconnect 이벤트를 제공 하 여 Microsoft id 플랫폼 끝점에 대 한 호출을 가로챌 수 있습니다. 웹 앱은이를 사용 하 여 로그 아웃 시 계정 선택 대화 상자가 사용자에 게 표시 되는 것을 방지 하려고 합니다. 이 가로채기는 `Microsoft.Identity.Web` 재사용 가능한 라이브러리 `AddAzureAdV2Authentication` 의에서 수행 됩니다. [StartupHelpers.cs L58-L66를](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66) 참조 하세요.

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

ASP.NET에서 로그 아웃은 컨트롤러 (예를 들어 AccountController)의 SignOut () 메서드에서 트리거됩니다. 이 메서드는 ASP.NET 프레임 워크의 일부가 아니며 (ASP.NET Core에서 발생 하는 것과 반대) 비동기를 사용 하지 않으므로 다음과 같은 이유 때문일 수 있습니다.

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
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>프로토콜

ASP.NET Core 또는 ASP.NET를 사용 하지 않으려는 경우 [OPEN ID Connect](./v2-protocols-oidc.md)에서 사용할 수 있는 프로토콜 설명서를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-web-app-sign-user-production.md)
