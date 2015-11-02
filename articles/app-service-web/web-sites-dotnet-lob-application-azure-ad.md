<properties 
	pageTitle="Azure Active Directory 인증을 통해 Azure 앱 서비스에서 .NET MVC 웹앱 만들기" 
	description="Azure Active Directory로 인증하는 Azure 앱 서비스에서 ASP.NET MVC LOB(기간 업무) 응용 프로그램을 만드는 방법에 대해 알아봅니다." 
	services="app-service\web, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="10/14/2015" 
	ms.author="cephalin"/>

# Azure Active Directory 인증을 통해 Azure 앱 서비스에서 .NET MVC 웹앱 만들기 #

이 문서에서는 ID 공급자로 [Azure Active Directory](/services/active-directory/)를 사용하여 [Azure 앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)에서 ASP.NET MVC 기간 업무 응용 프로그램을 만드는 방법을 알아봅니다. 또한 [Azure Active Directory Graph 클라이언트 라이브러리](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx)를 사용하여 응용 프로그램의 디렉터리 데이터를 쿼리하는 방법에 대해 알아봅니다.

사용하는 Azure Active Directory 테넌트는 Azure 전용 디렉터리이거나, 온-프레미스 AD(Active Directory)와의 디렉터리 동기화를 통해 온-프레미스 또는 원격에 있는 작업자를 위한 SSO(Single Sign-On) 환경을 만들 수 있습니다.

>[AZURE.NOTE]Azure 앱 서비스 웹앱의 경우 몇 번의 단추 클릭만으로 Azure Active Directory 테넌트에 대한 인증을 구성할 수 있습니다. 자세한 내용은 [Azure 앱 서비스에서 인증을 위해 Active Directory 사용](web-sites-authentication-authorization.md)을 참조하세요

<a name="bkmk_build"></a>
## 빌드할 내용 ##

앱 서비스 웹앱에서 다음 기능을 통해 작업 항목을 추적하는 간단한 기간 업무 CRUD(만들기-읽기-업데이트-삭제) 응용 프로그램을 빌드합니다.

- Azure Active Directory에 대해 사용자 인증
- 로그인 및 로그아웃 기능 구현
- `[Authorize]`를 사용하여 다른 CRUD 동작에 대해 사용자에게 권한 부여
- [Azure Active Directory Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx)를 사용하여 Azure Active Directory 데이터 쿼리
- [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)(Windows Identity Foundation, 즉 WIF 대신)을 사용합니다. 이는 ASP.NET의 미래이며 WIF보다 인증 및 권한 부여 설정이 훨씬 간편합니다.

<a name="bkmk_need"></a>
## 필요한 사항 ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

이 자습서를 완료하려면 다음 항목이 필요합니다.

- 다양한 그룹의 사용자가 포함된 Azure Active Directory 테넌트
- Azure Active Directory 테넌트에서 응용 프로그램을 만들 수 있는 권한
- Visual Studio 2013
- [Azure SDK 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) 이상

<a name="bkmk_sample"></a>
## 기간 업무 템플릿에 예제 응용 프로그램 사용 ##

이 자습서의 예제 응용 프로그램 [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)은 Azure Active Directory 팀이 만들었으며, 새 기간 업무 응용 프로그램을 간편하게 만들 수 있는 템플릿으로 사용될 수 있습니다. 기본 제공 기능은 다음과 같습니다.

