<properties
	pageTitle="앱 서비스 응용 프로그램에 대해 Microsoft 계정 인증을 구성하는 방법"
	description="앱 서비스 응용 프로그램에 대해 Microsoft 계정 인증을 구성하는 방법을 알아봅니다."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="10/30/2015"
	ms.author="mahender"/>

# Microsoft 계정 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 항목에서는 Microsoft 계정을 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Microsoft 계정을 사용하여 응용 프로그램 등록

1. [Azure 관리 포털]에 로그인한 다음 응용 프로그램으로 이동합니다. **URL**을 복사합니다. Microsoft 계정 앱을 구성하는 데 사용합니다.

2. Microsoft 계정 개발자 센터의 [내 응용 프로그램] 페이지로 이동하고 필요한 경우 Microsoft 계정으로 로그온합니다.

4. **응용 프로그램 만들기**를 클릭한 후 **응용 프로그램 이름**을 입력하고 **I accept**를 클릭합니다.

5. **API 설정**을 클릭합니다. **모바일 또는 데스크톱 클라이언트 앱**에 대해 **예**를 선택합니다. **리디렉션 URL** 필드에 응용 프로그램의 **리디렉션 URL**을 입력하고 **저장**을 클릭합니다. 리디렉션 URI는 경로 _/.auth/login/microsoftaccount/callback_가 추가된 응용 프로그램의 URL입니다. 예: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. HTTPS 체계를 사용 중인지 확인합니다.

	![][0]


	> [AZURE.NOTE]앱 서비스 인증 / 권한 부여 기능 대신 앱 서비스 게이트웨이를 사용 중인 경우 리디렉션 URL은 _/signin-microsoft_ 경로가 있는 게이트웨이 URL을 대신 사용합니다.


6. **앱 설정**을 클릭하고 **클라이언트 ID** 및 **클라이언트 암호** 값을 기록해 둡니다.


    > [AZURE.NOTE]클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 클라이언트 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.
	

## <a name="secrets"> </a>응용 프로그램에 Microsoft 계정 정보 추가


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Microsoft Account**. Paste in the values you obtained earlier and click **Save**.


7. [Azure 관리 포털]로 돌아가서 응용 프로그램으로 이동합니다. **설정**을 클릭한 다음 **인증 / 권한 부여**를 클릭합니다.

8. 인증 / 권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **설정**으로 전환합니다.

9. **Microsoft 계정**을 클릭합니다. 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣고 필요에 따라 응용 프로그램에 필요한 범위를 설정합니다. 그런 후 **OK**를 클릭합니다.

    ![][1]
	
11. 기본적으로 앱 서비스는 로그인을 제공하지만 사이트 콘텐츠 및 API에 대한 액세스를 제한하지 않습니다. 이는 앱 코드에서 처리합니다. Microsoft 계정 로그인으로 사이트를 완전히 보호하려면 **Microsoft 계정** 옵션을 사용하도록 **요청이 인증되지 않은 경우 수행할 동작** 드롭다운을 변경합니다. 이렇게 하려면 모든 요청이 인증되어야 합니다. 인증되지 않은 요청은 Microsoft 계정으로 로그인하도록 리디렉션됩니다.

11. **저장**을 클릭합니다.


이제 앱에서 Microsoft 계정을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 관리 포털]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->