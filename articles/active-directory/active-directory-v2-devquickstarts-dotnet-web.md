<properties
	pageTitle="앱 모델 v2.0 .NET 웹앱 | Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정 둘 다로 사용자를 로그인하는 .NET MVC Web 웹앱을 빌드하는 방법입니다."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# 앱 모델 v2.0 미리 보기: .NET MVC 웹앱에 로그인 추가

v2.0 앱 모델에서는 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다를 지원하는 인증을 웹앱에 빠르게 추가할 수 있습니다. Asp.NET 웹앱에서는 .NET Framework 4.5에 포함된 Microsoft OWIN 미들웨어를 사용하여 이 작업을 수행할 수 있습니다.

  > [AZURE.NOTE]
    이 정보는 v2.0 앱 모델 공개 미리 보기에 적용됩니다. 일반 공급 Azure AD 서비스와 통합하는 방법에 대한 지침은 [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.

 여기서는 OWIN을 사용하여 다음과 같은 작업을 수행합니다. 즉, Azure AD와 v2.0 앱 모델을 사용하여 사용자를 앱에 로그인하고, 사용자에 대한 일부 정보를 표시하고, 사용자를 앱에서 로그아웃합니다.

이 작업을 수행하려면 다음 작업이 필요합니다.

1. 앱을 등록합니다.
2. OWIN 인증 파이프라인을 사용하도록 앱을 설정합니다.
3. OWIN을 사용하여 Azure AD에 로그인 및 로그아웃 요청을 실행합니다.
4. 사용자에 대한 데이터를 출력합니다.

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

전체 앱은 이 자습서 마지막 부분에서도 제공됩니다.

## 1\. 앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새 앱을 만들거나 다음 [자세한 단계](active-directory-v2-app-registration.md)를 따르십시오. 다음을 수행해야 합니다.

- 곧 필요하게 되므로 앱에 할당된 **응용 프로그램 ID**를 적어둡니다.
- 앱에 대한 **웹** 플랫폼을 추가합니다.
- 올바른 **리디렉션 URI**를 입력합니다. 리디렉션 URI는 인증 응답을 보내야 하는 Azure AD를 나타냅니다. 이 자습서에 대한 기본값은 `https://localhost:44326/`입니다.

## 2\. OWIN 인증 파이프라인을 사용하도록 앱을 설정합니다.
여기서는 OpenID Connect 인증 프로토콜을 사용하도록 OWIN 미들웨어를 구성합니다. OWIN은 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 데 사용됩니다.

-	먼저 프로젝트의 루트에서 `web.config` 파일을 열고 `<appSettings>` 섹션에 앱의 구성 값을 입력합니다.
    -	`ida:ClientId`은(는) 등록 포털에서 앱에 할당된 **응용 프로그램 ID**입니다.
    -	`ida:RedirectUri`은(는) 포털에서 입력한 **리디렉션 URI**입니다.

-	그 다음, 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 프로젝트에 추가합니다.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	`Startup.cs` 프로젝트에 OWIN Startup 클래스를 추가하고, 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** --> **새 항목** --> "OWIN" 검색을 클릭합니다. OWIN 미들웨어는 앱이 시작되면 `Configuration(...)` 메서드를 호출합니다.
-	클래스 선언을 이미 다른 파일에서 이 클래스의 일부를 구현했던 `public partial class Startup`으로 변경합니다. `Configuration(...)` 메서드에서 ConfigureAuth(...)를 호출하여 웹앱에 대한 인증을 설정합니다.  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
	public partial class Startup
	{
		public void Configuration(IAppBuilder app)
		{
			ConfigureAuth(app);
		}
	}
}```

-	Open the file `App_Start\Startup.Auth.cs` and implement the `ConfigureAuth(...)` method.  The parameters you provide in `OpenIdConnectAuthenticationOptions` will serve as coordinates for your app to communicate with Azure AD.  You'll also need to set up Cookie Authentication - the OpenID Connect middleware uses cookies underneath the covers.

```C#
public void ConfigureAuth(IAppBuilder app)
			 {
					 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

					 app.UseCookieAuthentication(new CookieAuthenticationOptions());

					 app.UseOpenIdConnectAuthentication(
							 new OpenIdConnectAuthenticationOptions
							 {
									 // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
									 // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
									 // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

									 ClientId = clientId,
									 Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
									 RedirectUri = redirectUri,
									 Scope = "openid",
									 ResponseType = "id_token",
									 PostLogoutRedirectUri = redirectUri,
									 TokenValidationParameters = new TokenValidationParameters
									 {
											 ValidateIssuer = false,
									 },
									 Notifications = new OpenIdConnectAuthenticationNotifications
									 {
											 AuthenticationFailed = OnAuthenticationFailed,
									 }
							 });
			 }
```

## 3. Use OWIN to issue sign-in and sign-out requests to Azure AD
Your app is now properly configured to communicate with the v2.0 endpoint using the OpenID Connect authentication protocol.  OWIN has taken care of all of the ugly details of crafting authentication messages, validating tokens from Azure AD, and maintaining user session.  All that remains is to give your users a way to sign in and sign out.

- You can use authorize tags in your controllers to require that user signs in before accessing a certain page.  Open `Controllers\HomeController.cs`, and add the `[Authorize]` tag to the About controller.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	또한 OWIN을 사용하여 코드 내에서 직접 인증 요청을 실행할 수도 있습니다. `Controllers\AccountController.cs`을(를) 엽니다. SignIn() 및 SignOut() 작업에서 각각 OpenID Connect 챌린지 및 로그아웃 요청을 실행합니다.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-	이제 `Views\Shared\_LoginPartial.cshtml`을 엽니다. 여기서 사용자에게 앱 로그인 및 로그아웃 링크를 표시하고 보기에 사용자 이름을 출력하게 됩니다.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## 4\. 사용자 정보 표시
OpenID Connect로 사용자를 인증할 때 v2.0 끝점은 [클레임](active-directory-v2-tokens.md#id_tokens) 또는 사용자에 대한 어설션을 포함하는 id\_token을 앱에 반환합니다. 이러한 클레임을 사용하여 앱 개인 설정을 수행할 수 있습니다.

- `Controllers\HomeController.cs` 파일을 엽니다. `ClaimsPrincipal.Current` 보안 주체 개체를 통해 컨트롤러의 사용자 클레임에 액세스할 수 있습니다.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

마지막으로 앱을 빌드하고 실행합니다. 개인 Microsoft 계정이나 회사 또는 학교 계정으로 로그인하고 위쪽 탐색 모음에 사용자 ID가 반영되는 방식을 확인합니다. 이제 개인 및 회사/학교 계정으로 사용자를 인증할 수 있는 업계 표준 프로토콜을 사용하여 웹앱이 보안되었습니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## 다음 단계

이제 좀 더 고급 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[v2.0 앱 모델을 사용하여 Web API 보안 유지 >>](active-directory-devquickstarts-webapi-dotnet.md)

추가 리소스는 다음을 확인해보세요. 
- [앱 모델 v2.0 미리 보기 >>](active-directory-appmodel-v2-overview.md) 
- [스택 오버플로 "azure-active-directory" 태그 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=AcomDC_1203_2015-->