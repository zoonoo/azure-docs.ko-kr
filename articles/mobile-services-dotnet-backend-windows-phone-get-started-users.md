<properties 
	pageTitle="인증 시작(Windows Phone) | 모바일 개발자 센터" 
	description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Windows Phone 앱 사용자를 인증하는 방법을 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="glenga"/>

# 모바일 서비스 앱에 인증 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

이 항목에서는 Windows Phone 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 설명합니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

>[AZURE.NOTE] 이 항목에서는 Windows Phone 8.0 및 Windows Phone 8.1 Silverlight 앱만 지원합니다. Windows Phone 스토어 8.1 또는 범용 Windows 앱이 있는 경우 대신 [이 항목의 범용 Windows 앱 버전]을 수행하세요(mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가]
4. [클라이언트에 인증 토큰 저장]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다. 


##<a name="register"></a> 인증을 위해 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>Visual Studio에서 클라이언트 앱 프로젝트를 열고 App.xaml.cs에서 <strong>MobileServiceClient</strong>의 인스턴스가 모바일 서비스의 클라우드 URL을 사용하도록 구성되어 있는지 확인합니다.</li> 
<li><p>F5 키를 눌러 이 퀵 스타트 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.</p>
   
   	<p>이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 <em>TodoItem</em> 테이블에서 이제 인증을 요구하기 때문에 발생합니다.</p></li>
</ol>

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>클라이언트에 권한 부여 토큰 저장

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조]를 참조하세요.

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[클라이언트에 인증 토큰 저장]: #tokens
[다음 단계]: #next-steps


<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect를 사용한 Windows Phone 앱의 Single Sign-On]: mobile-services-windows-phone-single-sign-on.md
[모바일 서비스 시작]: mobile-services-dotnet-backend-windows-phone-get-started.md
[데이터 시작]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[인증 시작]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[푸시 알림 시작]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[스크립트를 통해 사용자 권한 부여]: mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts.md
[JavaScript 및 HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /develop/mobile/how-to-guides/work-with-net-client-library
[Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->