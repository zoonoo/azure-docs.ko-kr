<properties linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="Get Started with Users" pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

모바일 서비스에서 인증 시작
===========================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

이 토픽은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

오른쪽의 클립을 클릭하면 이 자습서의 동영상 버전을 시청할 수 있습니다.

[자습서 보기](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) [동영상 재생](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) 10:04

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성](#register)
2.  [테이블 사용 권한을 인증된 사용자로 제한](#permissions)
3.  [앱에 인증 추가](#add-authentication)

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 먼저 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started) 자습서를 완료해야 합니다.

**참고**

이 자습서는 다양한 ID 공급자를 통해 사용자를 인증하기 위해 모바일 서비스에서 제공하는 기본 방법을 보여 줍니다. 이 방법은 구성이 용이하며 여러 공급자를 지원합니다. 그러나 이 방법을 사용하는 경우 앱이 시작될 때마다 사용자가 로그인해야 합니다. 대신 Live Connect를 사용하여 Windows 스토어 앱에서 Single Sign-On을 제공하려면 [Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet) 토픽을 참조하십시오.

앱 등록인증을 위해 앱 등록 및 모바일 서비스 구성
------------------------------------------------

사용자를 인증할 수 있으려면 ID 공급자에 앱을 등록해야 합니다. 그런 다음 공급자가 생성한 클라이언트 암호를 모바일 서비스에 등록해야 합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

	![][4]

2.  **대시보드** 탭을 클릭하고 **모바일 서비스 URL** 값을 기록해 둡니다.

	![][5]

    앱을 등록할 때 이 값을 ID 공급자에게 제공해야 할 수도 있습니다.

3.  아래 목록에서 지원되는 ID 공급자를 선택하고 해당 공급자에 앱을 등록하는 단계를 따릅니다.

-   [Microsoft 계정](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
-   [Facebook 로그인](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
-   [Twitter 로그인](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
-   [Google 로그인](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    공급자가 생성한 클라이언트 ID 및 암호 값을 기록해 두어야 합니다.

    **보안 정보**

    공급자가 생성한 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

4.  관리 포털로 돌아가서 **ID** 탭을 클릭하고 ID 공급자로부터 받은 앱 ID 및 공유 암호 값을 입력한 후 **저장**을 클릭합니다.

	![][13]

5.  (옵션) [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록](/en-us/develop/mobile/how-to-guides/register-windows-store-app-package)의 단계를 완료합니다.

    **참고**

    이 단계는 Microsoft 계정 로그인 공급자에만 적용되기 때문에 선택 사항입니다. 모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록하는 경우 클라이언트에서 Single Sign-On 환경을 위해 Microsoft 계정 로그인 자격 증명을 다시 사용할 수 있습니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 Microsoft 계정 로그인 사용자에게 로그인 프롬프트가 표시됩니다. Microsoft 계정 ID 공급자를 사용하려는 경우 이 단계를 완료합니다.

이제 모바일 서비스와 앱이 선택한 인증 공급자를 사용하도록 둘 다 구성되었습니다.

사용 권한 제한사용 권한을 인증된 사용자로 제한
----------------------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

	![][14]

2.  **사용 권한** 탭을 클릭하고 모든 사용 권한을 **인증된 사용자만**으로 설정한 후 **저장**을 클릭합니다. 그러면 **TodoItem** 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다. 또한 익명 사용자의 가능성을 허용할 필요가 없으므로 다음 자습서의 스크립트가 간소화됩니다.

	![][15]

3.  Visual Studio 2012 Express for Windows 8에서 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started) 자습서를 완료할 때 만든 프로젝트를 엽니다.

4.  F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

	This happens because the app attempts to access Mobile Services as an unauthenticated user, but the _TodoItem_ table now requires authentication.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

인증 추가앱에 인증 추가
-----------------------

1.  프로젝트 파일 mainpage.xaml.cs를 열고 다음 using 문을 추가합니다.

         using Windows.UI.Popups;

2.  MainPage 클래스에 다음 코드 조각을 추가합니다.

         private MobileServiceUser user;
         private async System.Threading.Tasks.Task Authenticate()
         {
             while (user == null)
             {
                 string message;
                 try
                 {
                     user = await App.MobileService
                         .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                     message = 
                         string.Format("You are now logged in - {0}", user.UserId);
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

    현재 사용자를 저장하기 위한 멤버 변수와 인증 프로세스를 처리할 메서드가 만들어집니다. 사용자는 Facebook 로그인을 사용하여 인증됩니다. Facebook 이외의 ID 공급자를 사용하는 경우 위의 **MobileServiceAuthenticationProvider** 값을 공급자에 대한 값으로 변경합니다.

    **참고**

    모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록한 경우 *useSingleSignOn* 매개 변수에 **true** 값을 제공하여 [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) 메서드를 호출해야 합니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 사용자에게 로그인 프롬프트가 표시됩니다.

3.  기존 **OnNavigatedTo** 메서드 재정의를 새 **Authenticate** 메서드를 호출하는 다음 메서드로 바꿉니다.

         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

4.  F5 키를 눌러 앱을 실행하고 선택한 ID 공급자로 앱에 로그인합니다.

	When you are successfully logged-in, the app should run without errors, and you should be able to query Mobile Services and make updates to data.

다음 단계
---------

다음 자습서인 [스크립트를 통해 사용자 권한 부여](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)를 참조하십시오.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-dotnet-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-dotnet-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-dotnet-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: ./mobile-services-single-sign-on-win8-dotnet.md
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-users-js

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: /en-us/develop/mobile/how-to-guides/register-windows-store-app-package
