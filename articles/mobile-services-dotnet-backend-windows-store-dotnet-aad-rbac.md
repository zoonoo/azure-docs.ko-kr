<properties linkid="develop-mobile-tutorials-dotnet-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc"></tags>

# 모바일 서비스 및 Azure Active Directory의 역할 기반 액세스 제어

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="Windows 스토어 C#" class="current">Windows 스토어 C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | 
    <a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="JavaScript 백 엔드">JavaScript 백 엔드</a>
</div>


RBAC(역할 기반 액세스 제어)는 사용자가 가질 수 있는 역할에 권한을 할당하여 사용자의 특정 클래스가 수행할 수 있는 작업과 수행할 수 없는 작업에 대한 경계를 효율적으로 정의하는 방식입니다. 이 자습서에서는 Azure 모바일 서비스에 기본 RBAC를 추가하는 방법을 안내합니다.

이 자습서에서는 AAD(Azure Active Directory)에 정의된 Sales 그룹에 대한 각 사용자의 멤버 자격을 확인하면서 역할 기반 액세스 제어에 대해 설명합니다. 액세스 확인은 Azure Active Directory에 대한 [그래프 클라이언트 라이브러리][그래프 클라이언트 라이브러리]를 사용해서 .NET 모바일 서비스 백 엔드를 통해 수행됩니다. Sales 그룹에 속하는 사용자만 데이터 쿼리가 허용됩니다.


> [WACOM.NOTE] 이 자습서에서는 권한 부여 방식을 포함하여 인증에 대한 다양한 지식 정보를 제공합니다. 이 자습서 이전에 Azure Active Directory 인증 공급자를 사용해서 [인증 시작][인증 시작] 자습서를 완료해야 합니다. 이 자습서에서는 [인증 시작][인증 시작] 자습서에서 사용된 TodoItem 응용 프로그램을 계속 업데이트합니다.

이 자습서에서는 다음 단계를 단계별로 안내합니다.

1. [멤버 자격이 포함된 Sales 그룹 만들기][멤버 자격이 포함된 Sales 그룹 만들기]
2. [통합 응용 프로그램에 대한 키 생성][통합 응용 프로그램에 대한 키 생성]
3. [사용자 지정 권한 부여 특성 만들기][사용자 지정 권한 부여 특성 만들기]
4. [역할 기반 액세스 확인을 데이터베이스 작업에 추가][역할 기반 액세스 확인을 데이터베이스 작업에 추가]
5. [클라이언트 액세스 테스트][클라이언트 액세스 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

* Windows 8.1에서 실행 중인 Visual Studio 2013
* Azure Active Directory 인증 공급자를 사용해서 [인증 시작][인증 시작] 자습서 완료
* 서버 스크립트를 저장하기 위해 Git 저장소를 사용하는 데 익숙해지도록 [서버 스크립트 저장][서버 스크립트 저장] 자습서 완료



## <a name="create-group"></a> 멤버 자격이 포함된 Sales 그룹 만들기

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group][mobile-services-aad-rbac-create-sales-group]]


## <a name="generate-key"></a> 통합 응용 프로그램에 대한 키 생성


[인증 시작][인증 시작] 자습서에서는 [Azure Active Directory 로그인 사용 등록][Azure Active Directory 로그인 사용 등록] 단계를 완료할 때 통합 응용 프로그램에 대한 등록을 만들었습니다. 이 섹션에서는 해당 통합 응용 프로그램의 클라이언트 ID로 디렉터리 정보를 읽을 때 사용할 키를 생성합니다.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]



## <a name="create-custom-authorization-attribute"></a> 모바일 서비스에서 사용자 지정 권한 부여 특성 만들기

이 섹션에서는 모바일 서비스 작업에서 액세스 확인을 수행하는 데 사용할 수 있는 새로운 사용자 지정 권한 부여 특성을 만듭니다. 이 특성은 여기에 전달된 역할 이름을 기준으로 Active Directory 그룹을 조회합니다. 그런 후 해당 그룹의 멤버 자격을 기준으로 액세스 확인을 수행합니다.

1. Visual Studio에서 모바일 서비스 .NET 백 엔드 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.

2. NuGet 패키지 관리자 대화 상자에서 검색 조건에 **ADAL**을 입력하여 모바일 서비스에 대한 **Active Directory 인증 라이브러리**를 찾아서 설치합니다.

3. NuGet 패키지 관리자에서도 모바일 서비스에 대해 **Microsoft Azure Active Directory Graph 클라이언트 라이브러리**를 설치합니다.


4. Visual Studio에서 모바일 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **새 폴더**를 클릭합니다. 새 폴더 이름을 **Utilities**로 지정합니다.

5. Visual Studio에서 새로운 **Utilities** 폴더를 마우스 오른쪽 단추로 클릭하고, **AuthorizeAadRole.cs**라는 새 클래스 파일을 추가합니다.

    ![][]

