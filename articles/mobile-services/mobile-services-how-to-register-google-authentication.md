<properties 
	pageTitle="Google 인증 등록 | Microsoft Azure"
	description="Google을 사용하여 Azure 모바일 서비스에서 인증을 받도록 앱을 등록하는 방법을 알아봅니다."
	services="mobile-services"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="glenga"/>

# 모바일 서비스에 Google 로그인을 사용하기 위해 앱 등록

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

이 항목에서는 Google을 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

>[AZURE.NOTE]이 자습서에서는 플랫폼에 구애받지 않는 확장 가능한 모바일 응용 프로그램을 손쉽게 빌드할 수 있도록 하는 솔루션인 [Azure 모바일 서비스](http://azure.microsoft.com/services/mobile-services/)에 대해 다룹니다. 모바일 서비스를 사용하면 데이터 동기화, 사용자 인증, 푸시 알림 보내기 작업을 간편하게 수행할 수 있습니다. 이 페이지는 [인증 시작](mobile-services-ios-get-started-users.md) 자습서를 지원하며, 사용자 앱에 사용 로그인하는 방법을 설명합니다. <br/>모바일 서비스를 처음 사용하는 경우 [모바일 서비스 시작](mobile-services-ios-get-started.md) 자습서를 완료하세요.

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동하세요.

3. [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 웹 사이트로 이동하고 Google 계정 자격 증명으로 로그인하고 **Create Project**를 클릭하고 **Project name**을 제공한 후 **Create**를 클릭합니다.

4. 왼쪽 탐색 모음에서 **API & Auth**를 클릭하고 **Social APIs** 아래에서 **Google + API** > **Enable API**를 클릭합니다.

5. **API & Auth** > **Credentials** > **OAuth consent screen**을 클릭하고 **Email address**를 선택한 다음 **Product Name**을 입력하고 **Save**를 클릭합니다.

6. **Credentials** 탭에서 **Add credentials** > **OAuth 2.0 client ID**를 클릭하고 **Web application**을 선택합니다.

7. **Authorized JavaScript Origins**에 모바일 서비스 URL을 입력하고 **Authorized Redirect URI**에 생성된 URL을 경로 `/login/google`이 뒤에 추가된 모바일 서비스 URL로 바꾼 후 **Create client ID**를 클릭합니다.

	>[AZURE.NOTE]Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 _signin-google_ 경로를 추가한 것입니다(예: `https://todolist.azure-mobile.net/signin-google`). &nbsp;
	
8. 다음 화면에서 클라이언트 ID 및 클라이언트 암호 값을 적어 둡니다.

    > [AZURE.IMPORTANT]클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.

이제 앱에서 인증을 위해 Google 로그인을 사용하여 모바일 서비스를 구성할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=September15_HO1-->