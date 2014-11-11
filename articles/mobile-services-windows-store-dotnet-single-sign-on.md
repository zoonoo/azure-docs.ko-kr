<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="mobile-services" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Live Connect Single Sign-On으로 Windows 스토어 앱 인증

<div class="dev-center-tutorial-selector sublanding">

[Windows 스토어 C#][Windows 스토어 C#][Windows 스토어 JavaScript][Windows 스토어 JavaScript][Windows Phone][Windows Phone]

</div>

이 항목에서는 Windows 스토어 또는 Windows Phone 8.1 스토어 앱에서 Live Connect Single Sign-On을 사용하여 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 Live Connect를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. Live Connect에 의해 인증되고 나면 로그인한 사용자는 이름과 함께 환영의 메시지를 보게 되고 사용자 ID 값이 표시됩니다.

> [WACOM.NOTE]이 자습서에서는 Windows 스토어 앱용 Live Connect에서 제공되는 Single Sign-On 환경을 사용할 때 얻는 이점을 보여 줍니다. 이 환경은 모바일 서비스를 사용해 이미 로그온한 사용자를 더 쉽게 인증할 수 있습니다. 여러 인증 공급자를 지원하는, 보다 일반화된 인증 환경을 알아보려면 [인증 시작][인증 시작] 항목을 참조하십시오.

이 자습서에서는 Live Connect 인증을 사용하도록 설정하는 다음 기본 단계를 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성][인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한][테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가][앱에 인증 추가]

이 자습서를 사용하려면 다음이 필요합니다.

-   [Windows용 Live SDK][Windows용 Live SDK]
-   Microsoft Visual Studio 2012 Express for Windows 8 RC 이상 버전

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료해야 합니다.

> [WACOM.NOTE]이 자습서에서는 JavaScript 백 엔드 모바일 서비스를 사용합니다. Live Connect 또는 다른 인증 클라이언트를 사용한 클라이언트 관리 인증은 아직 .NET 백 엔드 모바일 서비스에서 지원되지 않습니다.

## <a name="register"></a>Windows 스토어에 앱 등록

사용자를 인증할 수 있으려면 Windows 스토어에 앱을 제출해야 합니다. 그런 다음 Live Connect와 모바일 서비스를 통합하도록 클라이언트 암호를 등록해야 합니다.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Visual Studio 2012 Express for Windows 8에서 [모바일 서비스 시작][1] 자습서를 완료할 때 만든 프로젝트를 엽니다.

2.  F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가

1.  [Windows용 Live SDK][Windows용 Live SDK]를 다운로드하여 설치합니다.

2.  Visual Studio의 **Project** 메뉴에서 **참조 추가**를 클릭한 후 **Windows**를 확장하여 **확장**을 클릭하고 **Live SDK**를 선택한 다음 **확인**을 클릭합니다.

    ![][0]

    Live SDK에 대한 참조가 프로젝트에 추가됩니다.

3.  프로젝트 파일 MainPage.xaml.cs를 열고 다음 using 문을 추가합니다.

        using Microsoft.Live;        

4.  MainPage 클래스에 다음 코드 조각을 추가합니다.

        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

            while (session == null)
            {
                // Force a logout to make it easier to test with multiple Microsoft Accounts
                if (liveIdClient.CanLogout)
                    liveIdClient.Logout();

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
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
         }

    현재 Live Connect 세션을 저장하기 위한 멤버 변수와 인증 프로세스를 처리할 메서드가 만들어집니다. 이 코드는 가능한 경우 로그아웃을 실행하며 응용 프로그램이 실행될 때마다 사용자에게 자격 증명을 요구하는 메시지가 표시되도록 합니다. 인증이 올바르게 작동하는지 확인하기 위해 다양한 Microsoft 계정을 사용하여 응용 프로그램을 테스트하기 쉬워집니다. 이 메커니즘은 로그인한 사용자에게 연결된 Microsoft 계정이 없는 경우에만 작동합니다.

    > [WACOM.NOTE]앱이 실행될 때마다 Live Connection 인증 토큰 또는 모바일 서비스 권한 부여 토큰을 요청해야 하는 것은 아닙니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 보다 나은 접근 방법은 토큰을 캐시하고 **LoginWithMicrosoftAccountAsync**를 호출하기 전에 캐시된 모바일 서비스의 사용을 시도하는 것입니다. 이 토큰을 캐시하는 방법을 예를 보려면 [인증 시작][2]을 참조하십시오.

5.  이전 단계의 *\<\< INSERT REDIRECT DOMAIN HERE \>\>* 문자열을 Live Connect에서 앱을 구성할 때 **<https://_service-name_.azure-mobile.net/>** 형식으로 지정한 리디렉션 도메인으로 업데이트합니다.

    <div class="dev-callout">

    **참고**
    Windows 스토어 앱에서 리디렉션 도메인 URI 값을 클래스 생성자에 전달하여 **LiveAuthClient** 클래스 인스턴스가 만들어집니다. [Windows Phone 8 앱][Windows Phone 8 앱]에서 클라이언트 ID를 전달하여 동일한 클래스가 인스턴스화됩니다.

    </div>

6.  기존 **OnNavigatedTo** 이벤트 처리기를 새 **Authenticate** 메서드를 호출하는 처리기로 바꿉니다.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }

7.  F5 키를 눌러 앱을 실행하고 Microsoft 계정으로 Live Connect에 로그인합니다.

로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [스크립트를 통해 사용자 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. 인증을 위해 다른 ID 공급자를 사용하는 방법에 대한 자세한 내용은 [인증 시작][3]을 참조하십시오. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조][모바일 서비스 .NET 방법 개념 참조]를 참조하십시오.



  [Windows 스토어 C#]: /ko-kr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko-kr/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows 스토어 JavaScript"
  [Windows Phone]: /ko-kr/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone"
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
  [테이블 사용 권한을 인증된 사용자로 제한]: #permissions
  [앱에 인증 추가]: #add-authentication
  [Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /ko-kr/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png
  [2]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens
  [Windows Phone 8 앱]: /ko-kr/develop/mobile/tutorials/single-sign-on-wp8/
  [스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
  [3]: /ko-kr/develop/mobile/tutorials/get-started-with-users-dotnet
  [모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library
