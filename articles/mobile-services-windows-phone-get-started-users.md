<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스에서 인증 시작
===========================

[Windows 스토어 C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows 스토어 JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/en-us/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")
[.NET 백 엔드](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/ ".NET 백 엔드") | [JavaScript 백 엔드](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users/ "JavaScript 백 엔드")

이 토픽은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

[자습서 보기](http://go.microsoft.com/fwlink/?LinkId=298631) [동영상 재생](http://go.microsoft.com/fwlink/?LinkId=298631) 10:50

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성](#register)
2.  [테이블 사용 권한을 인증된 사용자로 제한](#permissions)
3.  [앱에 인증 추가](#add-authentication)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-wp8) 자습서를 완료해야 합니다.

앱 등록인증을 위해 앱 등록 및 모바일 서비스 구성
------------------------------------------------

사용자를 인증할 수 있으려면 ID 공급자에 앱을 등록해야 합니다. 그런 다음 공급자가 생성한 클라이언트 암호를 모바일 서비스에 등록해야 합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

	![][1]

2.  **대시보드** 탭을 클릭하고 **모바일 서비스 URL** 값을 기록해 둡니다.

    ![][2]

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

    ![][3]

이제 모바일 서비스와 앱이 선택한 인증 공급자를 사용하도록 둘 다 구성되었습니다.

사용 권한을 인증된 사용자로 제한
--------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![][4]

2.  **사용 권한** 탭을 클릭하고 모든 사용 권한을 **인증된 사용자만**으로 설정한 후 **저장**을 클릭합니다. 그러면 **TodoItem** 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다. 또한 익명 사용자의 가능성을 허용할 필요가 없으므로 다음 자습서의 스크립트가 간소화됩니다.

    ![][5]

3.  Visual Studio 2012 Express for Windows Phone에서 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-wp8) 자습서를 완료할 때 만든 프로젝트를 엽니다.

4.  F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    This happens because the app attempts to access Mobile Services as an unauthenticated user, but the _TodoItem_ table now requires authentication.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

앱에 인증 추가
--------------

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

다음 단계
---------

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8)에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: https://manage.windowsazure.com/
