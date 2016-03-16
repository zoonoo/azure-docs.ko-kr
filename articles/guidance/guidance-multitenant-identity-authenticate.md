<properties
   pageTitle="다중 테넌트 응용 프로그램의 인증 | Microsoft Azure"
   description="다중 테넌트 응용 프로그램이 Azure AD에서 사용자를 인증하는 방법"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# 다중 테넌트 응용 프로그램의 인증

이 문서는 [시리즈의 일부](guidance-multitenant-identity.md)입니다. 이 시리즈와 함께 제공되는 완전한 [응용 프로그램 예제]도 있습니다.

이 문서에서는 다중 테넌트 응용 프로그램이 인증할 OpenID Connect(OIDC)를 사용하여 Azure AD(Azure Active Directory)에서 사용자를 어떻게 인증할 수 있는지 설명합니다.

## 개요

[참조 구현](guidance-multitenant-identity-tailspin.md)은 ASP.NET Core 1.0 응용 프로그램입니다. 응용 프로그램은 기본 제공 OpenID Connect 미들웨어를 사용하여 OIDC 인증 흐름을 수행합니다. 다음 다이어그램은 사용자가 로그인할 때 진행 과정을 개략적인 수준으로 보여 줍니다.

![인증 흐름](media/guidance-multitenant-identity/auth-flow.png)

1.	사용자가 앱에서 "로그인" 단추를 클릭합니다. 이 작업은 MVC 컨트롤러에서 처리됩니다.
2.	MVC 컨트롤러는 **ChallengeResult** 작업을 반환합니다.
3.	미들웨어는 **ChallengeResult**를 가로채고 사용자를 Azure AD 로그인 페이지로 리디렉션하는 302 응답을 생성합니다.
4.	사용자는 Azure AD로 인증합니다.
5.	Azure AD는 ID 토큰을 응용 프로그램으로 보냅니다.
6.	미들웨어는 ID 토큰의 유효성을 검사합니다. 이때 사용자가 응용 프로그램 내부에서 인증됩니다.
7.	미들웨어는 사용자를 응용 프로그램으로 다시 리디렉션합니다.

## Azure AD로 응용 프로그램 등록

OpenID Connect를 사용하도록 설정하려면 SaaS 공급자는 응용 프로그램을 자신의 Azure AD 테넌트 내에 등록합니다.

응용 프로그램을 등록하려면 "Azure 관리 포털에서 응용 프로그램을 등록하려면" 섹션의 [Azure Active Directory와 응용 프로그램 통합](../active-directory/active-directory-integrating-applications.md)의 단계를 따릅니다.

**구성** 페이지에서

-	클라이언트 ID를 적어둡니다.
-	**응용 프로그램은 다중 테넌트**에서 **예**를 선택합니다.
-	**회신 URL**을 Azure AD가 인증 응답을 보내는 URL로 설정합니다. 앱의 기본 URL을 사용할 수 있습니다.
  -	참고: URL 경로는 호스트 이름이 배포된 앱과 일치하기만 하면 어떤 항목도 가능합니다.
  -	회신 URL을 여러 개 설정할 수 있습니다. 개발하는 동안에는 앱을 로컬로 실행하기 위해 `localhost` 주소를 사용할 수 있습니다.
-	클라이언트 암호 생성: **키** 아래에서 **기간 선택** 드롭다운을 클릭하고 1년 또는 2년을 선택합니다. **저장**을 클릭하면 키가 표시됩니다. 구성 페이지를 다시 로드하면 값이 다시 표시되지 않으므로 값을 복사해 두어야 합니다.

## 인증 미들웨어 구성

이 섹션에서는 ASP.NET Core 1.0에서 OpenID Connect로 다중 테넌트 인증을 위한 인증 미들웨어를 구성하는 방법을 설명합니다.

시작 클래스에서 OpenID Connect 미들웨어를 추가합니다.

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs)를 참조하세요.

