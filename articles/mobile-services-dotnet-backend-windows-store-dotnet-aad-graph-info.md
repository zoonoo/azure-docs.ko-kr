<properties linkid="develop-mobile-tutorials-dotnet-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# Azure Active Directory 그래프 정보 액세스

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/" title="Windows 스토어 C#" class="current">Windows 스토어 C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> |
    <a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript 백 엔드">JavaScript 백 엔드</a>
</div>


모바일 서비스에서 제공되는 다른 ID 공급자와 마찬가지로 AAD(Azure Active Directory) 공급자에서도 디렉터리에 대한 프로그래밍 방식의 액세스를 위해 사용할 수 있는 리치 [그래프 클라이언트 라이브러리][그래프 클라이언트 라이브러리]가 지원됩니다. 이 자습서에서는 [그래프 클라이언트 라이브러리][그래프 클라이언트 라이브러리]를 사용해서 디렉터리에서 검색하는 추가 사용자 정보를 기반으로 인증된 사용자의 앱 환경을 개인 설정하기 위해 ToDoList 앱을 업데이트합니다.

> [WACOM.NOTE] 이 자습서에서는 Azure Active Directory에서 인증에 대한 다양한 지식 정보를 제공합니다. 이 자습서 이전에 Azure Active Directory 인증 공급자를 사용해서 [인증 시작][인증 시작]을 완료해야 합니다. 이 자습서에서는 [인증 시작][인증 시작] 자습서에서 사용된 TodoItem 응용 프로그램을 계속 업데이트합니다.



이 자습서에서는 다음 단계를 단계별로 안내합니다.


1. [AAD에서 앱 등록을 위한 액세스 키 생성][AAD에서 앱 등록을 위한 액세스 키 생성]
2. [GetUserInfo 사용자 지정 API 만들기][GetUserInfo 사용자 지정 API 만들기]
3. [사용자 지정 API를 사용하도록 앱 업데이트][사용자 지정 API를 사용하도록 앱 업데이트]
4. [앱 테스트][앱 테스트]

## 필수 조건

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

+ [인증 시작][인증 시작]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [사용자 지정 API 자습서][사용자 지정 API 자습서]<br/>사용자 지정 API를 호출하는 방법을 보여줍니다.



## <a name="generate-key"></a> AAD에서 앱 등록을 위한 액세스 키 생성


[인증 시작][인증 시작] 자습서에서는 [Azure Active Directory 로그인 사용 등록][Azure Active Directory 로그인 사용 등록] 단계를 완료할 때 통합 응용 프로그램에 대한 등록을 만들었습니다. 이 섹션에서는 해당 통합 응용 프로그램의 클라이언트 ID로 디렉터리 정보를 읽을 때 사용할 키를 생성합니다.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-api"></a> GetUserInfo 사용자 지정 API 만들기

이 섹션에서는 AAD에서 사용자에 대한 추가 정보를 검색하기 위해 [그래프 클라이언트 라이브러리][그래프 클라이언트 라이브러리]를 사용할 GetUserInfo 사용자 지정 API를 만듭니다.


모바일 서비스에서 사용자 지정 API를 사용해본 경험이 없으면 이 섹션을 완료하기 전에 [사용자 지정 API 자습서][사용자 지정 API 자습서]를 참조합니다.

1. Visual Studio에서 모바일 서비스 .NET 백 엔드 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.
2. NuGet 패키지 관리자 대화 상자에서 검색 조건에 **ADAL**을 입력하여 모바일 서비스에 대한 **Active Directory 인증 라이브러리**를 찾아서 설치합니다.
3. NuGet 패키지 관리자에서도 모바일 서비스에 대해 **Microsoft Azure Active Directory Graph 클라이언트 라이브러리**를 설치합니다.

4. Visual Studio에서 모바일 서비스 프로젝트에 대해 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭하여 `GetUserInfoController`라는 새로운 **Microsoft Azure 모바일 서비스 사용자 지정 컨트롤러**를 추가합니다. 클라이언트는 Active Directory에서 사용자 정보를 가져오기 위해 이 API를 호출합니다.

5.  새로운 GetUserInfoController.cs 파일에서 다음 `using` 문을 추가합니다.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. GetUserInfoController.cs에서 클래스에 다음 `GetAADToken` 메서드를 추가합니다.

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
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

    이 메서드는 [Azure 관리 포털][Azure 관리 포털]의 모바일 서비스에서 구성한 앱 설정을 사용하여 Active Directory에 액세스하기 위한 토큰을 가져옵니다.

7. GetUserInfoController.cs에서 다음 `GetAADUser` 메서드를 클래스에 추가합니다.

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    이 메서드는 권한이 부여된 사용자에 대한 Active Directory 개체 ID를 가져온 후 그래프 클라이언트 라이브러리를 사용해서 Active Directory에서 사용자 정보를 가져옵니다.

8. GetUserInfoController.cs에서 `Get` 메서드를 다음 메서드로 바꿉니다. 이 메서드는 그래프 클라이언트 라이브러리의 `User` 개체를 반환하고 권한 있는 사용자에게 API를 호출하라고 요구합니다.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. 변경 내용을 저장하고 서비스를 빌드하여 구문 오류가 없는지 확인합니다.
10. 모바일 서비스 프로젝트를 사용자의 Azure 계정에 게시합니다.



## <a name="update-app"></a> GetUserInfo를 사용하도록 앱 업데이트

이 섹션에서는 [인증 시작][인증 시작] 자습서에서 구현한 `AuthenticateAsync` 메서드를 업데이트하여 사용자 지정 API를 호출하고 AAD에서 사용자에 대한 추가 정보를 반환합니다.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app][mobile-services-aad-graph-info-update-app]]



## <a name="test-app"></a> 앱 테스트

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app][mobile-services-aad-graph-info-test-app]]

## <a name="next-steps"></a> 다음 단계

다음 자습서인 [모바일 서비스의 AAD에서 역할 기반 액세스 제어][모바일 서비스의 AAD에서 역할 기반 액세스 제어]에서는 액세스를 허용하기 전에 AAD(Azure Active Directory)에서 역할 기반 액세스 제어를 사용해서 그룹 구성원을 확인합니다.


<!-- Anchors. --> 
<!-- Images --> 
<!-- URLs. -->


[Windows 스토어 C\#]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/ "Windows 스토어 C#"
[.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ ".NET 백 엔드"
[JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "JavaScript 백 엔드"
[그래프 클라이언트 라이브러리]: http://go.microsoft.com/fwlink/?LinkId=510536
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[AAD에서 앱 등록을 위한 액세스 키 생성]: #generate-key
[GetUserInfo 사용자 지정 API 만들기]: #create-api
[사용자 지정 API를 사용하도록 앱 업데이트]: #update-app
[앱 테스트]: #test-app
[사용자 지정 API 자습서]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[Azure Active Directory 로그인 사용 등록]: /ko-kr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
[Azure 관리 포털]: https://manage.windowsazure.com/
[mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
[mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
[모바일 서비스의 AAD에서 역할 기반 액세스 제어]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
