

사용자를 인증할 수 있으려면 ID 공급자에 앱을 등록해야 합니다. 그런 다음 공급자가 생성한 클라이언트 암호를 앱 서비스에 등록해야 합니다.

1. [Azure Preview 포털]에 로그온하여 **찾아보기**, **리소스 그룹**을 차례로 클릭한 후 모바일 앱의 리소스 그룹을 선택합니다.

2. 게이트웨이를 선택하고 **속성** 아래의 **URL** 값을 적어 둡니다. 앱을 등록할 때 이 값을 ID 공급자에게 제공해야 할 수도 있습니다.

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. 아래 목록에서 지원되는 ID 공급자를 선택하고 해당 공급자에 앱을 구성하는 단계를 따릅니다.

 - <a href="/ko-kr/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Azure Active Directory</a>
 - <a href="/ko-kr/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Facebook 로그인</a>
 - <a href="/ko-kr/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Google 로그인</a>
 - <a href="/ko-kr/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Microsoft 계정</a>
 - <a href="/ko-kr/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Twitter 로그인</a>

	이제 선택한 인증 공급자와 작동하도록 응용 프로그램이 구성되었습니다.

4. (선택 사항) 이전 단계를 반복하여 앱에서 지원할 다른 ID 공급자를 구성합니다. 

<!-- URLs. -->
[Azure Preview 포털]: https://portal.azure.com/

<!---HONumber=July15_HO4-->