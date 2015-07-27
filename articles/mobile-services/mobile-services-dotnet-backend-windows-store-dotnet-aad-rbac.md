<properties 
	pageTitle="모바일 서비스 및 Azure Active Directory의 역할 기반 액세스 제어(Windows 스토어) | 모바일 개발자 센터" 
	description="Windows 스토어 응용 프로그램에서 Azure Active Directory 역할을 기반으로 액세스를 제어하는 방법을 알아봅니다." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="wesmc"/>

# 모바일 서비스 및 Azure Active Directory의 역할 기반 액세스 제어

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##개요

RBAC(역할 기반 액세스 제어)는 사용자가 저장할 수 있는 역할에 사용 권한을 할당하는 방법입니다. 사용자의 어느 특정 클래스가 수행할 수 있고 수행할 수 없는지에 대한 경계를 정의합니다. 이 자습서에서는 Azure 모바일 서비스에 기본 RBAC를 추가하는 방법을 안내합니다.

이 자습서에서는 AAD(Azure Active Directory)에 정의된 Sales 그룹에 대한 각 사용자의 멤버 자격을 확인하면서 역할 기반 액세스 제어에 대해 설명합니다. 액세스 확인은 Azure Active Directory에 대한 [Graph REST API]를 사용하여 .NET 모바일 서비스 백 엔드를 통해 수행됩니다. Sales 그룹에 속하는 사용자만 데이터 쿼리가 허용됩니다.


>[AZURE.NOTE]이 자습서에서는 권한 부여 방식을 포함하여 인증에 대한 다양한 지식 정보를 제공합니다. 이 자습서 이전에 Azure Active Directory 인증 공급자를 사용하여 [앱에 인증 추가] 자습서를 먼저 완료해야 합니다. 이 자습서에서는 [앱에 인증 추가] 자습서에서 사용된 TodoItem 응용 프로그램을 계속 업데이트합니다.

##필수 조건

이 자습서를 사용하려면 다음이 필요합니다.

* Windows 8.1에서 실행 중인 Visual Studio 2013
* Azure Active Directory 인증 공급자를 사용하여 [앱에 인증 추가] 자습서 완료

 


##통합 응용 프로그램에 대한 키 생성


[앱에 인증 추가] 자습서에서는 [Azure Active Directory 로그인 사용 등록] 단계를 완료할 때 통합 응용 프로그램에 대한 등록을 만들었습니다. 이 섹션에서는 해당 통합 응용 프로그램의 클라이언트 ID로 디렉터리 정보를 읽을 때 사용할 키를 생성합니다.

[Azure Active Directory 그래프 정보 액세스] 자습서를 진행할 때 이미 이 단계를 완료했으며 이 섹션은 건너뛰어도 됩니다.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##멤버 자격이 포함된 Sales 그룹 만들기

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]



##모바일 서비스에서 사용자 지정 권한 부여 특성 만들기 

이 섹션에서는 모바일 서비스 작업에서 액세스 확인을 수행하는 데 사용할 수 있는 새로운 사용자 지정 권한 부여 특성을 만듭니다. 이 특성은 여기에 전달된 역할 이름을 기준으로 Active Directory 그룹을 조회합니다. 그런 후 해당 그룹의 멤버 자격을 기준으로 액세스 확인을 수행합니다.

1. Visual Studio에서 모바일 서비스 .NET 백 엔드 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.

2. NuGet 패키지 관리자 대화 상자에서 검색 조건에 **ADAL**을 입력하여 모바일 서비스에 대한 **Active Directory 인증 라이브러리**를 찾아서 설치합니다. 이 자습서는 ADAL 패키지의 3.3.205061641-alpha(시험판) 버전으로 가장 최근에 테스트 했습니다.

3. Visual Studio에서 모바일 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **새 폴더**를 클릭합니다. 새 폴더 이름을 **Utilities**로 지정합니다.

4. Visual Studio에서 새로운 **Utilities** 폴더를 마우스 오른쪽 단추로 클릭하고, **AuthorizeAadRole.cs**라는 새 클래스 파일을 추가합니다.

    ![][0]

5. AuthorizeAadRole.cs 파일에서 파일 맨 위에 다음 `using` 문을 추가합니다.

		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using System.Web.Http.Controllers;
		using System.Web.Http.Filters;
		using Newtonsoft.Json;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.IO;

