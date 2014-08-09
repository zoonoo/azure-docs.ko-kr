<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

모바일 서비스에서 인증 시작
===========================

[Windows 스토어 C\#](/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows 스토어 C#")[Windows 스토어 JavaScript](/ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows 스토어 JavaScript")[Windows Phone](/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/ko-kr/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/ko-kr/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/ko-kr/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")
[.NET 백 엔드](/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ ".NET 백 엔드") | [JavaScript 백 엔드](/ko-kr/documentation/articles/mobile-services-ios-get-started-users/ "JavaScript 백 엔드")

이 항목에서는 iOS 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가]

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다.

이 자습서를 완료하려면 XCode 4.5 및 iOS 5.0 이상 버전이 필요합니다.

인증을 위해 앱 등록 및 모바일 서비스 구성
-----------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

사용 권한을 인증된 사용자로 제한
--------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Xcode에서 [모바일 서비스 시작](/ko-kr/documentation/articles/mobile-services-ios-get-started) 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

2.  **Run** 단추를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다. 그리고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

	이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다. 

## 앱에 인증 추가 
[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)] 

## 다음 단계 다음 자습서인 
[모바일 서비스 사용자의 서비스 쪽 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다. 

[인증을 위해 앱 등록 및 모바일 서비스 구성]: \#register 
[테이블 사용 권한을 인증된 사용자로 제한]: \#permissions 
[앱에 인증 추가]: \#add-authentication 
[다음 단계]:\#next-steps 

[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png 
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png 

[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png 
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png 
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png 

[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253 
[Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On]: /en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet 
[모바일 서비스 시작]: /en-us/develop/mobile/tutorials/get-started-ios 
[데이터 시작]: /en-us/develop/mobile/tutorials/get-started-with-data-ios 
[인증 시작]: /en-us/develop/mobile/tutorials/get-started-with-users-ios 
[푸시 알림 시작]: /en-us/develop/mobile/tutorials/get-started-with-push-ios 
[스크립트를 통해 사용자 권한 부여]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios 
[Azure 관리 포털]: https://manage.windowsazure.com/


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure Management Portal]: https://manage.windowsazure.com/