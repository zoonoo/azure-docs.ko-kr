<properties
	pageTitle="앱 모델 v2.0 | Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정 둘 다의 토큰을 허용하는 .NET MVC Web API를 빌드하는 방법입니다."
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# 앱 모델 v2.0 미리 보기: MVC Web API 보안 유지

v2.0 앱 모델에서는 [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 액세스 토큰을 사용하여 Web API를 보호함으로써 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다를 가진 사용자가 Web API에 안전하게 액세스할 수 있도록 합니다.

> [AZURE.NOTE]이 정보는 v2.0 앱 모델 공개 미리 보기에 적용됩니다. 일반 공급 Azure AD 서비스와 통합하는 방법에 대한 지침은 [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.

ASP.NET Web API에서는 .NET Framework 4.5에 포함된 Microsoft OWIN 미들웨어를 사용하여 이 작업을 수행할 수 있습니다. 여기서는 OWIN을 사용하여 클라이언트가 사용자의 할 일 모음에서 작업을 만들고 읽을 수 있도록 하고, 보호되는 API를 지정하고, Web API에 유효한 액세스 토큰이 포함되어 있는지 확인하는 "To Do List" Web API를 빌드하겠습니다.

이 작업을 수행하려면 다음 작업이 필요합니다.

1. Azure AD에 앱 등록
2. OWIN 인증 파이프라인을 사용하도록 앱을 설정합니다.
3. To Do List Web API를 호출하도록 클라이언트 앱 구성

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

The completed app is provided at the end of this tutorial as well.


## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.

This visual studio solution also contains a "TodoListClient", which is a simple WPF app.  The TodoListClient is used to demonstrate how a user signs-in and how a client can issue requests to your Web API.  In this case, both the TodoListClient and the TodoListService are represented by the same app.  To configure the TodoListClient, you should also:

- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.


## 2. Set up your app to use the OWIN authentication pipeline

Now that you’ve registered an app, you need to set up your app to communicate with the v2.0 endpoint in order to validate incoming requests & tokens.

-	To begin, open the solution and add the OWIN middleware NuGet packages to the TodoListService project using the Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService ```

-	OWIN Startup 클래스를 `Startup.cs`라는 TodoListService 프로젝트에 추가합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** --> **새 항목** --> "OWIN" 검색을 클릭합니다. OWIN 미들웨어는 앱이 시작되면 `Configuration(…)` 메서드를 호출합니다.
-	클래스 선언을 이미 다른 파일에서 이 클래스의 일부를 구현했던 `public partial class Startup`으로 변경합니다. `Configuration(…)` 메서드에서 ConfigureAuth(...)를 호출하여 웹앱에 대한 인증을 설정합니다.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	`App_Start\Startup.Auth.cs` 파일을 열고 v2.0 끝점의 토큰을 허용하도록 Web API를 설정하는 `ConfigureAuth(…)` 메서드를 구현합니다.

```C#
public void ConfigureAuth(IAppBuilder app)
{
		var tvps = new TokenValidationParameters
		{
				// In this app, the TodoListClient and TodoListService
				// are represented using the same Application Id - we use
				// the Application Id to represent the audience, or the
				// intended recipient of tokens.

				ValidAudience = clientId,

				// In a real applicaiton, you might use issuer validation to
				// verify that the user's organization (if applicable) has
				// signed up for the app.  Here, we'll just turn it off.

				ValidateIssuer = false,
		};

		// Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
		// The options provided here tell the middleware about the type of tokens
		// that will be recieved, which are JWTs for the v2.0 endpoint.

		// NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
		// metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
		// OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
		// metadata document.

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{
				AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
		});
}
```

-	이제 `[Authorize]` 특성을 사용하여 OAuth 2.0 전달자 인증으로 컨트롤러 및 작업을 보호할 수 있습니다. authorize 태그를 사용하여 `Controllers\TodoListController.cs` 클래스를 데코레이팅합니다. 이렇게 하면 사용자는 해당 페이지에 액세스하기 전에 강제로 로그인됩니다.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- 권한 있는 호출자가 `TodoListController` API 중 하나를 호출하면 작업은 호출자에 대한 정보에 액세스해야 합니다. OWIN은 `ClaimsPrincpal` 개체를 통해 전달자 토큰 내의 클레임에 액세스할 수 있도록 합니다.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-	마지막으로 TodoListService 프로젝트의 루트에서 `web.config` 파일을 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.
  -	`ida:Audience`는 포털에 입력한 앱의 **응용 프로그램 ID**입니다.

## 3\. 클라이언트 앱 구성 및 서비스 실행
Todo List Service가 작동하는 것을 보려면 먼저 v2.0 끝점에서 토큰을 가져오고 서비스를 호출할 수 있도록 Todo List Client를 구성해야 합니다.

- TodoListClient 프로젝트에서 `App.config`를 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.
  -	포털에서 복사한 `ida:ClientId` 응용 프로그램 Id입니다.
	- `ida:RedirectUri`는 포털의 **리디렉션 URI**입니다.

마지막으로, 각 프로젝트를 정리하고 빌드한 후 실행합니다. 이제 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다의 토큰을 허용하는 .NET MVC Web API가 있습니다. TodoListClient에 로그인하고 Web API를 호출하여 사용자의 할 일 모음에 작업을 추가합니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## 다음 단계
이제 추가 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[v2.0 앱 모델을 사용한 웹앱에서 Web API 호출 >>](active-directory-devquickstarts-webapp-webapi-dotnet.md)

추가 리소스는 다음을 확인해보세요. - [앱 모델 v2.0 미리 보기 >>](active-directory-appmodel-v2-overview.md) - [스택 오버플로 "azure-active-directory" 태그 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO7-->