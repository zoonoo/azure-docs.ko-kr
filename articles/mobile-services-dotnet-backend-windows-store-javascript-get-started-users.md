<properties pageTitle="인증 시작(Windows 스토어) | 모바일 개발자 센터" metaKeywords="authentication, FAcebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# 모바일 서비스 앱에 인증 추가 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

이 항목에서는 Windows 스토어 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

>[WACOM.NOTE]모바일 서비스 JavaScript 클라이언트 라이브러리를 사용하는 경우에는 현재 Windows Phone 스토어 8.1 앱에서 인증이 지원되지 않습니다. JavaScript 클라이언트를 사용하는 범용 Windows 앱 프로젝트가 있는 경우에는 현재 Windows Phone에서 사용자를 인증할 수 없습니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가]
4. [클라이언트에 권한 부여 토큰 저장]

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다. 

## <a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="3">
<li><p>Visual Studio 2013에서 <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/">모바일 서비스 시작</a> 자습서를 완료했을 때 생성된 프로젝트를 엽니다.</p></li> 
<li><p>F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.</p>
   
   	<p>이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 <em>TodoItem</em> 테이블에서 이제 인증을 요구하기 때문에 발생합니다.</p></li>
</ol>

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

##<a name="tokens"></a>클라이언트에 권한 부여 토큰 저장

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조]를 참조하세요.

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[클라이언트에 권한 부여 토큰 저장]: #tokens
[다음 단계]:#next-steps


<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect를 사용한 Windows 스토어 앱에 대한 Single Sign-On]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[스크립트를 통해 사용자 권한 부여]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts
[JavaScript 및 HTML]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/

[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library
[Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]: /ko-kr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
