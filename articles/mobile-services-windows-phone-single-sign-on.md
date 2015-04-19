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
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

# Live Connect Single Sign-On으로 Windows Phone 8 앱 인증

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">Windows 스토어 C#</a><a href="/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">Windows 스토어 JavaScript</a><a href="/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone" class="current">Windows Phone</a>
</div>	

이 항목에서는 Windows Phone 8 앱에서 Live Connect Single Sign-On을 사용하여 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다.  이 자습서에서는 Live Connect를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. Live Connect에 의해 인증되고 나면 로그인한 사용자는 이름과 함께 환영의 메시지를 보게 되고 사용자 ID 값이 표시됩니다.  

>[AZURE.NOTE]이 자습서에서는 Windows Phone 앱용 Live Connect에서 제공되는 Single Sign-On 환경을 사용할 때 얻는 이점을 보여 줍니다. 이 환경에서는 모바일 서비스를 사용해 이미 로그온한 사용자를 더 쉽게 인증할 수 있습니다. 여러 인증 공급자를 지원하는 보다 일반화된 인증 환경을 알아보려면 <a href="mobile-services-windows-phone-get-started-users.md">앱에 인증 추가</a> 항목을 참조하세요. 

이 자습서에서는 Live Connect 인증을 사용하도록 설정하는 다음 기본 단계를 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가]

이 자습서의 작업을 수행하려면 다음이 필요합니다.

+ [Windows 및 Windows Phone용 Live SDK]
+ Microsoft Visual Studio 2012 Express for Windows Phone
+ 먼저 [기존 앱에 모바일 서비스 추가] 자습서도 완료해야 합니다.

<h2><a name="register"></a>Live Connect에 앱 등록</h2>

사용자를 인증하려면 먼저 Live Connect 개발자 센터에서 앱을 등록해야 합니다. 그런 다음 Live Connect와 모바일 서비스를 통합하도록 클라이언트 암호를 등록해야 합니다.

1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

   	![][4]

2. **대시보드** 탭을 클릭하고 **사이트 URL** 값을 기록해 둡니다.

   	![][5]

    리디렉션 도메인을 정의하는 데 이 값을 사용합니다.

3. Live Connect 개발자 센터의 <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">내 응용 프로그램</a> 페이지로 이동하고 필요한 경우 Microsoft 계정으로 로그온합니다. 

4. **응용 프로그램 만들기**를 클릭한 후 **응용 프로그램 이름**을 입력하고 **동의함**을 클릭합니다.

   	![][1] 

   	이를 통해 응용 프로그램이 Live Connect에 등록됩니다.

5. **응용 프로그램 설정 페이지**를 클릭한 후 **API 설정**을 클릭하고 **클라이언트 ID** 및 **클라이언트 암호** 값을 기록해 둡니다. 

   	![][2]

 > [AZURE.NOTE] **보안 정보** 클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 클라이언트 암호를 공유하거나 앱과 함께 배포하지 마세요.

6. **리디렉션 도메인**에 2단계의 모바일 서비스 URL을 입력하고 **모바일 클라이언트 앱** 아래에서 **예**를 클릭한 후 **저장**을 클릭합니다.

7. 관리 포털로 돌아와 **Identity** 탭을 클릭하고 Live Connect에서 획득한 **클라이언트 암호**를 입력한 후 **저장**을 클릭합니다.

   	![][13]

이제 모바일 서비스와 앱이 Live Connect를 사용하도록 둘 다 구성되었습니다.

<h2><a name="permissions"></a>사용 권한을 인증된 사용자로 제한</h2>

1. 관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다. 

   	![][14]

2. **사용 권한** 탭을 클릭하고 모든 사용 권한을 **인증된 사용자만**으로 설정한 후 **저장**을 클릭합니다. 그러면 **TodoItem** 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다. 또한 익명 사용자의 가능성을 허용할 필요가 없으므로 다음 자습서의 스크립트가 간소화됩니다.

   	![][15]

3. Visual Studio 2012 Express for Windows phone에서 [기존 앱에 모바일 서비스 추가] 자습서를 완료할 때 만든 프로젝트를 엽니다. 

4. F5 키를 눌러 이 퀵 스타트 기반 앱을 실행합니다. 상태 코드 401(인증되지 않음)의 예외가 발생하는지 확인합니다. 
   
   	이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 하지만 _TodoItem_ 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 Live Connect로 사용자를 인증하도록 앱을 업데이트합니다.

<h2><a name="add-authentication"></a>앱에 인증 추가</h2>

1. [Windows 및 Windows Phone용 Live SDK]를 다운로드하여 설치합니다.

2. Visual Studio의 **프로젝트** 메뉴에서 **참조 추가**를 클릭한 다음 **어셈블리**를 확장하고 **확장**을 클릭한 다음 **Microsoft.Live**를 선택하고 **확인**을 클릭합니다. 

   	![][16]

  	Live SDK에 대한 참조가 프로젝트에 추가됩니다.

5. 프로젝트 파일 mainpage.xaml.cs를 열고 다음 using 문을 추가합니다.

        using Microsoft.Live;      

6. MainPage 클래스에 다음 코드 조각을 추가합니다.
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

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

    현재 Live Connect 세션을 저장하기 위한 멤버 변수와 인증 프로세스를 처리할 메서드가 만들어집니다.

7. 이전 단계의 _<< INSERT CLIENT ID HERE >>_ 문자열을 Live Connect에 앱을 등록할 때 생성된 클라이언트 ID 값으로 업데이트합니다.

    > [AZURE.NOTE] Windows Phone 8 앱에서는 클라이언트 ID 값을 클래스 생성자에 전달하여 **LiveAuthClient** 클래스 인스턴스가 만들어집니다. [Windows 스토어 앱](/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)에서는리디렉션 도메인 URI를 전달하여 동일한 클래스가 인스턴스화됩니다.

8. 기존 **OnNavigatedTo** 메서드 재정의를 삭제하거나 주석으로 처리하고 페이지의 **Loaded** 이벤트를 처리하는 다음 메서드로 바꿉니다. 

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	이 메서드는 새 **Authenticate** 메서드를 호출합니다. 

9. MainPage 생성자를 다음 코드로 바꿉니다.

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	이 생성자는 또한 Loaded 이벤트의 처리기를 등록합니다.
		
9. F5 키를 눌러 앱을 실행하고 Microsoft 계정으로 Live Connect에 로그인합니다. 

   	성공적으로 로그인되고 나면 앱이 오류 없이 실행되고 모바일 서비스를 쿼리하여 데이터를 업데이트할 수 있습니다.

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. 인증을 위해 다른 ID 공급자를 사용하는 방법에 대한 자세한 내용은 [인증 시작]을 참조하세요. 

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[다음 단계]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png

[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 및 Windows Phone용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[기존 앱에 모바일 서비스 추가]: mobile-services-windows-phone-get-started-data.md
[인증 시작]: mobile-services-windows-phone-get-started-users.md
[스크립트를 통해 사용자 권한 부여]: mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure 관리 포털]: https://manage.windowsazure.com/

<!--HONumber=49-->