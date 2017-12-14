---
title: "Azure AD .NET 웹앱 시작 | Microsoft Docs"
description: "로그인을 위해 Azure AD와 통합되는 .NET MVC 웹앱을 빌드합니다."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 5025ebda6eb47c4155c098be4a5479d5c3814942
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>Azure AD에서 ASP.NET 웹앱 로그인 및 로그아웃
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure AD(Azure Active Directory)는 몇 개의 코드 줄만으로 단일 로그인 및 로그아웃을 제공하여 간단하게 웹앱의 ID 관리를 아웃소싱할 수 있도록 합니다. .NET (OWIN) 미들웨어에 대한 공개 웹 인터페이스의 Microsoft 구현을 사용하여 ASP.NET 웹앱 내부 및 외부 사용자를 등록할 수 있습니다. 커뮤니티 기반 OWIN 미들웨어는 .NET Framework 4.5에 포함됩니다. 이 문서에서는 다음에 OWIN을 사용하는 방법을 보여 줍니다.

* Azure AD를 ID 공급자로 사용하여 사용자를 웹앱에 로그인합니다.
* 일부 사용자 정보를 표시합니다.
* 앱에서 사용자를 로그아웃합니다.

## <a name="before-you-get-started"></a>시작하기 전에
* [앱 기본 사항](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)을 다운로드하거나 [완성된 샘플](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)을 다운로드하세요.
* 앱을 등록할 Azure AD 테넌트도 필요합니다. Azure AD 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).

준비가 완료되면 다음 4가지 섹션의 절차를 수행합니다.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>1단계: Azure AD에 새 앱 등록
사용자를 인증하도록 앱을 설정하려면 먼저 다음을 수행하여 앱을 테넌트에 등록합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 위쪽 모음에서 계정 이름을 클릭합니다. **디렉터리** 목록에서 앱을 등록할 Active Directory 테넌트를 선택합니다.
3. 왼쪽 창에서 **더 많은 서비스**를 클릭하고 **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 클릭하고 **추가**를 선택합니다.
5. 프롬프트에 따라 새 **웹 응용 프로그램 및/또는 WebAPI**를 만듭니다.
  * **이름**은 사용자에게 앱에 대해 설명합니다.
  * **로그온 URL**은 앱의 기본 URL입니다. 기본 사항의 기본 URL은 http://localhost:44320/입니다.
6. 등록이 완료되면 Azure AD가 앱에 고유한 응용 프로그램 ID를 할당합니다. 다음 섹션에서 사용할 수 있도록 앱 페이지의 값을 복사합니다.
7. 응용 프로그램에 대한 **설정** -> **속성** 페이지에서 앱 ID URI를 업데이트합니다. **앱 ID URI**는 앱의 고유 식별자입니다. 명명 규칙은 `https://<tenant-domain>/<app-name>`(예: `https://contoso.onmicrosoft.com/my-first-aad-app`)입니다.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>2단계: OWIN 인증 파이프라인을 사용하도록 앱 설정
이 단계에서 OpenID Connect 인증 프로토콜을 사용하도록 OWIN 미들웨어를 구성합니다. OWIN을 사용하여 로그인 및 로그아웃 요청을 실행하고, 사용자 세션을 관리하고, 사용자 정보를 가져오는 등의 작업을 수행할 수 있습니다.

