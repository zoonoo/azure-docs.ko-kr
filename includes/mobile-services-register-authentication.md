
1. Azure 포털 > **모바일 서비스** > 모바일 서비스 > **대시보드**를 클릭하고, **모바일 서비스 URL** 값을 기록합니다.

2. 다음 인증 공급자 중 하나 이상에 앱을 등록합니다.
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md) 
   
   공급자가 생성한 클라이언트 ID 및 클라이언트 암호 값을 기록해 두어야 합니다. 클라이언트 암호를 공유하거나 배포하지 마세요.

3. Azure 포털에서 **모바일 서비스** > 모바일 서비스 > **ID** > ID 공급자 설정을 클릭하고 공급자로부터 받은 클라이언트 ID 및 암호 값을 입력합니다. 
 
이제 모바일 서비스와 앱이 인증 공급자를 사용하도록 둘 다 구성되었습니다. 지원하려는 각 추가 ID 공급자에 대해 이 모든 단계를 선택적으로 반복할 수 있습니다.

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->