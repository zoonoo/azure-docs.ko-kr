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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Microsoft 계정 로그인을 사용하도록 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 항목에서는 Microsoft 계정을 인증 공급자로 사용하도록 Azure 모바일 앱을 구성하는 방법을 보여 줍니다.

## <a name="register"> </a>Microsoft 계정을 사용하여 응용 프로그램 등록

1. [Azure 관리 포털]에 로그온한 다음 모바일 앱으로 이동합니다.

2. **설정**, **사용자 인증** 및 **Microsoft 계정**을 차례로 클릭합니다. **리디렉션 URL**을 복사합니다. Microsoft 계정에 대한 새 앱을 구성하는 데 사용합니다.

3. Microsoft 계정 개발자 센터의 [내 응용 프로그램] 페이지로 이동하고 필요한 경우 Microsoft 계정으로 로그온합니다.

4. **응용 프로그램 만들기**를 클릭한 후 **응용 프로그램 이름**을 입력하고 **I accept**를 클릭합니다.

5. **API 설정**을 클릭합니다. **모바일 또는 데스크톱 클라이언트 앱**에 대해 **예**를 선택합니다. **리디렉션 URL** 필드에 앞서 복사한 **리디렉션 URL**를 입력합니다. _/signin-microsoft_가 추가된 모바일 앱 게이트웨이입니다. 예: `https://contosogateway.azurewebsites.net/signin-microsoft`. HTTPS 체계를 사용 중인지 확인합니다. 리디렉션 URL을 입력하고 **저장**을 클릭합니다.

	![][0]

	>[AZURE.NOTE]기존 Microsoft 계정 앱 등록의 경우 먼저 **강화된 리디렉션 보안**을 사용하도록 설정해야 할 수도 있습니다.

4. **앱 설정**을 클릭하고 **클라이언트 ID** 및 **클라이언트 암호** 값을 기록해 둡니다.

    > [AZURE.NOTE]클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 클라이언트 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.


## <a name="secrets"> </a>모바일 앱에 Microsoft 계정 정보 추가

1. 모바일 앱용 Microsoft 계정 설정 블레이드의 [Azure 관리 포털]에 돌아와서 이전에 가져온 클라이언트 ID 및 클라이언트 암호 값을 붙여 넣습니다. 그런 다음 **Save**를 클릭합니다.

    ![][1]

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
 

<!---HONumber=Nov15_HO1-->