<properties 
	pageTitle="Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증(Windows 스토어) | 모바일 개발자 센터" 
	description="Windows 스토어 응용 프로그램에서 ADAL을 사용하여 SSO(Single Sign-On) 사용자를 인증하는 방법에 대해 알아봅니다." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="wesmc"/>

# Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

이 자습서에서는 Azure Active Directory에서 [클라이언트 지향 로그인 작업](http://msdn.microsoft.com/library/azure/jj710106.aspx)을 지원하기 위해 Active Directory 인증 라이브러리를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. Azure Active Directory에서 [서비스 지향 로그인 작업](http://msdn.microsoft.com/library/azure/dn283952.aspx)을 지원하려면 [모바일 서비스 앱에 인증 추가](mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md) 자습서를 시작합니다.

사용자를 인증하려면 먼저 AAD(Azure Active Directory)에 응용 프로그램을 등록해야 합니다. 이 작업은 다음 두 단계로 수행됩니다. 먼저, 모바일 서비스를 등록하고 그에 대한 사용 권한을 표시합니다. 두 번째로 Windows 스토어 앱을 등록하여 해당 사용 권한에 대한 액세스를 부여해야 합니다.


>[AZURE.NOTE] 이 자습서는 모바일 서비스에서 [클라이언트 지향 로그인 작업](http://msdn.microsoft.com/library/azure/jj710106.aspx)을 사용하여 Windows 스토어 앱에 대해 Single Sign-On Azure Active Directory 인증을 수행할 수 있도록 하는 방법의 이해를 돕기 위한 것입니다. 모바일 서비스를 처음 사용하는 경우 [모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [Azure Active Directory에 모바일 서비스 등록]
2. [Azure Active Directory에 앱 등록]
3. [인증을 요구하도록 모바일 서비스 구성]
4. [클라이언트 앱에 인증 코드 추가]
5. [인증을 사용하여 클라이언트 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

* Windows 8.1에서 실행 중인 Visual Studio 2013
* [모바일 서비스 시작] 또는 [데이터 시작] 자습서 완료
* Microsoft Azure 모바일 서비스 SDK NuGet 패키지
* Active Directory 인증 라이브러리 NuGet 패키지 

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

## <a name="register-app-aad"></a>Azure Active Directory에 앱 등록

Azure Active Directory에 앱을 등록하려면 Windows 스토어에 앱을 연결하고 앱용 패키지 SID(보안 식별자)를 받아야 합니다. 패키지 SID가 Azure Active Directory의 네이티브 클라이언트 설정에 등록됩니다.


### 새 스토어 앱 이름과 앱 연결

1. Visual Studio에서 클라이언트 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어** 및 **응용 프로그램을 스토어에 연결**을 클릭합니다.

    ![][1]

2. 개발자 센터 계정에 로그인합니다.

3. 앱에 예약할 앱 이름을 입력하고 **예약**을 클릭합니다.

    ![][2]

4. 새 앱 이름을 선택하고 **다음**을 클릭합니다.

5. **연결**을 클릭하여 앱과 스토어 이름을 연결합니다.


### 앱용 패키지 SID를 검색합니다.

이제 네이티브 앱 설정으로 구성할 패키지 SID를 검색해야 합니다.

1. [Windows 개발자 센터 대시보드]에 로그인하고 앱에서 **편집**을 클릭합니다.

    ![][3]

2. 그런 다음 **서비스**를 클릭합니다.

    ![][4]

3. 그런 다음 **라이브 서비스 사이트**를 클릭합니다. 

    ![][5]

4. 페이지 맨 위에서 패키지 SID를 복사합니다.

    ![][6]

### 네이티브 앱 등록 만들기

1. [Azure 관리 포털]에서 **Active Directory**로 이동한 다음 디렉터리를 클릭합니다.

    ![][7] 

2. 맨 위에 있는 **응용 프로그램** 탭을 클릭한 다음 앱을 클릭하여 **추가**합니다. 

    ![][8]

3. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

4. 응용 프로그램 추가 마법사에서 응용 프로그램의 **이름**을 입력하고 **네이티브 클라이언트 응용 프로그램** 유형을 클릭합니다. 계속하려면 클릭합니다.

    ![][9]

5. **리디렉션 URI** 상자에서 앞서 복사한 앱 패키지 SID를 붙여 넣은 다음 클릭하여 네이티브 앱 등록을 완료합니다.

    ![][10]

6. 네이티브 응용 프로그램의 **구성** 탭을 클릭하고 **클라이언트 ID**를 복사합니다. 이 ID는 나중에 필요합니다.

    ![][11]

7. 페이지 아래의 **다른 응용 프로그램에 대한 권한** 섹션으로 스크롤하여 앞서 등록한 모바일 서비스 응용 프로그램에 대한 모든 권한을 부여합니다. 그런 다음 **저장**을 클릭합니다.

    ![][12]

이제 모바일 서비스는 AAD에서 앱의 Single Sign-On을 받도록 구성됩니다.



## <a name="require-authentication"></a>인증을 요구하도록 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>클라이언트 앱에 인증 코드 추가

1. Visual Studio에서 Windows 스토어 클라이언트 앱 프로젝트를 엽니다.

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. Visual Studio의 솔루션 탐색기에서 MainPage.xaml.cs 파일을 열고 문을 사용하여 다음을 추가합니다.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. 다음 코드를  `AuthenticateAsync` 메서드를 선언하는 MainPage 클래스에 추가합니다.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

6. 위의  `AuthenticateAsync` 메서드에 대한 코드에서 응용 프로그램을 제공한 테넌트의 이름으로 **INSERT-AUTHORITY-HERE**를 바꿉니다. 형식은 https://login.windows.net/tenant-name.onmicrosoft.com입니다. 이 값은 [Azure 관리 포털]에서 Azure Active Directory의 도메인 탭에서 복사할 수 있습니다.

7. 위의  `AuthenticateAsync` 메서드에 대한 코드에서 **INSERT-RESOURCE-URI-HERE**를 모바일 서비스에 대한 **앱 ID URI**로 바꿉니다. [Azure Active Directory에 등록하는 방법] 항목을 따르면 앱 ID URI가 https://todolist.azure-mobile.net/login/aad와 유사합니다.

8. 위의  `AuthenticateAsync` 메서드에 대한 코드에서 **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 응용 프로그램에서 복사한 클라이언트 ID로 바꿉니다.

9. Visual Studio용 솔루션 탐색기 창의 클라이언트 프로젝트에서 Package.appxmanifest 파일을 엽니다. **기능** 탭을 클릭하고 **엔터프라이즈 응용 프로그램** 및 **개인 네트워크(클라이언트 및 서버)**를 사용하도록 설정합니다. 파일을 저장합니다.

    ![][14]

10. MainPage.cs 파일에서 다음과 같이  `AuthenticateAsync` 메서드를 호출하도록  `OnNavigatedTo` 이벤트 처리기를 업데이트합니다.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


## <a name="test-client"></a>인증을 사용하여 클라이언트 테스트

1. Visual Studio에서 클라이언트 앱을 실행합니다.
2. Azure Active Directory에 대한 로그인 메시지가 표시됩니다.  
3. 앱이 인증하며 todo 항목을 반환합니다.

    ![][15]



<!-- Anchors. -->
[Azure Active Directory에 모바일 서비스 등록]: #register-mobile-service-aad
[Azure Active Directory에 앱 등록]: #register-app-aad
[인증을 요구하도록 모바일 서비스 구성]: #require-authentication
[JavaScript 백 엔드 모바일 서비스]: #javascript-authentication
[.NET 백 엔드 모바일 서비스]: #dotnet-authentication
[클라이언트 앱에 인증 코드 추가]: #add-authentication-code
[인증을 사용하여 클라이언트 테스트]: #test-client

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png

<!-- URLs. -->
[Azure Active Directory에 등록하는 방법]: /ko-kr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure 관리 포털]: https://manage.windowsazure.com/
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Windows 개발자 센터 대시보드]: http://go.microsoft.com/fwlink/p/?LinkID=266734

<!--HONumber=42-->