1. 시작하려면 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 프로젝트에 추가합니다.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. `Startup.cs`라는 프로젝트에 OWIN Startup 클래스를 추가하려면 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택하고 **새 항목**을 선택한 다음 **OWIN**을 검색합니다. OWIN 미들웨어는 앱을 시작하면 **Configuration(...)** 메서드를 호출합니다.
3. 클래스 선언을 `public partial class Startup`으로 변경합니다. 다른 파일에서 이 클래스의 일부를 구현했습니다. **Configuration(...)** 메서드에서 **ConfgureAuth(...)**를 호출하여 앱에 대한 인증을 설정합니다.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. App_Start\Startup.Auth.cs 파일을 연 다음 **ConfigureAuth(...)** 메서드를 구현합니다. *OpenIDConnectAuthenticationOptions*에 제공하는 매개 변수는 앱이 Azure AD와 통신하기 위한 좌표로 사용됩니다. OpenID Connect 미들웨어는 백그라운드에 쿠키를 사용하므로 쿠키 인증도 설정해야 합니다.

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. 프로젝트 루트에 있는 web.config 파일을 연 다음 `<appSettings>` 섹션에 구성 값을 입력합니다.
  * `ida:ClientId`: "1단계: Azure AD에 새 앱 등록"에서 Azure Portal에서 복사한 GUID입니다.
  * `ida:Tenant`: Azure AD 테넌트의 이름(예: contoso.onmicrosoft.com)입니다.
  * `ida:PostLogoutRedirectUri`: 로그아웃 요청이 성공적으로 완료된 후 사용자가 리디렉션되는 Azure AD를 나타냅니다.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>3단계: OWIN을 사용하여 Azure AD에 로그인 및 로그아웃 요청 실행
이제 앱은 OpenID Connect 인증 프로토콜을 사용하여 Azure AD와 통신하도록 올바르게 구성되었습니다. OWIN이 인증 메시지를 작성하고, Azure AD에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 모든 세부 과정을 처리했습니다. 이제 사용자에게 로그인하고 로그아웃하는 방법을 알려주기만 하면 됩니다.

1. 컨트롤러에서 권한 부여 태그를 사용하여 사용자가 특정 페이지에 액세스하기 전에 로그인하도록 요구할 수 있습니다. 이렇게 하려면 Controllers\HomeController.cs를 연 다음 About 작업에 `[Authorize]` 태그를 추가합니다.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. 또한 OWIN을 사용하여 코드 내에서 직접 인증 요청을 실행할 수도 있습니다. 이렇게 하려면 Controllers\AccountController.cs를 엽니다. 그런 다음 SignIn() 및 SignOut() 작업에서 OpenID Connect 챌린지 및 로그아웃 요청을 실행합니다.

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Views\Shared\_LoginPartial.cshtml을 열고 사용자에게 앱 로그인 및 로그 아웃 링크를 표시하고 보기에서 사용자의 이름을 인쇄합니다.

    ```HTML
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

## <a name="step-4-display-user-information"></a>4단계: 사용자 정보 표시
OpenID Connect로 사용자를 인증할 때 Azure AD는 “클레임” 또는 사용자에 대한 어설션을 포함하는 id_token을 앱에 반환합니다. 다음을 수행하여 앱 개인에 맞게 이러한 클레임을 사용할 수 있습니다.

1. Controllers\HomeController.cs 파일을 엽니다. `ClaimsPrincipal.Current` 보안 주체 개체를 통해 컨트롤러의 사용자 클레임에 액세스할 수 있습니다.

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. 앱을 빌드 및 실행합니다. onmicrosoft.com 도메인을 사용하여 테넌트에 아직 새 사용자를 만들지 않았으면 이제 만들어야 합니다. 방법은 다음과 같습니다.

  a. 해당 사용자로 로그인하고 위쪽 모음에 사용자 ID가 반영되는 방식을 확인합니다.

  b. 로그아웃하고 테넌트의 다른 사용자로 다시 로그인합니다.

  c. 관심이 있으면 이 앱의 다른 인스턴스를 등록 및 실행하고(자체 clientId 사용) SSO(Single Sign-On) 작동을 살펴봅니다.

## <a name="next-steps"></a>다음 단계
참조용 자료로 [완성된 샘플](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)(사용자 구성 값 제외)을 참조하세요.

이제 좀 더 고급 항목으로 이동할 수 있습니다. 예를 들어 [Azure AD를 사용하여 Web API 보안 유지](active-directory-devquickstarts-webapi-dotnet.md)를 시도해 보세요.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
