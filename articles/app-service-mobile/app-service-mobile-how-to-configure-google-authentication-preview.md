<properties
	pageTitle="앱 서비스 응용 프로그램에 대해 Google 인증을 구성하는 방법"
	description="앱 서비스 응용 프로그램에 대해 Google 인증을 구성하는 방법을 알아봅니다."
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Google 로그인을 사용하도록 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

이 항목에서는 Google을 인증 공급자로 사용하도록 모바일 앱을 구성하는 방법을 보여 줍니다.

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동하세요.

1. [Azure 관리 포털]에 로그온한 다음 모바일 앱으로 이동합니다. **URL**을 복사합니다. 나중에 Google 앱으로 사용합니다.
 
2. **설정**, **사용자 인증** 및 **Google**을 차례로 클릭합니다. **리디렉션 URI**를 복사합니다. Google 앱을 구성하는 데 사용합니다.

3. [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 웹 사이트로 이동하고 Google 계정 자격 증명으로 로그인하고 **Create Project**를 클릭하고 **Project name**을 제공한 후 **Create**를 클릭합니다.

4. 왼쪽 탐색 모음에서 **API & Auth**를 클릭하고 **Social APIs** 아래에서 **Google + API** > **Enable API**를 클릭합니다.

5. **API & Auth** > **Credentials** > **OAuth consent screen**을 클릭하고 **Email address**를 선택한 다음 **Product Name**을 입력하고 **Save**를 클릭합니다.

6. **Credentials** 탭에서 **Add credentials** > **OAuth 2.0 client ID**를 클릭하고 **Web application**을 선택합니다.

7. 앞서 복사한 모바일 앱 **URL**을 **Authorized JavaScript Origins**에 붙여넣고, 앞서 복사한 **Redirect URI**를 **Authorized Redirect URI**에 붙여넣습니다. 리디렉션 URI는 _/signin-google_ 경로가 추가된 모바일 앱 게이트웨이입니다. 예: `https://contosogateway.azurewebsites.net/signin-google` HTTPS 체계를 사용 중인지 확인합니다. 그런 다음 **만들기**를 클릭합니다.

8. 다음 화면에서 클라이언트 ID 및 클라이언트 암호 값을 적어 둡니다.

    > [AZURE.IMPORTANT]클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.

9. 모바일 앱용 Google 설정 블레이드의 [Azure 관리 포털]에 돌아와서 이전에 가져온 클라이언트 ID 및 클라이언트 암호 값을 붙여넣습니다. 그런 다음 **Save**를 클릭합니다.

     ![][1]

이제 앱에서 Google을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 관리 포털]: https://portal.azure.com/
 

<!---HONumber=Oct15_HO3-->