<properties
	pageTitle="앱 서비스 응용 프로그램에 대해 Facebook 인증을 구성하는 방법"
	description="앱 서비스 응용 프로그램에 대해 Facebook 인증을 구성하는 방법을 알아봅니다."
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

# Facebook 로그인을 사용하도록 응용 프로그램을 구성하는 방법

이 항목에서는 Facebook을 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.

이 토픽의 절차를 완료하려면 검증된 메일 주소와 휴대폰 번호가 포함된 Facebook 계정이 있어야 합니다. 새 Facebook 계정을 만들려면 [facebook.com]으로 이동하세요.

## <a name="register"> </a>Facebook을 사용하여 응용 프로그램 등록

1. [Facebook 개발자] 웹 사이트로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.

2. (옵션) 아직 등록하지 않은 경우 **Apps**, **Register as a Developer**를 차례로 클릭하고 정책에 동의한 후 등록 단계를 따릅니다.

3. **Apps**를 클릭한 후 **Create a New App**을 클릭합니다.

4. 플랫폼으로 **Website**를 선택합니다. 앱의 고유한 이름을 선택하고 **Create New Facebook App ID**를 클릭합니다.

5. 드롭다운에서 응용 프로그램의 범주를 선택합니다. 그런 다음 **Create App ID**를 클릭합니다.

6. 다음 페이지의 오른쪽 위에서 **Skip Quick Start**를 선택합니다. 응용 프로그램에 대한 개발자 대시보드로 이동됩니다.

7. **App Secret** 필드에서 **Show**를 클릭하고 요청될 경우 암호를 제공한 다음 **App ID** 및 **App Secret** 값을 적어 둡니다.

	> [AZURE.NOTE]**보안 정보** 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.

8. 왼쪽 탐색 모음에서 **Settings**를 클릭합니다. **App Domains**에 게이트웨이의 URL을 입력하고 **Contact Email**을 입력합니다.

    ![][0]

9. 아래에 웹 사이트 섹션이 표시되지 않는 경우 **Add Platform**을 클릭하고 **Website**를 선택합니다. **Site URL**에 앱 서비스 게이트웨이의 URL을 입력하고 **Save Changes**를 클릭합니다.

10. **Advanced** 탭을 클릭하고 **Valid OAuth redirect URIs**에 리디렉션 URI를 추가합니다. 그런 다음 **Save Changes**를 클릭합니다. 리디렉션 URI는 앞에 _/signin-facebook_ 경로가 추가된 게이트웨이 URL입니다. 예: `https://contosogateway.azurewebsites.net/signin-facebook` HTTPS 체계를 사용 중인지 확인합니다.

11. 응용 프로그램을 등록하는 데 사용된 Facebook 계정이 앱의 관리자입니다. 지금은 관리자만 이 응용 프로그램에 로그인할 수 있습니다. 다른 Facebook 계정을 인증하려면 왼쪽 탐색 모음에서 **Status & Review**를 클릭합니다. 그런 다음 **Yes**를 클릭하여 일반 공용 액세스를 사용하도록 설정합니다.


## <a name="secrets"> </a>모바일 앱에 Facebook 정보 추가


12. [Azure 관리 포털]에 로그온한 다음 앱 서비스 게이트웨이로 이동합니다.

13. **설정**에서 **ID**를 선택한 다음 **Facebook**을 선택합니다. 앞에서 얻은 App ID 및 App Secret 값을 붙여넣습니다. 그런 다음 **Save**를 클릭합니다.

    ![][1]

이제 앱에서 Facebook을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-settings.png

<!-- URLs. -->
[Facebook 개발자]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /ko-kr/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 관리 포털]: https://portal.azure.com/
 

<!---HONumber=62-->