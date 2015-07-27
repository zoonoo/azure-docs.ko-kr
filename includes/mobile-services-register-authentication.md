
먼저, 해당 사이트에서 ID 공급자에 앱을 등록한 다음 모바일 서비스에서 자격 증명을 설정해야 합니다.

1. [Azure 관리 포털]에서 모바일 서비스로 이동하여 **대시보드**를 클릭하고 **모바일 서비스 URL** 값을 기록합니다.

2. 다음 지원되는 ID 공급자 중 하나에 앱을 등록합니다.

	* [Google](mobile-services-how-to-register-google-authentication.md)
	* [Facebook](mobile-services-how-to-register-facebook-authentication.md)
	* [Twitter](mobile-services-how-to-register-twitter-authentication.md)
	* [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
	* [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md)  
	
    >[AZURE.IMPORTANT]ID 공급자의 개발자 사이트에서 모바일 서비스에 대한 리디렉션 URI를 올바르게 설정해야 합니다. 위의 각 공급자에 대한 링크된 지침에 설명된 대로, 리디렉션 URL의 경로는 .NET 백 엔드 모바일 서비스(`/signin-<provider>`)와 JavaScript 백 엔드 서비스(`/login/<provider>`)가 서로 다릅니다. 리디렉션 URI를 잘못 구성하면 클라이언트에서 앱에 로그인할 수 없게 됩니다. <br/>클라이언트 암호를 공유하거나 배포하지 마세요.

3. [Azure 관리 포털]의 모바일 서비스로 돌아가 **ID** 탭을 클릭하고 ID 공급자로부터 얻은 앱 ID 및 암호 값을 입력합니다.

인증에 하나의 ID 공급자를 지원하도록 앱과 모바일 서비스를 둘 다 구성했으므로 이제 다음 단계를 반복하여 추가 ID 공급자를 지원할 수 있습니다.

[Azure 관리 포털]: https://manage.windowsazure.com/

<!---HONumber=July15_HO3-->