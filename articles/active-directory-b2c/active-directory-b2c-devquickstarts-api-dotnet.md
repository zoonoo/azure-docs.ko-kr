<properties
	pageTitle="Azure AD B2C 미리 보기 | Microsoft Azure"
	description="Azure AD B2C,를 사용하여 NET 웹 API를 빌드하는 방법이며 인증을 위해 OAuth 2.0 액세스 토큰을 사용하여 보호됩니다."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>
	
# Azure AD B2C 미리 보기: .NET 웹 API 빌드

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C로 OAuth 2.0 액세스 토큰을 사용하여 웹 API를 보호할 수 있으며 이는 Azure AD B2C API를 사용하여 API에 인증하는 클라이언트 앱을 사용하도록 설정합니다. 이 문서에서는 사용자 등록, 로그인 및 프로필 관리를 포함하는 .NET MVC "할 일 모음" 앱을 만드는 방법을 보여줍니다. 각 사용자의 할 일 모음은 작업 서비스에서 저장됩니다. 인증된 사용자를 만들어서 해당 할 일 모음에서 작업을 만들고 읽을 수 있는 웹 API입니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 넘어가기 전에 [B2C 디렉터리 만들기](active-directory-b2c-get-started.md)로 이동합니다.

## 2\. 응용 프로그램 만들기

이제 B2C 디렉터리에 앱을 만들어야 하며 Azure AD가 앱과 안전하게 통신해야 한다는 일부 정보를 제공합니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 반드시

- 응용 프로그램에서 **웹앱/웹 API** 포함
- 웹앱의 경우 **리디렉션 Uri** `https://localhost:44316/`를 사용합니다. 이 코드 샘플에 대한 웹앱 클라이언트의 기본 위치입니다.
- 앱에 할당된 **응용 프로그램 ID**를 적복사합니다. 곧 필요합니다.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. 정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [**정책**](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 코드 샘플의 클라이언트는 등록, 로그인 및 편집 프로필 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

- ID 공급자 블레이드에서 **사용자 ID 등록** 또는 **메일 등록** 중 하나를 선택합니다.
- 등록 정책에서 **표시 이름** 및 다른 몇 가지 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 클레임을 응용 프로그램 클레임으로 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 이러한 정책 이름이 곧 필요합니다. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 성공적으로 만들었다면 앱을 빌드할 준비가 되었습니다.

## 4\. 코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet)에서 유지 관리됩니다. 진행하면서 샘플을 빌드하려면 [골격 프로젝트를 .zip으로 다운로드](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip)하거나 골격을 복제합니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

완성된 앱도 [.zip으로 다운로드하거나](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.

샘플 코드를 다운로드하면 Visual Studio `.sln` 파일을 열어 시작합니다. 솔루션에 `TaskWebApp` 프로젝트와 `TaskService` 프로젝트라는 두 프로젝트가 있는 것을 확인합니다. `TaskWebApp`은 사용자가 상호 작용하는 MVC 웹 응용 프로그램입니다. `TaskService`는 각 사용자의 할 일 모음을 저장하는 앱의 백 엔드 Web API입니다.

## 5\. 작업 웹앱 구성

사용자가 `TaskWebApp`을 조작할 때 클라이언트는 Azure AD로 요청을 보내고 `TaskService` Web API를 호출하는 데 사용할 수 있는 토큰을 받습니다. 사용자를 로그인하고 토큰을 가져오려면 앱에 대한 일부 정보를 `TaskWebApp`에 제공해야 합니다. `TaskWebApp` 프로젝트에서 프로젝트 루트에 있는 `web.config` 파일을 열고 `<appSettings>` 섹션의 값을 바꿉니다.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

로그인 정책 이름을 제공해야 하는 두 개의 `[PolicyAuthorize]` 데코레이터도 있습니다. 사용자가 인증이 필요한 앱의 페이지에 액세스하려고 하면 `[PolicyAuthorize]` 특성이 특정 정책을 호출하는 데 사용됩니다.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

`TaskWebApp`과 같은 웹앱이 Azure AD B2C를 사용하는 방법을 알아보려면 [웹앱 로그인 시작 문서](active-directory-b2c-devquickstarts-web-dotnet.md)를 확인하세요.

## 6\. API 보호

이제 사용자를 대신해서 API를 호출하는 클라이언트가 있으므로 OAuth 2.0 전달자 토큰을 사용하여 `TaskService` 보안을 유지할 수 있습니다. API는 Microsoft의 OWIN 라이브러리를 사용하여 토큰을 수락하고 유효성을 검사할 수 있습니다.

#### OWIN 설치
OWIN OAuth 인증 파이프라인을 설치하여 시작합니다.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

#### B2C 세부 정보 입력
`TaskService` 프로젝트의 루트에 있는 `web.config` 파일을 열고 `<appSettings>` 섹션의 값을 바꿉니다. 이러한 값은 API 및 OWIN 라이브러리 전체에서 사용됩니다.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings> 
```

#### OWIN 시작 클래스 추가
`Startup.cs`라는 `TaskService` 프로젝트에 OWIN 시작 클래스를 추가합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** --> **새 항목** --> "OWIN" 검색을 클릭합니다.
  

```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

#### OAuth 2.0 인증 구성
`App_Start\Startup.Auth.cs` 파일을 열고 `ConfigureAuth(...)` 메서드를 구현합니다.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	// These values are pulled from web.config
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
	private const string discoverySuffix = ".well-known/openid-configuration";

	public void ConfigureAuth(IAppBuilder app)
	{   
		TokenValidationParameters tvps = new TokenValidationParameters
		{
			// This is where you specify that your API only accepts tokens from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

#### 작업 컨트롤러 보호
이제 앱이 OAuth 2.0 인증을 사용하도록 구성되었으므로 작업 컨트롤러에 `[Authorize]` 태그를 추가하기만 하면 Web API 보안을 유지할 수 있습니다. 할 일 모음 조작이 발생하는 컨트롤러이므로 클래스 수준에서 전체 컨트롤러를 보호합니다. 보다 세분화한 제어를 위해 개별 작업에 `[Authorize]` 태그를 추가할 수도 있습니다.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

#### 토큰에서 사용자 정보 가져오기
`TaskController`는 데이터베이스에 작업을 저장하며, 여기서 각 작업에는 작업을 "소유"하는 연결된 사용자가 있습니다. 소유자는 사용자의 **개체 ID**로 식별됩니다. 모든 정책에서 개체 ID를 응용 프로그램 클레임으로 추가해야 했던 이유는 이 때문입니다.

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## 7\. 샘플 앱 실행

마지막으로, `TaskWebApp` 및 `TaskService` 둘 다를 빌드 및 실행합니다. 전자 메일 주소 또는 사용자 이름을 사용하여 앱에 등록합니다. 사용자의 할 일 모음에 일부 작업을 만들고 클라이언트를 중지하고 다시 시작한 후에 API에서 어떻게 유지할지 확인합니다.

## 8\. 청책 편집

이제 Azure AD B2C로 보호된 API가 있으므로 앱의 정책을 진행하고 API에서 효과(또는 부족)를 볼 수 있습니다. 정책에서 **응용 프로그램 클레임**을 <!--add **identity providers**
to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->조작하고 Web API에서 사용할 수 있는 사용자 정보를 변경할 수 있습니다. 위에서 설명한 것처럼, 추가한 모든 클레임은 `ClaimsPrincipal` 개체의 .NET MVC Web API에서 사용할 수 있습니다.

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO3-->