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
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Google 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Google을 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하십시오.

> [AZURE.NOTE]
이 항목에서는 앱 서비스 인증/권한 부여 기능을 사용하는 과정을 보여 줍니다. 이는 대부분의 응용 프로그램에 대한 앱 서비스 게이트웨이를 대체합니다. 게이트웨이 사용에 적용되는 차이가 항목 전체적으로 명시되어 있습니다.


## <a name="register"> </a>Google을 사용하여 응용 프로그램 등록

1. [Azure 포털]에 로그인한 다음 응용 프로그램으로 이동합니다. **URL**을 복사합니다. Google 앱을 구성하는 데 사용합니다.

2. [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 웹 사이트로 이동하고 Google 계정 자격 증명으로 로그인하고 **Create Project**를 클릭하고 **Project name**을 제공한 후 **Create**를 클릭합니다.

3. 왼쪽 탐색 모음에서 **API & Auth**를 클릭하고 **Social APIs** 아래에서 **Google + API** > **Enable API**를 클릭합니다.

4. **API & Auth** > **Credentials** > **OAuth consent screen**을 클릭하고 **Email address**를 선택한 다음 **Product Name**을 입력하고 **Save**를 클릭합니다.

5. **Credentials** 탭에서 **Add credentials** > **OAuth 2.0 client ID**를 클릭하고 **Web application**을 선택합니다.

6. 앞서 복사한 앱 서비스 **URL**을 **Authorized JavaScript Origins**에 붙여넣고, 앞서 복사한 **Redirect URI**를 **Authorized Redirect URI**에 붙여넣습니다. 리디렉션 URI는 경로 _/.auth/login/google/callback_이 추가된 응용 프로그램의 URL입니다. 예: `https://contoso.azurewebsites.net/.auth/login/google/callback` HTTPS 체계를 사용 중인지 확인합니다. 그런 다음 **Create**를 클릭합니다.


	> [AZURE.NOTE]
	앱 서비스 인증/권한 부여 기능 대신 앱 서비스 게이트웨이를 사용 중인 경우 리디렉션 URL은 _/signin-google_ 경로가 있는 게이트웨이 URL을 대신 사용합니다.


7. 다음 화면에서 클라이언트 ID 및 클라이언트 암호 값을 적어 둡니다.


    > [AZURE.IMPORTANT]
	클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.


## <a name="secrets"> </a>응용 프로그램에 Google 정보 추가

> [AZURE.NOTE]
앱 서비스 게이트웨이를 사용하는 경우 이 섹션을 무시하고 대신 포털에서 게이트웨이로 이동합니다. **Settings**, **Identity**, **Google**을 차례로 선택합니다. 앞에서 얻은 값을 붙여넣고 **Save**를 클릭합니다.


8. [Azure 포털]로 돌아가서 응용 프로그램으로 이동합니다. **Settings**를 클릭한 다음 **Authentication / Authorization**을 클릭합니다.

9. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **On**으로 전환합니다.

10. **Google**을 클릭합니다. 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣고 필요에 따라 응용 프로그램에 필요한 범위를 설정합니다. 그런 후 **OK**를 클릭합니다.

    ![][1]

	기본적으로 앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

17. (선택 사항) Google에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Google**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Google에 리디렉션되어야 합니다.

12. **Save**를 클릭합니다.

이제 앱에서 Google을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 포털]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->