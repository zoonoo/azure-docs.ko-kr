<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Get started with authentication (Xamarin.Android) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" documentationCenter="Mobile" authors="" />

모바일 서비스에서 인증 시작
===========================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

이 항목에서는 Xamarin.Android 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성](#register)
2.  [테이블 사용 권한을 인증된 사용자로 제한](#permissions)
3.  [앱에 인증 추가](#add-authentication)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-xamarin-android) 자습서를 완료해야 합니다.

이 자습서를 완료하려면 [Xamarin.Android] 및 Android SDK 4.2 이상 버전이 필요합니다.

앱 등록인증을 위해 앱 등록 및 모바일 서비스 구성
------------------------------------------------

사용자를 인증할 수 있으려면 ID 공급자에 앱을 등록해야 합니다. 그런 다음 공급자가 생성한 클라이언트 암호를 모바일 서비스에 등록해야 합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

   	![][4]

2.  **대시보드** 탭을 클릭하고 **사이트 URL** 값을 기록해 둡니다.

   	![][5]

    앱을 등록할 때 이 값을 ID 공급자에게 제공해야 할 수도 있습니다.

3.  아래 목록에서 지원되는 ID 공급자를 선택하고 해당 공급자에 앱을 등록하는 단계를 따릅니다.

 - <a href="/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Microsoft 계정</a>
 - <a href="/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Facebook 로그인</a>
 - <a href="/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Twitter 로그인</a>
 - <a href="/en-us/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Google 로그인</a>
 - <a href="/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    공급자가 생성한 클라이언트 ID 및 암호 값을 기록해 두어야 합니다.

    **보안 정보**

    공급자가 생성한 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

1.  관리 포털로 돌아가서 **ID** 탭을 클릭하고 ID 공급자로부터 받은 앱 ID 및 공유 암호 값을 입력한 후 **저장**을 클릭합니다.

   	![][13]

이제 모바일 서비스와 앱이 선택한 인증 공급자를 사용하도록 둘 다 구성되었습니다.

사용 권한 제한사용 권한을 인증된 사용자로 제한
----------------------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][14]

2.  **사용 권한** 탭을 클릭하고 모든 사용 권한을 **인증된 사용자만**으로 설정한 후 **저장**을 클릭합니다. 그러면 **TodoItem** 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다. 또한 익명 사용자의 가능성을 허용할 필요가 없으므로 다음 자습서의 스크립트가 간소화됩니다.

   	![][15]

3.  Eclipse에서 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-xamarin-android) 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

4.  **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

인증 추가앱에 인증 추가
-----------------------

1.  **TodoActivity** 클래스에 다음 메서드를 추가합니다.

             private async Task Authenticate()
             {
                 try
                 {
                     user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
                     CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                 }
                 catch (Exception ex)
                 {
                     CreateAndShowDialog(ex, "Authentication failed");
                 }
             }

    인증 프로세스를 처리하는 새 메서드가 만들어집니다. 사용자는 Microsoft 계정 로그인을 사용하여 인증됩니다. 인증된 사용자의 ID를 표시하는 대화 상자가 나타납니다. 양성 인증 없이는 진행할 수 없습니다.

    **참고**

    Microsoft 이외의 ID 공급자를 사용하는 경우 위의 **login** 메서드에 전달된 값을 *Facebook*, *Google* 또는 *Twitter* 중 하나로 변경합니다.

2.  **OnCreate** 메서드에서 `MobileServiceClient` 개체를 인스턴스화하는 코드 뒤에 다음 코드 줄을 추가합니다.

         await Authenticate();

    이 호출은 인증 프로세스를 시작하고 비동기적으로 기다립니다.

3.  **OnCreate** 메서드에서 `await Authenticate();` 뒤의 나머지 코드를 새 **CreateTable** 메서드로 이동합니다. 다음과 같이 표시됩니다.

             private async Task CreateTable()
             {
                 // Get the Mobile Service Table instance to use
                 todoTable = client.GetTable<TodoItem>();

                 textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);

                 // Create an adapter to bind the items with the view
                 adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
                 var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
                 listViewTodo.Adapter = adapter;

                 // Load the items from the Mobile Service
                 await RefreshItemsFromTableAsync();
             }

4.  2단계에서 **Authenticate** 호출이 추가된 후 **OnCreate**에서 새 **CreateTable** 메서드를 호출합니다.

         await CreateTable();

5.  **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작하고 원하는 ID 공급자에 로그인합니다.

   	로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

완성된 예 가져오기
------------------

[완성된 예제 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=331328)를 다운로드합니다. 고유한 Azure 설정으로 **applicationURL** 및 **applicationKey** 변수를 업데이트해야 합니다.

다음 단계
---------

다음 자습서인 [스크립트를 통해 사용자 권한 부여](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android)에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png
