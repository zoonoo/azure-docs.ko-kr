<properties 
	pageTitle="Microsoft 인증 등록 - 모바일 서비스" 
	description="Azure 모바일 서비스 응용 프로그램에서 Microsoft 인증을 위해 등록하는 방법에 대해 알아봅니다." 
	authors="ggailey777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Microsoft 계정 로그인을 사용하도록 앱 등록

이 항목에서는 Azure 모바일 서비스를 위한 인증 공급자로 Live Connect를 사용할 수 있도록 앱을 등록하는 방법을 보여 줍니다. 

>[AZURE.NOTE]공용 Windows 앱에 대한 Microsoft 계정 인증을 구성하거나 Windows 스토어 앱의 Single Sign-On 환경을 제공하려면 [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록](/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication)을 참조하세요.

1. 먼저 <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">내 응용 프로그램</a> 페이지로 이동하고 필요한 경우 Microsoft 계정으로 로그온합니다. 

2. **응용 프로그램 만들기**를 클릭한 후 **응용 프로그램 이름**을 입력하고 **동의함**을 클릭합니다.

   	![][1] 

   	이를 통해 응용 프로그램이 Live Connect에 등록됩니다.

3. **API 설정**을 클릭하고 **리디렉션 URL**에 `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` 값을 제공하고 **저장**을 클릭합니다.

	>[AZURE.NOTE]Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 _signin-microsoft_ 경로를 추가한 것입니다(예: <code>https://todolist.azure-mobile.net/signin-microsoft</code>).  

	![][3]

	이렇게 하면 앱에 대해 Microsoft 계정 인증이 사용하도록 설정됩니다.

	>[AZURE.NOTE]기존 Live Connect 앱 등록의 경우 먼저 **강화된 리디렉션 보안**을 사용하도록 설정해야 할 수도 있습니다.

4. **앱 설정**을 클릭하고 **클라이언트 ID** 및 **클라이언트 암호** 값을 기록해 둡니다. 

   	![][2]

    > [AZURE.NOTE] 클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 클라이언트 암호를 공유하거나 앱과 함께 배포하지 마세요.

이제 모바일 서비스에 클라이언트 ID 및 클라이언트 암호 값을 제공하여 앱에서 Microsoft 계정을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Azure 관리 포털]: https://manage.windowsazure.com/

<!--HONumber=47-->
