<properties pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="mahender"></tags>

# 모바일 서비스에서 인증 시작

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows 스토어 C#">Windows 스토어 C#</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS" >iOS</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android" class="current">Android</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-android-get-started-users/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성][인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한][테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가][앱에 인증 추가]
4. [클라이언트에 인증 토큰 저장][클라이언트에 인증 토큰 저장]
5. [만료된 토큰 새로 고침][만료된 토큰 새로 고침]


이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료해야 합니다.

## <a name="register"></a> 인증을 위해 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a> 사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1. Eclipse에서 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

2. **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a> 앱에 인증 추가

[WACOM.INCLUDE [mobile-services-android-authenticate-app][mobile-services-android-authenticate-app]]

## <a name="cache-tokens"></a> 클라이언트에 인증 토큰 캐싱

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token][mobile-services-android-authenticate-app-with-token]]

## <a name="refresh-tokens"></a> 토큰 캐시 새로 고침

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token][mobile-services-android-authenticate-app-refresh-token]]

## <a name="next-steps"></a> 다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여][모바일 서비스 사용자의 서비스 쪽 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다.


<!-- Anchors. --> 
<!-- URLs. -->

[Windows 스토어 C\#]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows 스토어 C#"
[Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows 스토어 JavaScript"
[Windows Phone]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
[iOS]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS"
[Android]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
[Xamarin.iOS]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
[Xamarin.Android]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
[.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/ ".NET 백 엔드"
[JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-android-get-started-users/ "JavaScript 백 엔드"
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[클라이언트에 인증 토큰 저장]: #cache-tokens
[만료된 토큰 새로 고침]: #refresh-tokens
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
[mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
[mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
[mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
[mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
[mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
[모바일 서비스 사용자의 서비스 쪽 권한 부여]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-authorize-users-in-scripts
