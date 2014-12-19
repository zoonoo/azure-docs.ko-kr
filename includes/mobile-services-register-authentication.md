

사용자를 인증할 수 있으려면 ID 공급자에 앱을 등록해야 합니다. 그런 다음 공급자가 생성한 클라이언트 암호를 모바일 서비스에 등록해야 합니다.

1. [Azure 관리 포털]에 로그인하고 **모바일 서비스**를 클릭한 후 모바일 서비스를 클릭합니다.

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2. **대시보드** 탭을 클릭하고 **모바일 서비스 URL** 값을 기록해 둡니다.

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    앱을 등록할 때 이 값을 ID 공급자에게 제공해야 할 수도 있습니다.

3. 아래 목록에서 지원되는 ID 공급자를 선택하고 해당 공급자에 앱을 등록하는 단계를 따릅니다.

 - <a href="/ko-kr/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Microsoft 계정</a>
 - <a href="/ko-kr/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook 로그인</a>
 - <a href="/ko-kr/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter 로그인</a>
 - <a href="/ko-kr/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google 로그인</a>
 - <a href="/ko-kr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    공급자가 생성한 클라이언트 ID 및 암호 값을 기록해 두어야 합니다.

    <div class="dev-callout"><b>보안 정보</b>
	<p>공급자가 생성한 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.</p>
    </div>

4. 관리 포털로 돌아가서 **ID** 탭을 클릭하고 ID 공급자로부터 받은 앱 ID 및 공유 암호 값을 입력한 후 **저장**을 클릭합니다.

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

	이제 모바일 서비스와 앱이 선택한 인증 공급자를 사용하도록 둘 다 구성되었습니다.

<!-- URLs. -->
[Azure 관리 포털]: https://manage.windowsazure.com/