6. AuthorizeAadRole.cs에서 다음 열거 유형을 Utilities 네임스페이스에 추가합니다. 이 자습서에서는 **Sales** 역할에 대해서만 다룹니다. 다른 항목은 사용 가능한 그룹 예일 뿐입니다.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. AuthorizeAadRole.cs에서 다음 `AuthorizeAadRole` 클래스 정의를 Utilities 네임스페이스에 추가합니다.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
	        private ApiServices services = null;
	
	        // Constants used with ADAL and the Graph REST API for AAD
	        private const string AadInstance = "https://login.windows.net/{0}";
	        private const string GraphResourceId = "https://graph.windows.net/";
	        private const string APIVersion = "?api-version=2013-04-05";
	
	        // App settings pulled from the Mobile Service
	        private string tenantdomain;
	        private string clientid;
	        private string clientkey;
	        private Dictionary<int, string> groupIds = new Dictionary<int, string>();
	
	        private string token = null;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

	        // private class used to serialize the Graph REST API web response
	        private class MembershipResponse
	        {
	            public bool value;
	        }

            public AadRoles Role { get; private set; }

            // Generate a local dictionary for the role group ids configured as 
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }

            // Use ADAL and the authentication app settings from the Mobile Service to 
            // get an AAD access token
            private string GetAADToken()
            {
            }

            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }

            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }



8. AuthorizeAadRole.cs에서 `AuthorizeAadRole` 클래스에 있는 `InitGroupIds` 메서드를 다음과 같이 업데이트합니다. 이 메서드는 각 역할에 그룹 ID의 사전 매핑을 만듭니다.

        private void InitGroupIds()
        {
            string groupId;

            if (services == null)
                return;

            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }


9. AuthorizeAadRole.cs에서 `AuthorizeAadRole` 클래스에 있는 `GetAADToken` 메서드를 업데이트합니다. 이 메서드는 ADAL에서 AAD에 액세스 토큰을 가져오는 데 모바일 서비스에 저장된 앱 설정을 사용합니다.

    >[AZURE.NOTE].NET용 ADAL은 Active Directory에 대한 추가 네트워크 트래픽을 줄이기 위해 기본적으로는 메모리 내 토큰 캐시를 포함합니다. 하지만 고유한 캐시 구현을 작성하거나 완전히 캐싱을 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [.NET용 ADAL]을 참조하세요.

        // Use ADAL and the authentication app settings from the Mobile Service to get an AAD access token
        private async Task<string> GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);

            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);
            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

10. AuthorizeAadRole.cs에서 `AuthorizeAadRole` 클래스에 있는 `CheckMembership` 메서드를 업데이트합니다. 이 메서드는 사용자의 개체 ID를 받습니다. 그런 다음 AAD Graph REST API를 사용하여 `AuthorizeAadRole` 클래스에서 선택한 역할과 연관되는 그룹에 대한 멤버 ID인지 보기 위해 개체 ID를 확인합니다.

        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format(""groupId":"{0}","memberId":"{1}"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }

                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }

            return membership;
        }


11. AuthorizeAadRole.cs에서 `AuthorizeAadRole` 클래스에 있는 `OnAuthorization` 메서드를 다음 코드로 업데이트합니다. 이 코드는 모바일 서비스로 호출되는 사용자가 AAD로 인증될 것을 기대합니다. 그런 다음 사용자의 AAD 개체 ID를 가져오고 역할에 해당하는 Active Directory 그룹의 멤버 자격을 확인합니다.

    >[AZURE.NOTE]Active Directory 그룹을 이름별로 조회할 수 있습니다. 하지만 많은 경우에 모바일 서비스 앱 설정으로 그룹 ID를 저장하는 것이 더 낫습니다. 그룹 이름은 변경되기 쉽지만 ID는 동일하게 유지되기 때문입니다.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            services = new ApiServices(actionContext.ControllerContext.Configuration);

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
            }

            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();

                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");

                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }

12. 변경 사항을 AuthorizeAadRole.cs에 저장합니다.

##역할 기반 액세스 확인을 데이터베이스 작업에 추가

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


##클라이언트의 액세스 테스트

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[앱에 인증 추가]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Azure Active Directory 로그인 사용 등록]: mobile-services-how-to-register-active-directory-authentication.md
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Azure Active Directory 그래프 정보 액세스]: mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info.md
[.NET용 ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx

<!---HONumber=July15_HO3-->