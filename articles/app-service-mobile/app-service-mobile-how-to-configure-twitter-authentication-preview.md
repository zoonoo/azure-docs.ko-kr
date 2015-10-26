<properties
	pageTitle="앱 서비스 응용 프로그램에 대해 Twitter 인증을 구성하는 방법"
	description="앱 서비스 응용 프로그램에 대해 Twitter 인증을 구성하는 방법을 알아봅니다."
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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Twitter 로그인을 사용하도록 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

이 항목에서는 Twitter를 인증 공급자로 사용하도록 Azure 모바일 앱을 구성하는 방법을 보여 줍니다.

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Twitter 계정이 있어야 합니다. 새 Twitter 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>으로 이동합니다.

## <a name="register"> </a>Twitter를 사용하여 응용 프로그램 등록


1. [Azure 관리 포털]에 로그온한 다음 모바일 앱으로 이동합니다. **URL**을 복사합니다. Twitter 앱을 구성하는 데 사용합니다.

2. **설정**, **사용자 인증** 및 **Twitter**를 차례로 클릭합니다. **콜백 URL**을 복사합니다. Twitter 앱을 구성하는 데 사용합니다.

3. [Twitter 개발자] 웹 사이트로 이동하고 Twitter 계정 자격 증명을 사용하여 로그인한 다음 **Create New App**을 클릭합니다.

4. 새 앱에 대한 **이름** 및 **설명**을 입력합니다. **웹사이트** 값에 대한 **모바일 앱 URL**을 붙여 넣습니다. 그런 다음에 **콜백 URL**에 앞서 복사한 **콜백 URL**을 붙여 넣습니다. 이는 _/signin-twitter_ 경로를 사용하여 추가된 모바일 앱 게이트웨이입니다. 예: `https://contosogateway.azurewebsites.net/signin-twitter`. HTTPS 체계를 사용 중인지 확인합니다.

    ![][0]

3.  페이지 맨 아래에서 사용 약관을 읽고 동의합니다. 그런 다음 **Create your Twitter application**을 클릭합니다. 앱이 등록되고 응용 프로그램 세부 정보가 표시됩니다.

4. **Settings** 탭을 클릭하고 **Allow this application to be used to sign in with Twitter**를 선택한 다음 **Update Settings**를 클릭합니다.

5. **Keys and Access Tokens** 탭을 선택합니다. **Consumer Key (API Key)** 및 **Consumer secret (API Secret)**의 값을 적어 둡니다.

    > [AZURE.NOTE]소비자 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.


## <a name="secrets"> </a>모바일 앱에 Twitter 정보 추가

1. 모바일 앱용 Twitter 설정 블레이드의 [Azure 관리 포털]에 돌아와서 이전에 가져온 API 키 및 API 암호 값을 붙여 넣습니다. 그런 다음 **Save**를 클릭합니다.

    ![][1]

이제 앱에서 Twitter를 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter 개발자]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure 관리 포털]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=Oct15_HO3-->