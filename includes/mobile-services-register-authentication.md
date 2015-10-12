
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

> [AZURE.IMPORTANT]ID 공급자의 개발자 사이트에서 올바른 리디렉션 URI를 설정했는지 확인합니다. 위의 각 공급자에 대한 링크된 지침에 설명된 대로, 리디렉션 URI는 .NET 백 엔드 서비스와 JavaScript 백 엔드 서비스가 서로 다를 수 있습니다. 리디렉션 URI가 잘못 구성되면 로그인 화면이 제대로 표시되지 않고 앱이 예상치 못한 방식으로 잘못 작동할 수 있습니다.

<!---HONumber=Oct15_HO1-->