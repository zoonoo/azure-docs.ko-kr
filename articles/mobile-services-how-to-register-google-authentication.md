<properties pageTitle="Google 인증 등록 - 모바일 서비스" description="Google을 사용하여 Azure 모바일 서비스에서 인증을 받도록 앱을 등록하는 방법을 알아봅니다." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# 모바일 서비스에 Google 로그인을 사용하기 위해 앱 등록

이 항목에서는 Google을 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

>[AZURE.NOTE] 이 자습서에서는 플랫폼에 구애받지 않는 확장 가능한 모바일 응용 프로그램을 손쉽게 빌드할 수 있도록 하는 솔루션인 [Azure 모바일 서비스](http://azure.microsoft.com/ko-kr/services/mobile-services/)에 대해 다룹니다. 모바일 서비스를 사용하면 데이터 동기화, 사용자 인증, 푸시 알림 보내기 작업을 간편하게 수행할 수 있습니다. 이 페이지는 <a href="http://azure.microsoft.com/ko-kr/documentation/articles/mobile-services-ios-get-started-users/">인증 시작</a> 자습서를 보완하는 역할을 하며, 사용자를 앱에 기록하는 방법을 설명합니다. 모바일 서비스를 처음 사용하는 경우 <a href="http://azure.microsoft.com/ko-kr/documentation/articles/mobile-services-ios-get-started/">모바일 서비스 시작</a> 자습서를 완료하는 것이 좋습니다.

이 항목의 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동합니다.

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google API</a> 웹 사이트로 이동하고 Google 계정 자격 증명으로 로그인하고 **Create Project**를 클릭하고 **프로젝트 이름**을 제공한 후 **Create**를 클릭합니다.

   	![][1]

2. **Consent screen**을 클릭하고 **Email Address**를 선택한 후 **제품 이름**을 입력하고 **Save**를 클릭합니다. 

3. **API & Auth**, **Credentials** 및 **Create new Client ID**를 차례로 클릭합니다.

   	![][2]

4. **Web application**을 선택하고 **Authorized JavaScript Origins**에 모바일 서비스 URL을 입력하고 **Authorized Redirect URI**의 생성된 URL을 경로 _/login/google_이 뒤에 추가된 모바일 서비스 URL로 바꾼 후 **Create client ID**를 클릭합니다.

	>[AZURE.NOTE] Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 _signin-google_ 경로를 추가한 것입니다(예: <code>https://todolist.azure-mobile.net/signin-google</code>. 

   	![][3]

5. **Client ID for web applications**에 있는 **Client ID** 및 **Client secret**의 값을 기록해 둡니다. 

   	![][4]

    > [AZURE.IMPORTANT] 클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.

이제 모바일 서비스에 클라이언트 ID 및 클라이언트 암호 값을 제공하여 앱에서 Google 로그인을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google API]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[인증 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure 관리 포털]: https://manage.windowsazure.com/


<!--HONumber=42-->
