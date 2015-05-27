

사용자를 인증하려면 ID 공급자에 앱을 등록한 다음 공급자가 생성한 클라이언트 자격 증명을 Azure 모바일 서비스에 등록합니다.

1. Azure 관리 포털에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

2. **대시보드** 탭을 클릭하고 **모바일 서비스 URL** 값을 기록해 둡니다. 앱을 등록할 때 이 값을 ID 공급자에게 제공해야 할 수도 있습니다.

3. 아래 목록에서 지원되는 ID 공급자를 선택합니다. 단계에 따라 해당 공급자에 앱을 등록합니다. 공급자가 생성한 클라이언트 ID 및 암호 값을 기록해 두어야 합니다.

 - <a href="/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Microsoft 계정</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google </a>
 - <a href="/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

    > [AZURE.IMPORTANT]공급자가 생성한 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

4. 관리 포털로 돌아가서 **ID** 탭을 클릭하고 ID 공급자로부터 받은 앱 ID 및 공유 암호 값을 입력한 후 **저장**을 클릭합니다. 이제 모바일 서비스와 앱이 선택한 인증 공급자를 사용하도록 둘 다 구성되었습니다.

    > [AZURE.IMPORTANT]ID 공급자의 개발자 사이트에서 올바른 리디렉션 URI를 설정했는지 확인합니다. 위의 각 공급자에 대한 링크된 지침에 설명된 대로, 리디렉션 URI는 .NET 백 엔드 서비스와 JavaScript 백 엔드 서비스가 서로 다를 수 있습니다. 리디렉션 URI가 잘못 구성되면 로그인 화면이 제대로 표시되지 않고 앱이 예상치 못한 방식으로 잘못 작동할 수 있습니다.

5. (선택 사항) 3단계 및 4단계를 반복하여 앱에서 지원할 다른 ID 공급자를 구성합니다.

<!--HONumber=54-->