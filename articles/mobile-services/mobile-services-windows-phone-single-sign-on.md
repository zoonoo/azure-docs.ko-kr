<properties 
	pageTitle="Live Connect로 앱 인증(Windows Phone) | 모바일 개발자 센터" 
	description="Windows Phone 응용 프로그램에서 Azure 모바일 서비스의 Live Connect Single Sign-On을 사용하는 방법을 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Microsoft 계정을 사용하여 클라이언트 관리 인증으로 Windows Phone 앱 인증

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]
##개요
이 항목에서는 Windows Phone 8 또는 Windows Phone 8.1 Silverlight 앱에서 Live SDK를 사용하여 Microsoft 계정에 대한 인증 토큰을 가져오는 방법을 보여줍니다. 그런 다음 이 토큰을 사용하여 Azure 모바일 서비스로 사용자를 인증합니다. 이 자습서에서는 Live SDK를 사용하여 Microsoft 계정 인증을 기존 프로젝트에 추가합니다. 성공적으로 인증되고 나면 로그인한 사용자는 이름과 함께 환영 메시지를 보게 되고 사용자 ID 값이 표시됩니다.

>[AZURE.NOTE]이 자습서에서는 클라이언트 관리 인증 및 Live SDK 사용의 이점을 설명합니다. 이 환경은 모바일 서비스를 사용해 이미 로그온한 사용자를 더 쉽게 인증할 수 있습니다. 추가 범위를 요청하여 앱이 OneDrive와 같은 리소스에도 액세스하도록 설정할 수 있습니다. 서비스 관리 인증은 보다 일반적인 경험을 제공하며 여러 인증 공급자를 지원합니다. 서비스 관리 인증에 대한 자세한 내용은 [앱에 인증 추가](mobile-services-windows-phone-get-started-users.md) 항목을 참조하세요.

이 자습서를 사용하려면 다음이 필요합니다.

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 이상 버전
+ 먼저 [기존 앱에 모바일 서비스 추가] 자습서도 완료해야 합니다.

##앱을 등록하여 Microsoft 계정 사용 

사용자를 인증하려면 먼저 Microsoft 계정 개발자 센터에서 앱을 등록해야 합니다. 그런 다음 모바일 서비스와 이 등록을 연결해야 합니다. Microsoft 계정 등록을 만들고 모바일 서비스에 연결하려면 다음 항목의 단계를 완료하세요.

+ [Microsoft 계정 로그인을 사용하도록 앱 등록](mobile-services-how-to-register-microsoft-authentication.md) 

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

이제 *TodoItems* 테이블이 로그인한 사용자만 액세스할 수 있도록 하는 경우, 리소스에 액세스를 제한해야 합니다.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>앱에 인증 추가

마지막으로, Live SDK를 추가하고 앱에서 사용자를 인증하는 데 사용합니다.

1. **솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인** 범주를 선택하고, **LiveSDK**를 검색하고, **Live SDK** 패키지에서 **설치**를 클릭하고, 모든 프로젝트를 선택한 다음 라이선스 계약에 동의합니다.

  	이렇게 하면 Live SDK를 솔루션에 추가합니다.

5. 프로젝트 파일 mainpage.xaml.cs를 열고 다음 using 문을 추가합니다.

        using Microsoft.Live;      

6. MainPage 클래스에 다음 코드 조각을 추가합니다.
	
        private LiveConnectSession session;
        private static string clientId = "<microsoft-account-client-id>";
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            // Create the authentication client using the client ID of the registration.
            LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

            while (session == null)
            {
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    MessageBox.Show(message, title, MessageBoxButton.OK);
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);
                }
            }
        }

    현재 Live Connect 세션을 저장하기 위한 멤버 변수와 인증 프로세스를 처리할 메서드가 만들어집니다. LiveLoginResult는 사용자를 인증하기 위해 모바일 서비스에 지정 된 인증 토큰을 포함합니다.

7. 위의 코드 조각에서 `<microsoft-account-client-id>` 자리 표시자를 앱에 대해 Microsoft 계정 등록에서 가져온 클라이언트 ID로 바꿉니다.

5. 기존 **OnNavigatedTo** 메서드 재정의에서 **RefreshTodoItems** 메서드 호출을 주석 처리하거나 삭제합니다.

	그러면 사용자가 인증되기 전에 데이터가 로드되지 않습니다. 다음으로, 로그인 프로세스를 시작하는 단추를 추가합니다.

6. MainPage 클래스에 다음 코드 조각을 추가합니다.

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = System.Windows.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
7. 앱 프로젝트에서 MainPage.xaml 프로젝트 파일을 열고 **TitlePanel**의 다음 **Button** 요소를 **TextBlock** 요소 다음에 추가합니다.

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
		
9. F5 키를 눌러 앱을 실행하고 Microsoft 계정으로 로그인합니다.

   로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

이제, 등록된 ID 공급자 중 하나로 인증된 사용자는 *TodoItem* 테이블에 액세스할 수 있습니다. 사용자 특정 데이터의 보안을 강화하려면 권한 부여도 구현해야 합니다. 이를 위해 지정된 사용자의 사용자 ID를 가져옵니다. 이는 사용자가 지정된 리소스에 대해 갖고 있는 액세스 단계를 결정하는 데 사용할 수 있습니다.

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. 인증을 위해 다른 ID 공급자를 사용하는 방법에 대한 자세한 내용은 [인증 시작]을 참조하십시오.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[기존 앱에 모바일 서비스 추가]: mobile-services-windows-phone-get-started-data.md
[인증 시작]: mobile-services-windows-phone-get-started-users.md
[스크립트를 통해 사용자 권한 부여]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->