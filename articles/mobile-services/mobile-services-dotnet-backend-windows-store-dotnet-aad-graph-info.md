<properties 
	pageTitle="Azure Active Directory 그래프 정보 액세스(Windows 스토어) | Microsoft Azure" 
	description="Windows 스토어 응용 프로그램에서 Graph API를 사용하여 Azure Active Directory 정보에 액세스하는 방법에 대해 알아봅니다." 
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

# Azure Active Directory 그래프 정보 액세스



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]

##개요

모바일 서비스에서 제공되는 다른 ID 공급자와 마찬가지로 AAD(Azure Active Directory) 공급자에서 도 디렉토리에 대한 프로그래밍 방식의 액세스를 위해 사용할 수 있는 리치 그래프 API가 지원됩니다. 이 자습서에서는 [그래프 REST API]를 사용하여 디렉토리에서 검색하는 추가 사용자 정보를 반환하는 인증된 사용자의 앱 환경을 개인 설정하기 위해 ToDoList 앱을 업데이트합니다.

Azure AD Graph API에 대한 자세한 내용은 [Azure Active Directory 그래프 팀 블로그]를 참조하세요.


>[AZURE.NOTE]이 자습서에서는 Azure Active Directory에서 인증에 대한 다양한 지식 정보를 제공합니다. 이 자습서 이전에 Azure Active Directory 인증 공급자를 사용하여 [앱에 인증 추가] 자습서를 완료해야 합니다. 이 자습서에서는 [앱에 인증 추가] 자습서에서 사용된 TodoItem 응용 프로그램을 계속 업데이트합니다.




##필수 조건 

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

+ [앱에 인증 추가]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [사용자 지정 API 자습서l]<br/>사용자 지정 API를 호출하는 방법을 보여 줍니다.



## <a name="generate-key"></a>AAD에서 앱 등록을 위한 액세스 키 생성


[앱에 인증 추가] 자습서에서는 [Azure Active Directory 로그인 사용 등록] 단계를 완료할 때 통합 응용 프로그램에 대한 등록을 만들었습니다. 이 섹션에서는 해당 통합 응용 프로그램의 클라이언트 ID로 디렉터리 정보를 읽을 때 사용할 키를 생성합니다.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>GetUserInfo 사용자 지정 API 만들기

이 섹션에서는 AAD에서 사용자에 대한 추가 정보를 검색하기 위해 Azure AD Graph API를 사용할 GetUserInfo 사용자 지정 API를 만듭니다.

모바일 서비스에서 사용자 지정 API를 사용해본 경험이 없으면 이 섹션을 완료하기 전에 [사용자 지정 API 자습서]를 참조합니다.

1. Visual Studio에서 모바일 서비스 .NET 백 엔드 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.
2. NuGet 패키지 관리자 대화 상자에서 검색 조건에 **ADAL**을 입력하여 모바일 서비스에 대한 **Active Directory 인증 라이브러리**를 찾아서 설치합니다. 이 자습서는 ADAL 패키지의 3.3.205061641-alpha(시험판) 버전으로 가장 최근에 테스트 했습니다.


3. Visual Studio에서 모바일 서비스 프로젝트에 대해 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭하여 `GetUserInfoController`(이)라는 새로운 **Microsoft Azure 모바일 서비스 사용자 지정 컨트롤러**를 추가합니다. 클라이언트는 Active Directory에서 사용자 정보를 가져오기 위해 이 API를 호출합니다.

4. 새로운 GetUserInfoController.cs 파일에서 다음 `using` 문을 추가합니다.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. 새 GetUserInfoController.cs 파일에 다음 `UserInfo` 클래스를 추가하여 AAD에서 수집하려는 정보를 포함합니다.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
	
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. GetUserInfoController.cs에서 다음 멤버 변수를 `GetUserInfoController` 클래스에 추가합니다.

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. GetUserInfoController.cs에서 다음 `GetAADToken` 메서드를 클래스에 추가합니다.

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    이 메서드는 [Azure 관리 포털]의 모바일 서비스에서 구성한 앱 설정을 사용하여 Active Directory에 액세스하기 위한 토큰을 가져옵니다.

7. GetUserInfoController.cs에서 다음 `GetAADUser` 메서드를 클래스에 추가합니다.

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    이 메서드는 권한이 부여된 사용자에 대한 Active Directory 개체 ID를 가져온 후 Graph REST API를 사용하여 Active Directory에서 사용자 정보를 가져옵니다.


8. GetUserInfoController.cs에서 `Get` 메서드를 다음 메서드로 바꿉니다. 이 메서드는 Graph REST API를 사용하여 Azure Active Directory에서 사용자 엔터티를 반환하며 API를 호출하는 데 인증된 사용자가 필요합니다.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. 변경 내용을 저장하고 서비스를 빌드하여 구문 오류가 없는지 확인합니다.
10. 모바일 서비스 프로젝트를 사용자의 Azure 계정에 게시합니다. 


## <a name="update-app"></a>GetUserInfo를 사용하도록 앱 업데이트

이 섹션에서는 [앱에 인증 추가] 자습서에서 구현한 `AuthenticateAsync` 메서드를 업데이트하여 사용자 지정 API를 호출하고 AAD에서 사용자에 대한 추가 정보를 반환합니다.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>앱 테스트

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>다음 단계

다음 자습서인 [모바일 서비스의 AAD에서 역할 기반 액세스 제어]에서는 액세스를 허용하기 전에 AAD(Azure Active Directory)에서 역할 기반 액세스 제어를 사용하여 그룹 구성원을 확인합니다.



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[앱에 인증 추가]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure 관리 포털]: https://manage.windowsazure.com/
[그래프 REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[사용자 지정 API 자습서]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[사용자 지정 API 자습서l]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Azure Active Directory 로그인 사용 등록]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Active Directory 그래프 팀 블로그]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[모바일 서비스의 AAD에서 역할 기반 액세스 제어]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md
 

<!---HONumber=August15_HO7-->