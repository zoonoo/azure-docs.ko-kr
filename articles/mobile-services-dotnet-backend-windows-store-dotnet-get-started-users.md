<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

모바일 서비스에서 인증 시작
===========================

[Windows 스토어 C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows 스토어 C#") [Windows 스토어 JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows 스토어 JavaScript") [Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")

[.NET 백 엔드](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/ ".NET 백 엔드") | [JavaScript 백 엔드](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/ "JavaScript 백 엔드")

이 토픽은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성](#register)
2.  [테이블 사용 권한을 인증된 사용자로 제한](#permissions)
3.  [앱에 인증 추가](#add-authentication)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/) 자습서를 완료해야 합니다.

> [WACOM.NOTE]이 자습서는 다양한 ID 공급자를 통해 사용자를 인증하기 위해 모바일 서비스에서 제공하는 기본 방법을 보여 줍니다. 이 방법은 간편하게 구성할 수 있으며 여러 공급자를 지원합니다. 그러나 이 방법을 사용하는 경우 앱이 시작될 때마다 사용자가 로그인해야 합니다. 대신 Live Connect를 사용하여 Windows 스토어 앱에서 Single Sign-On을 제공하려면 [Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On](/en-us/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on) 토픽을 참조하십시오.

인증을 위해 앱 등록 및 모바일 서비스 구성
-----------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  Visual Studio에서 모바일 서비스 프로젝트의 Web.config 파일을 열고, appSettings 섹션에서 앱 ID 및 ID 공급자로부터 얻은 공유된 비밀 값을 설정합니다.

    이러한 설정은 로컬 개발 중에 사용됩니다. 모바일 서비스 프로젝트를 Azure에 게시하면 이러한 설정은 포털에서 설정한 값으로 재정의됩니다.

2.  (옵션) [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록](/en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/)의 단계를 완료합니다.

    **참고**

    이 단계는 Microsoft 계정 로그인 공급자에만 적용되기 때문에 선택 사항입니다. 모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록하는 경우 클라이언트에서 Single Sign-On 환경을 위해 Microsoft 계정 로그인 자격 증명을 다시 사용할 수 있습니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 Microsoft 계정 로그인 사용자에게 로그인 프롬프트가 표시됩니다. Microsoft 계정 ID 공급자를 사용하려는 경우 이 단계를 완료합니다.

##사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Visual Studio 2013에서 [모바일 서비스 시작](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/) 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

2.  F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

앱에 인증 추가
--------------

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

> [WACOM.NOTE]모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록한 경우 *useSingleSignOn* 매개 변수에 **true** 값을 제공하여 [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) 메서드를 호출해야 합니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 사용자에게 로그인 프롬프트가 표시됩니다.

다음 단계
---------

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts)에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)를 참조하십시오.