6.  AuthorizeAadRole.cs 파일에서 파일 맨 위에 다음 `using` 문을 추가합니다.

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7. AuthorizeAadRole.cs에서 다음 열거 유형을 Utilities 네임스페이스에 추가합니다. 이 자습서에서는 **Sales** 역할에 대해서만 다룹니다. 다른 항목은 사용 가능한 그룹 예일 뿐입니다.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8. AuthorizeAadRole.cs에서 다음 `AuthorizeAadRole` 클래스 정의를 Utilities 네임스페이스에 추가합니다.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9. AuthorizeAadRole.cs에서 다음 `GetAADToken` 메서드를 `AuthorizeAadRole` 클래스에 추가합니다.

    >[WACOM.NOTE] 각 액세스 확인 시 새 토큰을 만드는 대신 토큰을 캐시에 저장해야 합니다. 그런 후 [그래프 클라이언트 라이브러리][그래프 클라이언트 라이브러리]에 설명된 것처럼 토큰을 사용하려고 시도할 때 캐시를 새로 고쳐서 AccessTokenExpiredException을 throw합니다. 단순성을 위해 이에 대해서는 아래 코드에 설명되지 않지만, Active Directory에 대한 네트워크 트래픽을 추가로 줄여줍니다.

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;
            
            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireToken(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;

            return token;
        }

10. AuthorizeAadRole.cs에서 `AuthorizeAadRole` 클래스에 있는 `OnAuthorization` 메서드를 다음 코드로 업데이트합니다. 이 코드는 [그래프 클라이언트 라이브러리][그래프 클라이언트 라이브러리]를 사용해서 역할에 해당하는 Active Directory 그룹을 조회합니다. 그런 후 해당 그룹에서 사용자의 멤버 자격을 확인하여 사용자에게 권한을 부여합니다.

    >[WACOM.NOTE] 이 코드는 Active Directory 그룹을 이름별로 조회합니다. 많은 경우, 그룹 ID를 모바일 서비스 앱 설정으로 저장하는 것이 더 낫습니다. 그룹 이름이 변경될 수도 있지만 ID는 동일하게 유지되기 때문입니다. 하지만 그룹 이름이 변경될 경우에는 최소한 일반적으로 역할 범위가 변경되므로 모바일 서비스 코드를 업데이트해야 할 수도 있습니다.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
			// through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
						"User is not logged in or not a member of the required group");
            }
        }

11. 변경 사항을 AuthorizeAadRole.cs에 저장합니다.

## <a name="add-access-checking"></a> 역할 기반 액세스 확인을 데이터베이스 작업에 추가

1. Visual Studio에서 모바일 서비스 프로젝트 아래의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 엽니다.

2. TodoItemController.cs에서 사용자 지정 권한 부여 특성을 포함하는 유틸리티 네임스페이스에 대해 `using` 문을 추가합니다.

        using todolistService.Utilities;

3. TodoItemController.cs에서는 원하는 액세스 확인 방법에 따라 특성을 컨트롤러 클래스 또는 개별 메서드에 추가할 수 있습니다. 모든 컨트롤러 작업이 동일한 역할을 기반으로 액세스를 확인하도록 하려면 특성을 클래스에 추가합니다. 이 자습서를 테스트하려면 다음과 같이 클래스에 특성을 추가합니다.

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    삽입, 업데이트 및 삭제 작업에 대해서만 액세스 확인이 필요한 경우 다음과 같이 해당 메서드에서만 특성을 설정합니다.

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. TodoItemController.cs를 저장하고 모바일 서비스를 작성하여 구문 오류가 없는지 확인합니다.
5. 모바일 서비스를 Azure 계정에 게시합니다.


## <a name="test-client"></a> 클라이언트의 액세스 테스트

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app][mobile-services-aad-rbac-test-app]]




<!-- Anchors. --> 
<!-- Images --> 
<!-- URLs. -->

[Windows 스토어 C\#]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/ "Windows 스토어 C#"
[.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/ ".NET 백 엔드"
[JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/ "JavaScript 백 엔드"
[그래프 클라이언트 라이브러리]: http://go.microsoft.com/fwlink/?LinkId=510536
[인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[멤버 자격이 포함된 Sales 그룹 만들기]: #create-group
[통합 응용 프로그램에 대한 키 생성]: #generate-key
[사용자 지정 권한 부여 특성 만들기]: #create-custom-authorization-attribute
[역할 기반 액세스 확인을 데이터베이스 작업에 추가]: #add-access-checking
[클라이언트 액세스 테스트]: #test-client
[서버 스크립트 저장]: /ko-kr/documentation/articles/mobile-services-store-scripts-source-control/
[mobile-services-aad-rbac-create-sales-group]: ../includes/mobile-services-aad-rbac-create-sales-group.md
[Azure Active Directory 로그인 사용 등록]: /ko-kr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
[]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png
[mobile-services-aad-rbac-test-app]: ../includes/mobile-services-aad-rbac-test-app.md