시작 클래스에 대한 자세한 내용은 ASP.NET Core 1.0 설명서에서 [응용 프로그램 시작](https://docs.asp.net/en/latest/fundamentals/startup.html)을 참조하세요.

다음과 같은 미들웨어 옵션을 설정합니다.

- **ClientId**. Azure AD에 응용 프로그램을 등록할 때 얻은 응용 프로그램의 클라이언트 ID입니다.
- **Authority**. 다중 테넌트 응용 프로그램의 경우 `https://login.microsoftonline.com/common/`으로 설정합니다. Azure AD 공용 끝점에 대한 URL로, Azure AD 테넌트의 사용자가 이를 통해 로그인할 수 있습니다. 공용 끝점에 대한 자세한 내용은 [이 블로그 게시물](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/)을 참조하세요.
- **TokenValidationParameters**에서 **ValidateIssuer**를 false로 설정합니다. 즉, 앱은 ID 토큰에서 발급자 값의 유효성을 검사하는 것을 담당합니다. (미들웨어 토큰 자체의 유효성을 계속 검사합니다.) 발급자의 유효성을 검사하는 방법에 대한 자세한 내용은 [발급자 유효성 검사](guidance-multitenant-identity-claims.md#issuer-validation)를 참조하세요.
- **CallbackPath**. Azure AD에 등록한 회신 URL의 경로와 동일하게 설정합니다. 예를 들어, 회신 URL이 `http://contoso.com/aadsignin`이면, **CallbackPath**는 `aadsignin`여야 합니다. 이 옵션을 설정하지 않으면, 기본값은 `signin-oidc`입니다.
- **PostLogoutRedirectUri**. 로그아웃한 후 사용자를 리디렉션할 URL을 지정합니다. 익명 요청을 허용하는 페이지여야 하며 일반적으로 홈 페이지입니다.
- **SignInScheme**. `CookieAuthenticationDefaults.AuthenticationScheme`로 설정합니다. 이 설정은 사용자가 인증된 후 사용자 클레임이 쿠키에 로컬로 저장됨을 의미합니다. 이 쿠키는 브라우저 세션 중에 사용자가 로그인을 유지하는 방법입니다.
- **이벤트.** 이벤트 콜백, [인증 이벤트](#authentication-events)를 참조하세요.

또한 파이프라인에 쿠키 인증 미들웨어를 추가합니다. 이 미들웨어는 쿠키에 사용자 클레임을 기록한 후 후속 페이지 로드 중에 쿠키를 읽는 역할을 합니다.

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## 인증 흐름 시작

ASP.NET MVC에서 인증 흐름을 시작하려면 컨트롤러에서 **ChallengeResult**를 반환합니다.

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

그러면 미들웨어가 인증 끝점으로 리디렉션하는 302(Found) 응답을 반환합니다.

## 사용자 로그인 세션

언급했듯이 사용자가 처음 로그인할 때, 쿠키 인증 미들웨어는 사용자 클레임을 쿠키에 씁니다. 그 후에는 쿠키를 읽어 HTTP 요청을 인증합니다.

기본적으로 쿠키 미들웨어는 [세션 쿠키][session-cookie]를 기록하며, 이 내용은 사용자가 브라우저를 닫으면 삭제됩니다. 사용자가 사이트를 다음에 방문하는 경우 다시 로그인해야 합니다. 그러나 **ChallengeResult**에서 **IsPersistent**를 true로 설정하면 미들웨어는 영구 쿠키를 기록하므로 사용자가 브라우저를 닫아도 로그인 상태를 유지합니다. 쿠키 기간 만료를 구성할 수 있습니다. [쿠키 제어 옵션][cookie-options]을 참조하세요. 영구 쿠키는 사용자에게 가장 편리하지만 사용자가 매번 로그인하도록 하려는 일부 응용 프로그램(예: 금융 응용 프로그램)에는 부적절할 수 있습니다.

## OpenID Connect 미들웨어 정보

ASP.NET의 OpenID Connect 미들웨어는 대부분의 프로토콜 정보를 숨깁니다. 이 섹션에는 프로토콜 흐름을 이해하는 데 유용할 수 있는 구현에 대 한 일부 정보가 들어 있습니다.

먼저, ASP.NET를 기준으로 하는 인증 흐름을 살펴보겠습니다(앱과 Azure AD 간의 OIDC 프로토콜 흐름에 대한 세부 정보는 생략). 다음 다이어그램은 이 프로세스를 보여 줍니다.

![로그인 흐름](media/guidance-multitenant-identity/sign-in-flow.png)

이 다이어그램에는 두 개의 MVC 컨트롤러가 있습니다. Account 컨트롤러는 로그인 요청을 처리하고 Home 컨트롤러는 홈 페이지를 서비스합니다.

다음은 인증 프로세스입니다.

1. 사용자가 "로그인" 단추를 클릭하면 브라우저는 GET 요청을 보냅니다. 예: `GET /Account/SignIn/`
2. Account 컨트롤러는 `ChallengeResult`를 반환합니다.
3. OIDC 미들웨어는 Azure AD로 리디렉션하는 HTTP 302 응답을 반환합니다.
4. 브라우저가 Azure AD에 인증 요청을 보냅니다.
5. 사용자가 Azure AD에 로그인하고 Azure AD는 인증 응답을 다시 보냅니다.
6. OIDC 미들웨어는 클레임 주체를 만들고 이를 쿠키 인증 미들웨어에 전달합니다.
7. 쿠키 미들웨어는 클레임 주체를 직렬화하고 쿠키를 설정합니다.
8. OIDC 미들웨어는 응용 프로그램의 콜백 URL로 리디렉션됩니다.
10. 브라우저는 요청에 쿠키를 전송하는 리디렉션을 따릅니다.
11. 쿠키 미들웨어는 클레임 주체로 쿠키를 역직렬화하고 클레임 주체와 동일하게 `HttpContext.User`를 설정합니다. 요청은 MVC 컨트롤러로 라우팅됩니다.

### 인증 티켓

인증이 성공하면 OIDC 미들웨어는 사용자의 클레임을 보유하는 클레임 주체를 포함하는 인증 티켓을 만듭니다. **AuthenticationValidated** 또는 **TicketReceived** 이벤트 내부 티켓에 액세스할 수 있습니다.

> [AZURE.NOTE] 전체 인증 흐름이 완료될 때까지 `HttpContext.User`는 인증된 사용자가 _아닌_ 익명 보안 주체를 계속 보유합니다. 익명 보안 주체에는 빈 클레임 컬렉션이 있습니다. 인증이 완료되고 앱이 리디렉션된 후에는 쿠키 미들웨어가 인증 쿠키를 역직렬화하고 `HttpContext.User`를 인증된 사용자를 나타내는 클레임 주체로 설정합니다.

### 인증 이벤트

인증 프로세스 중에 OpenID Connect 미들웨어는 일련의 이벤트를 발생시킵니다.

- **RedirectToAuthenticationEndpoint**. 미들웨어가 인증 끝점으로 리디렉션되기 바로 전에 호출됩니다. 이 이벤트를 사용하여 리디렉션 URL을 수정할 수 있습니다.예를 들어 요청 매개 변수를 추가하는 예는 [관리자 동의 프롬프트 추가](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt)를 참조하세요.

- **AuthorizationResponseReceived**. 미들웨어가 IDP(ID 공급자)에서 인증 응답을 받은 후 미들웨어가 응답을 유효성 검사하기 전에 호출됩니다.

- **AuthorizationCodeReceived**. 인증 코드와 함께 호출됩니다.

- **TokenResponseReceived**. 미들웨어가 IDP에서 액세스 토큰을 가져온 후 호출됩니다. 인증 코드 흐름에만 적용됩니다.

- **AuthenticationValidated**. 미들웨어가 ID 토큰의 유효성을 검사한 후 호출됩니다. 이때 응용 프로그램에는 사용자에 대해 유효성 검증된 클레임 집합이 포함됩니다. 이 이벤트를 사용하여 클레임에 대한 추가 유효성 검사를 수행하거나 클레임을 변환할 수 있습니다. [클레임 작업](guidance-multitenant-identity-claims.md)을 참조하세요.

- **UserInformationReceived**. 미들웨어가 사용자 정보 끝점에서 사용자 프로필을 가져오는 경우 호출됩니다. 인증 코드 흐름에만 적용되며 미들웨어 옵션에서 `GetClaimsFromUserInfoEndpoint = true`인 경우에만 적용됩니다.

- **TicketReceived**. 인증이 완료되면 호출됩니다. 인증이 성공했다고 가정할 경우 마지막 이벤트입니다. 이 이벤트가 처리된 후에는 사용자가 앱에 로그인됩니다.

- **AuthenticationFailed**. 인증에 실패하면 호출됩니다. 이 이벤트를 사용하여 예를 들어 오류 페이지로 리디렉션하여 인증 실패를 처리합니다.

이러한 이벤트에 대한 콜백을 제공하기 위해 미들웨어에서 **이벤트** 옵션을 설정합니다. 이벤트 처리기를 선언하는 두 가지 다른 방법이 있습니다. 람다에 인라인으로 포함하거나 **OpenIdConnectEvents**에서 파생된 클래스에 선언합니다.

람다에 인라인으로 포함

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

**OpenIdConnectEvents**에서 파생

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

두 번째 방법은 이벤트 콜백에 많은 논리가 포함된 경우 권장되며 시작 클래스를 복잡하지 않게 합니다. 참조 구현에서 이 방식을 사용합니다. [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs)를 참조하세요.

### OpenID Connect 끝점

Azure AD는 IDP(ID 공급자)가 [잘 알려진 끝점](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)에서 JSON 메타데이터 문서를 반환하는 [OpenID Connect Discovery](https://openid.net/specs/openid-connect-discovery-1_0.html)를 지원합니다. 메타데이터 문서에는 다음과 같은 정보가 들어 있습니다.

-	권한 부여 끝점의 URL. 사용자 인증을 위해 앱이 리디렉션되는 위치입니다.
-	"세션 종료" 끝점의 URL. 여기서 앱에서 사용자가 로그아웃됩니다.
-	클라이언트가 IDP에서 가져온 OIDC 토큰의 유효성을 검사하기 위해 사용하는 서명 키를 가져올 URL입니다.

기본적으로 OIDC 미들웨어는 이 메타데이터를 가져오는 방법을 알고 있습니다. 미들웨어에서 **Authority** 옵션을 설정합니다. 미들웨어는 메타데이터에 대한 URL을 생성합니다. (**MetadataAddress** 옵션을 설정하여 메타데이터 URL을 재정의할 수 있습니다.)

### OpenID connect 흐름

기본적으로 OIDC 미들웨어는 폼 게시 응답 모드로 하이브리드 흐름을 사용합니다.

-	_하이브리드 흐름_은 클라이언트가 동일한 왕복에 있는 ID 토큰 및 인증 코드를 인증 서버로 가져올 수 있음을 의미합니다.
-	_폼 게시 응답 모드_는 인증 서버가 HTTP POST 요청을 사용하여 ID 토큰 및 인증 코드를 앱으로 보내는 것을 의미합니다. 값은 form-urlencoded입니다(content type = "application/x-www-form-urlencoded").

OIDC 미들웨어가 권한 부여 끝점으로 리디렉션되면 리디렉션 URL은 OIDC에 필요한 모든 쿼리 문자열 매개 변수를 포함합니다. 하이브리드 흐름의 경우 다음과 같습니다.

-	client\_id. 이 값은 **ClientId** 옵션에서 설정됩니다.
-	scope = "openid profile", OIDC 요청이며 사용자의 프로필을 원한다는 것을 의미합니다.
-	response\_type = "code id\_token". 하이브리드 흐름을 지정합니다.
-	response\_mode = "form\_post". 폼 게시 응답을 지정합니다.

서로 다른 흐름을 지정하려면 옵션에서 **ResponseType** 속성을 설정합니다. 예:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[응용 프로그램 예제]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->