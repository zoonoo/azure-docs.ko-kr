<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

모바일 서비스에서 인증 시작
===========================

[Windows 스토어 C\#](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows 스토어 C#")[Windows 스토어 JavaScript](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows 스토어 JavaScript")[Windows Phone](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")[iOS](/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS")

[.NET 백 엔드](/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ ".NET 백 엔드") | [JavaScript 백 엔드](/ko-kr/documentation/articles/mobile-services-ios-get-started-users/ "JavaScript 백 엔드")

이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다.

인증을 위해 앱 등록 및 모바일 서비스 구성
-----------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  Visual Studio에서 모바일 서비스 프로젝트에 대한 Web.config 파일을 열고, appSettings 섹션에서 ID 공급자에서 가져온 앱 ID 및 공유 암호 값을 설정합니다.

    이러한 설정은 로컬 개발 중에 사용됩니다. Azure에 모바일 서비스 프로젝트를 게시한 후 이러한 설정은 포털에 설정된 값으로 재정의됩니다.

사용 권한을 인증된 사용자로 제한
--------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Xcode에서 [모바일 서비스 시작](/ko-kr/documentation/articles/mobile-services-ios-get-started) 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

2.  **Run** 단추를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다. 그리고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

    다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다. ##앱에 인증 추가 

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)] 

## 다음 단계 다음 자습서인 

[모바일 서비스 사용자의 서비스 쪽 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다. 

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register 
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions 
[앱에 인증 추가]: #add-authentication 
[다음 단계]:#next-steps 

<!-- URLs. -->
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ 
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ 
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ 
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ 
[스크립트를 통해 사용자 권한 부여]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts 
[Azure 관리 포털]: https://manage.windowsazure.com/ 
[모바일 서비스 .NET 방법 개념 참조]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library 
[Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]: /ko-kr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


