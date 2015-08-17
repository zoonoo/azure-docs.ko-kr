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
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Google 로그인을 사용하도록 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

이 항목에서는 Google을 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.

이 항목의 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동하세요.

## <a name="register"> </a>Google을 사용하여 응용 프로그램 등록

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google API</a> 웹 사이트로 이동하고 Google 계정 자격 증명으로 로그인한 다음 **Create Project**를 클릭합니다. **Project name**을 제공하고 **Create**를 클릭합니다.

2. 왼쪽 탐색 모음에서 **API & Auth**를 클릭합니다. 그런 다음 **Consent screen**을 클릭합니다. **Email Address**를 선택하고 **Product Name**을 입력합니다. 그런 다음 **Save**를 클릭합니다.

3. 또한 **API & Auth**에서 **APIs**를 선택하고 **Google+ API**를 사용하도록 설정합니다.

4. 다시 **API & Auth**에서 **Credentials**를 선택한 다음 **Create new Client ID**를 선택합니다.

5. **Web application**을 선택합니다. **Authorized JavaScript Origins**에 앱 서비스 게이트웨이 URL을 입력한 다음 **Authorized Redirect URI**에 생성된 URL을 앞에 _/signin-google_ 경로가 추가된 게이트웨이 URL로 바꿉니다. 예: `https://contoso.azurewebsites.net/signin-google` HTTPS 체계를 사용 중인지 확인합니다. 그런 다음 **Create client ID**를 클릭합니다.

     ![][0]

6. 다음 화면에서 **Client ID for web applications** 아래에 있는 **Client ID** 및 **Client secret** 값을 적어 둡니다.

    > [AZURE.IMPORTANT]클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.


## <a name="secrets"> </a>모바일 앱에 Google 정보 추가

7. [Azure 관리 포털]에 로그온한 다음 앱 서비스 게이트웨이로 이동합니다.

8. **설정**에서 **ID**를 선택한 다음 **Google**을 선택합니다. 앞에서 얻은 Client ID 및 Client secret 값을 붙여넣습니다. 그런 다음 **Save**를 클릭합니다.

     ![][1]


이제 앱에서 Google을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 관리 포털]: https://portal.azure.com/
 

<!---HONumber=August15_HO6-->