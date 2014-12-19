<properties pageTitle="인증 시작(Windows 스토어) | 모바일 개발자 센터" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 모바일 서비스 앱에 인증 추가

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

이 항목에서는 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가]
4. [앱에 인증 토큰 저장]

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다.

##<a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>Xcode에서 클라이언트 앱 빠른 시작 프로젝트를 엽니다.</li>
<li><p><strong>실행</strong> 단추를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다. 그리고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.<p>

   	<p>이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 <em>TodoItem</em> 테이블에서 이제 인증을 요구하기 때문에 발생합니다.</p></li>
</ol>

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>앱에 인증 토큰 저장

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다.

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[다음 단계]:#next-steps
[앱에 인증 토큰 저장]:#store-authentication

<!-- URLs. -->
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[스크립트를 통해 사용자 권한 부여]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts

[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library
[Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]: /ko-kr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
