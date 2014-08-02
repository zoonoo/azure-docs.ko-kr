<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

Live Connect Single Sign-On으로 Windows 스토어 앱 인증
======================================================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone")

이 항목에서는 Windows 스토어 앱에서 Live Connect Single Sign-On을 사용하여 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 Live Connect를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. Live Connect에 의해 인증되고 나면 로그인한 사용자는 이름과 함께 환영의 메시지를 보게 되고 사용자 ID 값이 표시됩니다.

**참고**

이 자습서에서는 Windows 스토어 앱용 Live Connect에서 제공되는 Single Sign-On 환경을 사용할 때 얻는 이점을 보여 줍니다. 이 환경은 모바일 서비스를 사용해 이미 로그온한 사용자를 더 쉽게 인증할 수 있습니다. 여러 인증 공급자를 지원하는, 보다 일반화된 인증 환경을 알아보려면 [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/) 항목을 참조하십시오.

이 자습서에서는 Live Connect 인증을 사용하도록 설정하는 다음 기본 단계를 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성](#register)
2.  [테이블 사용 권한을 인증된 사용자로 제한](#permissions)
3.  [앱에 인증 추가](#add-authentication)

이 자습서를 이용하려면 다음 사항이 필요합니다.

-   [Windows용 Live SDK](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express for Windows 8 RC 이상 버전

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started) 자습서를 완료해야 합니다.

앱 등록Windows 스토어에 앱 등록
-------------------------------

사용자를 인증할 수 있으려면 Windows 스토어에 앱을 제출해야 합니다. 그런 다음 Live Connect와 모바일 서비스를 통합하도록 클라이언트 암호를 등록해야 합니다.

1.  앱을 아직 등록하지 않은 경우 Windows 스토어 앱용 개발자 센터에 있는 [앱 제출 페이지](http://go.microsoft.com/fwlink/p/?LinkID=266582)로 이동하여 Microsoft 계정으로 로그온한 후 **앱 이름**을 클릭합니다.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png)

2.  **앱 이름**에 앱의 이름을 입력하고 **앱 이름 예약**을 클릭한 후 **저장**을 클릭합니다.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png)

     이렇게 하면 앱을 새로 Windows 스토어에 등록하게 됩니다.

3.  Visual Studio 2012 Express for Windows 8에서 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started) 자습서를 완료할 때 만든 프로젝트를 엽니다.

4.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png) 

   **응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

1.  마법사에서 **로그인**을 클릭한 후 Microsoft 계정에 로그인합니다.

2.  2단계에서 등록한 앱을 선택하고 **다음**을 클릭한 후 **연결**을 클릭합니다.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png)

     이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.    

3.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png)

4.  **대시보드** 탭을 클릭하고 **사이트 URL** 값을 기록해 둡니다.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png)

    리디렉션 도메인을 정의하는 데 이 값을 사용합니다.

5.  Live Connect 개발자 센터의 [내 응용 프로그램](http://go.microsoft.com/fwlink/p/?LinkId=262039) 페이지로 이동하고 **내 응용 프로그램** 목록에서 앱을 클릭합니다.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png) 

6.  **설정 편집**을 클릭한 후 **API 설정**을 클릭하고 **클라이언트 ID** 및 **클라이언트 암호** 값을 기록해 둡니다.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>보안 정보</b>
    <p>클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.</p>
    </div>

1.  **리디렉션 도메인**에서 8단계의 모바일 서비스 URL을 입력하고 **저장**을 클릭합니다.

2.  관리 포털로 돌아와 **ID** 탭을 클릭하고 Windows 스토어에서 획득한 **클라이언트 암호**를 입력한 후 **저장**을 클릭합니다.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png)

이제 모바일 서비스와 앱이 Live Connect를 사용하도록 둘 다 구성되었습니다.

사용 권한 제한사용 권한을 인증된 사용자로 제한
----------------------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png)

2.  **사용 권한** 탭을 클릭하고 모든 사용 권한을 **인증된 사용자만**으로 설정한 후 **저장**을 클릭합니다. 그러면 **TodoItem** 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다. 또한 익명 사용자의 가능성을 허용할 필요가 없으므로 다음 자습서의 스크립트가 간소화됩니다.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png)

3.  Visual Studio 2012 Express for Windows 8에서 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started) 자습서를 완료할 때 만든 프로젝트를 엽니다.

4.  F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 상태 코드 401(인증되지 않음)의 예외가 발생하는지 확인합니다.

      이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하지만 _TodoItem_ 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 Live Connect로 사용자를 인증하도록 앱을 업데이트합니다.

인증 추가앱에 인증 추가
-----------------------

1.  [Windows용 Live SDK](http://go.microsoft.com/fwlink/p/?LinkId=262253)를 다운로드하여 설치합니다.

2.  Visual Studio의 **Project** 메뉴에서 **참조 추가**를 클릭한 후 **Windows**를 확장하여 **확장**을 클릭하고 **Live SDK**를 선택한 다음 **확인**을 클릭합니다.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png) 
  
   Live SDK에 대한 참조가 프로젝트에 추가됩니다.

1.  프로젝트 파일 MainPage.xaml.cs를 열고 다음 using 문을 추가합니다.

         using Microsoft.Live;        

2.  MainPage 클래스에 다음 코드 조각을 추가합니다.

         private LiveConnectSession session;
         private async System.Threading.Tasks.Task Authenticate()
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

    현재 Live Connect 세션을 저장하기 위한 멤버 변수와 인증 프로세스를 처리할 메서드가 만들어집니다.

    **참고**

    이 코드는 가능한 경우 로그아웃을 실행하며 응용 프로그램이 실행될 때마다 사용자에게 자격 증명을 요구하는 메시지가 표시되도록 합니다. 인증이 올바르게 작동하는지 확인하기 위해 다양한 Microsoft 계정을 사용하여 응용 프로그램을 테스트하기 쉬워집니다. 이 메커니즘은 로그인한 사용자에게 연결된 Microsoft 계정이 없는 경우에만 작동합니다.

3.  이전 단계의 *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>* 문자열을 Live Connect에서 앱을 설정할 때 **https://*service-name*.azure-mobile.net/** 형식으로 지정한 리디렉션 도메인으로 업데이트합니다.

    **참고**

    Windows 스토어 앱에서 리디렉션 도메인 URI 값을 클래스 생성자에 전달하여 **LiveAuthClient** 클래스 인스턴스가 만들어집니다. [Windows Phone 8 앱](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)에서 클라이언트 ID를 전달하여 동일한 클래스가 인스턴스화됩니다.

4.  기존 **OnNavigatedTo** 이벤트 처리기를 새 **Authenticate** 메서드를 호출하는 처리기로 바꿉니다.

         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

5.  F5 키를 눌러 앱을 실행하고 Microsoft 계정으로 Live Connect에 로그인합니다.

    로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

다음 단계
---------

다음 자습서인 [스크립트를 통해 사용자 권한 부여](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. 인증을 위해 다른 ID 공급자를 사용하는 방법에 대한 자세한 내용은 [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)을 참조하십시오. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)를 참조하십시오.