- [OpenID Connect](http://openid.net/connect/)를 사용하여 Azure Active Directory 인증
- `[Authorize]`의 표준 사용을 포함하여 응용 프로그램의 특정 작업에 대한 권한을 여러 역할에 부여할 수 있는 방법을 보여 주는 예제 `TaskTracker` 컨트롤러를 포함합니다. 
- 사용자 및 그룹을 즉시 할당할 수 있는 미리 정의된 역할이 잇는 다중 테넌트 응용 프로그램입니다. 

<a name="bkmk_run" />
## 예제 응용 프로그램 실행 ##

1.	[WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)에서 로컬 디렉터리에 예제 솔루션을 복제하거나 다운로드합니다.

2.	[단일 테넌트 앱으로 샘플을 실행하는 방법](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#how-to-run-the-sample-as-a-single-tenant-app)의 지침에 따라 Azure Active Directory 응용 프로그램 및 프로젝트를 설정합니다. 모든 지침을 따라 다중 테넌트에서 단일 테넌트로 응용 프로그램을 변환해야 합니다.

3.	방금 만든 Azure Active Directory 응용 프로그램에 대한 [Azure 포털](https://manage.windowsazure.com) 보기에서 **사용자** 탭을 클릭합니다. 그런 다음 원하는 역할에 원하는 사용자를 할당합니다.

	>[AZURE.NOTE]역할을 사용자 외에도 그룹에 할당하려는 경우 Azure Active Directory 테넌트를 [Azure Active Directory Premium](/pricing/details/active-directory/)으로 업그레이드해야 합니다. 응용 프로그램의 포털 UI에서 ** 사용자 및 그룹 탭 대신 **사용자** 탭이 표시되는 경우 Azure Active Directory 테넌트의 **라이선스** 탭으로 이동하여 Azure Active Directory Premium을 시도할 수 있습니다.

3.	응용 프로그램 구성을 마친 후에는 Visual Studio에서 `F5` 키를 눌러 ASP.NET 응용 프로그램을 실행합니다.

4.	응용 프로그램이 로드되면 **로그인**을 클릭하고 Azure 포털에서 관리자 역할이 있는 사용자로 로그인합니다.

5.	Azure Active Directory 응용 프로그램을 올바르게 구성하고 Web.config에서 해당 설정을 지정하면 로그인으로 리디렉션되어야 합니다. Azure 포털에서 Azure Active Directory 응용 프로그램을 만드는 데 사용된 계정이 Azure Active Directory 응용 프로그램의 기본 소유자이므로 이 계정을 사용하여 로그인하면 됩니다.
	
<a name="bkmk_deploy"></a>
## 앱 서비스 웹앱에 샘플 응용 프로그램 배포

여기에서는 Azure 앱 서비스에서 웹앱에 응용 프로그램을 게시합니다. [README.md](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims/blob/master/README.md)에 이미 앱 서비스 웹앱 배포에 대한 지침이 있지만 이러한 단계는 로컬 디버그 환경에 대한 구성을 취소하기도 합니다. 디버그 구성을 유지하면서 배포하는 방법에 대해 알아보겠습니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. **Microsoft Azure 웹앱**을 선택합니다.

3. Azure에 로그인하지 않은 경우 **계정 추가**를 클릭하고 Azure 구독에 대한 Microsoft 계정을 사용하여 로그인합니다.

4. 로그인되면 **새로 만들기**를 클릭하여 Azure에서 새 웹앱을 만듭니다.

5. 모든 필수 필드를 입력합니다. 이 응용 프로그램이 역할 매핑, 캐시된 토큰 및 모든 응용 프로그램 데이터를 저장하려면 데이터베이스에 연결해야 합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. **만들기**를 클릭합니다. 웹앱이 만들어지면 **웹 게시** 대화 상자가 열립니다.

7. **대상 URL**에서 **http**를 **https**로 변경합니다. 전체 URL을 텍스트 편집기에 복사합니다. 이 URL은 나중에 사용합니다. 그런 후 **다음**을 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. **조직 인증 사용** 확인란의 선택을 취소합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-enable-code-first-migrations.png)

8. **RoleClaimContext**를 확장하고 **Code First 마이그레이션 실행(응용 프로그램 시작 시 실행)**을 선택합니다. [Code First 마이그레이션](https://msdn.microsoft.com/data/jj591621.aspx)(영문)을 사용하면 나중에 추가 Code First 데이터 모델을 정의하는 경우 Azure에서 앱의 데이터베이스 스키마를 업데이트할 수 있습니다.

9. **게시**를 클릭하여 웹 게시를 진행하는 대신 **닫기**를 클릭합니다. **예**를 클릭하여 게시 프로필에 변경 사항을 저장합니다.

2. [Azure 관리 포털](https://manage.windowsazure.com)에서 Azure Active Directory 테넌트로 이동하여 **응용 프로그램** 탭을 클릭합니다.

2. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

2. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

3. **웹 응용 프로그램 및/또는 API**를 선택합니다.

4. 응용 프로그램의 이름을 지정하고 **다음**을 클릭합니다.

5. 앱 속성에서 **로그온 URL**을 이전에 저장된 웹앱 URL(예: `https://<site-name>.azurewebsites.net/`)로 설정하고 **앱 ID URI**를 `https://<aad-tenanet-name>/<app-name>`으로 설정합니다. 그런 후 **완료**를 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

2.	응용 프로그램을 만든 후 [응용 프로그램 역할 정의](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#step-2-define-your-application-roles)의 지침에서 했던 것과 동일한 방법으로 응용 프로그램 매니페스트를 업데이트합니다.

3.	방금 만든 Azure Active Directory 응용 프로그램에 대한 [Azure 포털](https://manage.windowsazure.com) 보기에서 **사용자** 탭을 클릭합니다. 그런 다음 원하는 역할에 원하는 사용자를 할당합니다.

6. **구성** 탭을 클릭합니다.

7. **키** 아래의 드롭다운에서 **1년**을 선택하여 새 키를 만듭니다.

8. **다른 응용 프로그램에 대한 권한** 아래의 **위임된 권한** 드롭다운에서 **Azure Active Directory** 항목에 대해 **로그인 및 사용자 프로필 읽기** 및 **디렉터리 데이터 읽기**를 선택합니다.

	> [AZURE.NOTE]여기에서 필요한 정확한 권한은 응용 프로그램의 원하는 기능에 따라 다릅니다. 일부 권한에는 **전역 관리자** 역할을 설정해야 하지만 이 자습서에는 **사용자** 역할만 필요합니다.

9.  **저장**을 클릭합니다.

10.  저장한 구성 페이지에서 나가기 전에 다음 정보를 텍스트 편집기에 복사합니다.

	-	클라이언트 ID
	-	키(페이지에서 나간 경우에는 키를 다시 볼 수 없음)

11. Visual Studio에서 프로젝트의 **Web.Release.config**를 엽니다. 다음 XML을 `<configuration>` 태그에 삽입하고 각 키 값을 새 Azure Active Directory 응용 프로그램에 대해 저장한 정보로 바꿉니다.
	<pre class="prettyprint">
&lt;appSettings>
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
&lt;/appSettings></pre>ida:PostLogoutRedirectUri 값은 슬래시("/")로 끝나야 합니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

2. **게시**를 클릭하여 Azure 앱 서비스 웹앱을 게시합니다.

작업을 마치면 Azure 관리 포털에 Azure Active Directory 응용 프로그램 두 개가 구성됩니다. 하나는 Visual Studio의 디버그 환경용이고, 나머지 하나는 Azure에 게시된 웹앱용입니다. 디버그하는 동안 Web.config의 응용 프로그램 설정을 통해 **디버그** 구성이 Azure Active Directory와 작동하도록 설정되며, 게시된 경우(기본적으로 **릴리스** 구성이 게시됨) 변환된 Web.config가 업로드되어 응용 프로그램 설정 변경 사항이 Web.Release.config에 포함됩니다.

게시된 웹앱을 디버거에 연결하려는 경우(즉, 게시된 웹앱에서 코드의 디버그 기호를 업로드해야 함) Web.Release.config의 Azure Active Directory 설정을 사용하는 고유한 사용자 지정 Web.config 변환(예: Web.AzureDebug.config)을 통해 Azure 디버깅을 위한 디버그 구성의 복제본을 만들 수 있습니다. 이렇게 하면 여러 환경에서 정적 구성을 유지 관리할 수 있습니다.

<a name="bkmk_crud"></a>
## 예제 응용 프로그램에 기간 업무 기능 추가

자습서의 이 부분에서는 예제 응용 프로그램을 기반으로 원하는 기간 업무 기능을 빌드하는 방법에 대해 알아봅니다. TaskTracker 컨트롤러와 유사하지만 표준 CRUD 스캐폴딩 및 디자인 패턴을 사용하는 간단한 CRUD 작업 항목 추적기를 만듭니다. 또한 포함된 Scripts\\AadPickerLibrary.js를 사용하여 Azure Active Directory Graph API의 데이터로 응용 프로그램을 보강합니다.

5.	Models 폴더에서 WorkItem.cs라는 새 [Code First](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) 모델을 만들고 코드를 다음 코드로 바꿉니다.

		using System.ComponentModel.DataAnnotations;
		
		namespace WebApp_RoleClaims_DotNet.Models
		{
		    public class WorkItem
		    {
		        [Key]
		        public int ItemID { get; set; }
		        public string AssignedToID { get; set; }
		        public string AssignedToName { get; set; }
		        public string Description { get; set; }
		        public WorkItemStatus Status { get; set; }
		    }
		
		    public enum WorkItemStatus
		    {
		        Open, 
		        Investigating, 
		        Resolved, 
		        Closed
		    }
		}

6.	DAL\\RoleClaimContext.cs를 열고 강조 표시된 코드를 추가합니다.
	<pre class="prettyprint">
public class RoleClaimContext : DbContext
{
    public RoleClaimContext() : base("RoleClaimContext") { }

    public DbSet&lt;Task> Tasks { get; set; }
    <mark>public DbSet&lt;WorkItem> WorkItems { get; set; }</mark>
    public DbSet&lt;TokenCacheEntry> TokenCacheEntries { get; set; }
}</pre>

7.	프로젝트를 빌드하여 새 모델이 Visual Studio에서 스캐폴딩 논리에 액세스할 수 있도록 합니다.

8.	스캐폴드된 새 항목 `WorkItemsController`를 Controllers 폴더에 추가합니다. 이렇게 하려면 **컨트롤러**를 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 후 **새 스캐폴드된 항목**을 선택합니다.

9.	**뷰가 포함된 MVC 5 컨트롤러, Entity Framework 사용**을 선택하고 **추가**를 클릭합니다.

10.	방금 만든 모델을 선택하고 **추가**를 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Controllers\\WorkItemsController.cs를 엽니다.

11. 강조 표시된 [Authorize] 장식을 아래의 각 작업에 추가합니다.
	<pre class="prettyprint">
...

<mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
public class WorkItemsController : Controller
{
	...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public ActionResult Create()
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Edit(int? id)
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
    ...

    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
    public async Task&lt;ActionResult> Delete(int? id)
    ...

    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
    public async Task&lt;ActionResult> DeleteConfirmed(int id)
    ...
}</pre>Azure 포털 UI에서 역할 매핑에 주의했기 때문에 각 작업에서 올바른 역할에 권한을 부여하는지만 확인하면 됩니다.

	> [AZURE.NOTE]일부 작업에서 <code>[ValidateAntiForgeryToken]</code> 장식이 표시될 수 있습니다. [MVC 4, AntiForgeryToken 및 클레임](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/)의 [Brock Allen](https://twitter.com/BrockLAllen)에 설명된 동작으로 인해 HTTP POST가 위조 방지 토큰의 유효성을 검사하지 못할 수 있습니다. 이유: + Azure Active Directory가 위조 방지 토큰에 기본적으로 필요한 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider를 전송하지 않습니다. + Azure Active Directory가 AD FS와 동기화된 디렉터리인 경우 기본적으로 AD FS 트러스트는 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider 클레임을 전송하지 않습니다. 이 클레임을 전송하도록 AD FS를 수동으로 구성할 수 있습니다. 다음 단계에서 이 문제를 해결할 수 있습니다.

12.  App\_Start\\Startup.Auth.cs에서 `ConfigureAuth` 메서드에 다음 코드 줄을 추가합니다. 해결할 각 이름 지정 해결 방법 오류를 마우스 오른쪽 단추로 클릭합니다.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies`는 Azure Active Directory에서 제공하는 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 클레임을 지정합니다. 이제 인증 부분을 해결했으므로(오래 걸리지는 않았음) 작업의 실제 기능에 집중할 수 있습니다.

13.	Create() 및 Edit()에서 다음 코드를 추가하여 나중에 JavaScript에서 일부 변수를 사용할 수 있도록 합니다. 해결할 각 이름 지정 해결 방법 오류를 마우스 오른쪽 단추로 클릭합니다.

        ViewData["token"] = AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
        ViewData["tenant"] = ConfigHelper.Tenant;

13.	`AcquireToken()` 메서드는 아직 정의되지 않았으므로 이제 `WorkItemsController` 클래스에서 정의합니다. 해결할 각 이름 지정 해결 방법 오류를 마우스 오른쪽 단추로 클릭합니다.

        static string AcquireToken(string userObjectId)
        {
            ClientCredential cred = new ClientCredential(ConfigHelper.ClientId, ConfigHelper.AppKey);
            Claim tenantIdClaim = ClaimsPrincipal.Current.FindFirst(Globals.TenantIdClaimType);
            AuthenticationContext authContext = new AuthenticationContext(String.Format(CultureInfo.InvariantCulture, ConfigHelper.AadInstance, tenantIdClaim.Value), new TokenDbCache(userObjectId));
            AuthenticationResult result = authContext.AcquireTokenSilent(ConfigHelper.GraphResourceId, cred, new UserIdentifier(userObjectId, UserIdentifierType.UniqueId));
            return result.AccessToken;
        }
		
14.	Views\\WorkItems\\Create.cshtml(자동으로 스캐폴드된 항목)에서 `Html.BeginForm` 도우미 메서드를 찾아 다음과 같이 수정합니다.
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
{
    @Html.AntiForgeryToken()

    &lt;div class="form-horizontal">
        &lt;h4>WorkItem&lt;/h4>
        &lt;hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })

        &lt;div class="form-group">
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type="hidden"</mark> } })
                @Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
                @Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            &lt;div class="col-md-offset-2 col-md-10">
                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> />
            &lt;/div>
        &lt;/div>
    &lt;/div>

    <mark>&lt;script>
            // People/Group Picker Code
            var maxResultsPerPage = 14;
            var input = document.getElementById("AssignedToName");
            var token = "@ViewData["token"]";
            var tenant = "@ViewData["tenant"]";

            var picker = new AadPicker(maxResultsPerPage, input, token, tenant);

            // Submit the selected user/group to be asssigned.
            $("#submit-button").click({ picker: picker }, function () {
                if (!picker.Selected())
                    return;
                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
            });
    &lt;/script></mark>

}</pre>스크립트에서 AadPicker 개체는 [Azure Active Directory Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)를 호출하여 입력과 일치하는 사용자 및 그룹을 검색합니다.

15. [패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)을 열고**Enable-Migrations –EnableAutomaticMigrations**를 실행합니다. 앱을 Azure에 게시했을 때 선택한 옵션과 유사하게 이 명령을 사용하면 Visual Studio에서 디버깅하는 경우 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)에서 앱의 데이터베이스 스키마를 업데이트할 수 있습니다.

15. 이제 응용 프로그램을 Visual Studio 디버거에서 실행하거나 앱 서비스 웹앱에 다시 게시합니다. 응용 프로그램 소유자로 로그인하여 `https://<webappname>.azurewebsites.net/WorkItems/Create`로 이동합니다. 이제 드롭다운 목록에서 Azure Active Directory 사용자 또는 그룹을 선택하거나 목록을 필터링하도록 입력할 수 있습니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. 양식의 나머지 부분을 입력하고 **만들기**를 선택합니다. ~/WorkItems/Index 페이지에 새로 만든 작업 항목이 표시됩니다. 또한 아래의 스크린샷을 보면 Views\\WorkItems\\Index.cshtml에서 `AssignedToID` 열이 제거된 것을 알 수 있습니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	이제 이와 유사하게 **편집** 보기를 변경합니다. Views\\WorkItems\\Edit.cshtml에서 이전 단계의 Views\\WorkItems\\Create.cshtml과 동일한 `Html.BeginForm` 도우미 메서드를 변경합니다(위의 강조 표시된 코드에서 "Create"를 "Edit"로 바꿈).

이것으로 끝입니다.

이제 WorkItems 컨트롤러에서 여러 작업에 대한 권한 부여 및 기간 업무 기능을 구성했으므로 여러 응용 프로그램 역할의 사용자로 로그인하여 응용 프로그램 응답 방법을 볼 수 있습니다.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## 추가 리소스

- [SSL 및 Authorize 특성을 사용하여 응용 프로그램 보호](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Azure 앱 서비스에서 인증을 위해 Active Directory 사용](web-sites-authentication-authorization.md)
- [AD FS 인증을 통해 Azure 앱 서비스에서 .NET MVC 웹앱 만들기](web-sites-dotnet-lob-application-adfs.md)
- [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/AzureADSamples)
- [Vittorio Bertocci의 블로그](http://blogs.msdn.com/b/vbertocci/)
- [WIF에서 Katana로 VS2013 웹 프로젝트 마이그레이션](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Azure의 새로운 하이브리드 연결](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Active Directory와 Azure Active Directory 간의 유사점](http://technet.microsoft.com/library/dn518177.aspx)
- [SSO(Single Sign-On)으로 디렉터리 동기화 시나리오](http://technet.microsoft.com/library/dn441213.aspx)
- [Azure Active Directory 지원 토큰 및 클레임 유형](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!---HONumber=Oct15_HO4-->