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
	ms.date="10/29/2015"
	ms.author="mahender"/>

# Facebook 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 항목에서는 Facebook을 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.

이 토픽의 절차를 완료하려면 검증된 전자 메일 주소와 휴대폰 번호가 포함된 Facebook 계정이 있어야 합니다. 새 Facebook 계정을 만들려면 [facebook.com]으로 이동하세요.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Facebook을 사용하여 응용 프로그램 등록

1. [Azure 관리 포털]에 로그인한 다음 응용 프로그램으로 이동합니다. **URL**을 복사합니다. Facebook 앱을 구성하는 데 사용합니다.
 
2. 다른 브라우저 창에서 [Facebook 개발자] 웹 사이트로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.

3. (옵션) 아직 등록하지 않은 경우 **Apps**, **Register as a Developer**를 차례로 클릭하고 정책에 동의한 후 등록 단계를 따릅니다.

4. **내 앱**을 클릭한 후 **새 앱 추가**를 클릭합니다.

5. 플랫폼으로 **Website**를 선택합니다. 앱의 고유한 이름을 선택하고 **Create New Facebook App ID**를 클릭합니다.

6. 드롭다운에서 응용 프로그램의 범주를 선택합니다. 그런 다음 **Create App ID**를 클릭합니다.

7. 다음 페이지의 오른쪽 위에서 **Skip Quick Start**를 선택합니다. 응용 프로그램에 대한 개발자 대시보드로 이동됩니다.

8. **App Secret** 필드에서 **Show**를 클릭하고 요청될 경우 암호를 제공한 다음 **App ID** 및 **App Secret** 값을 적어 둡니다. 나중에 이를 사용하도록 응용 프로그램을 구성합니다.

	> [AZURE.NOTE]**보안 정보** 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.

9. 왼쪽 탐색 모음에서 **Settings**를 클릭합니다. **앱 도메인**에 모바일 앱의 **URL**을 입력하고 **전자 메일 연락처**를 입력합니다.

    ![][0]

10. 아래에 웹 사이트 섹션이 표시되지 않는 경우 **Add Platform**을 클릭하고 **Website**를 선택합니다. **사이트 URL** 필드에 모바일 앱의 **URL**을 입력하고 **변경 내용 저장**을 클릭합니다.

11. **고급** 탭을 클릭하고 응용 프로그램의 **리디렉션 URI**를 **유효한 OAuth 리디렉션 URI**에 추가합니다. 그런 다음 **Save Changes**를 클릭합니다. 리디렉션 URI는 경로 _/.auth/login/facebook/callback_이 추가된 응용 프로그램의 URL입니다. 예: `https://contoso.azurewebsites.net/.auth/login/facebook/callback` HTTPS 체계를 사용 중인지 확인합니다.


	> [AZURE.NOTE]앱 서비스 인증 / 권한 부여 기능 대신 앱 서비스 게이트웨이를 사용 중인 경우 리디렉션 URL은 _/signin-facebook_ 경로가 있는 게이트웨이 URL을 대신 사용합니다.


12. 응용 프로그램을 등록하는 데 사용된 Facebook 계정이 앱의 관리자입니다. 지금은 관리자만 이 응용 프로그램에 로그인할 수 있습니다. 다른 Facebook 계정을 인증하려면 왼쪽 탐색 모음에서 **Status & Review**를 클릭합니다. 그런 다음 **Yes**를 클릭하여 일반 공용 액세스를 사용하도록 설정합니다.


## <a name="secrets"> </a>응용 프로그램에 Facebook 정보 추가


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Facebook**. Paste in the values you obtained earlier and click **Save**.


13. [Azure 관리 포털]로 돌아가서 응용 프로그램으로 이동합니다. **설정**을 클릭한 다음 **인증/권한 부여**를 클릭합니다.

14. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **설정**으로 전환합니다.

15. **Facebook**을 클릭합니다. 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣고 필요에 따라 응용 프로그램에 필요한 범위를 설정합니다. 그런 후 **OK**를 클릭합니다.

    ![][1]
	
16. 기본적으로 앱 서비스는 로그인을 제공하지만 사이트 콘텐츠 및 API에 대한 액세스를 제한하지 않습니다. 이는 앱 코드에서 처리합니다. Facebook 로그인으로 사이트를 완전히 보호하려면 **Facebook** 옵션을 사용하도록 **요청이 인증되지 않은 경우 수행할 동작** 드롭다운을 변경합니다. 이렇게 하려면 모든 요청이 인증되어야 합니다. 인증되지 않은 요청은 Facebook으로 로그인하도록 리디렉션됩니다.

17. **Save**를 클릭합니다.


이제 앱에서 Facebook을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 개발자]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /ko-KR/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 관리 포털]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->