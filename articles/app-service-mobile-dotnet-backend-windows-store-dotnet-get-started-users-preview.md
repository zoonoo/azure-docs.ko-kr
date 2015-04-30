<properties 
	pageTitle="Windows에서 모바일 앱에 대한 인증 시작" 
	description="모바일 앱을 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Windows 앱 사용자를 인증하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="mahender"/>

# Windows 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

이 항목에서는 클라이언트 응용 프로그램에서 앱 서비스 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 앱 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 먼저 [모바일 앱 시작] 자습서를 완료해야 합니다. 

## <a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li>Visual Studio에서 클라이언트 앱 프로젝트를 열고 App.xaml.cs에서 <b>MobileServiceClient</b> 인스턴스가 모바일 앱 리소스의 클라우드 URL을 사용하도록 구성되어 있는지 확인합니다.</li> 
<li><p>F5 키를 눌러 이 퀵 스타트 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.</p>
   
   	<p>이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 코드에 액세스하려고 시도하는데 <em>TodoItem</em> 테이블에서 이제 인증을 요구하기 때문에 발생합니다.</p></li>
</ol>

다음에는 앱 서비스에서 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)] 


[AZURE.NOTE] 앱 서비스에 Windows 스토어 앱 패키지 정보를 등록한 경우 <em>useSingleSignOn</em> 매개 변수에 <strong>true</strong> 값을 제공하여 <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> 메서드를 호출해야 합니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 사용자에게 로그인 프롬프트가 표시됩니다.


## <a name="tokens"></a>클라이언트에 인증 토큰 저장

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)] 


<!-- Anchors. -->
[인증을 위해 앱 등록 및 앱 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[클라이언트에 인증 토큰 저장]: #tokens
[다음 단계]:#next-steps


<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 앱 시작]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md


[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library
[Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md
[Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On]: mobile-services-windows-store-dotnet-single-sign-on.md

<!--HONumber=49-->