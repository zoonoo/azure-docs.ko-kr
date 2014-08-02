<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="" documentationCenter="" title="Register your apps for Twitter login with Mobile Services" authors="" solutions="" manager="" editor="" />

모바일 서비스에 Twitter 로그인을 사용하기 위해 앱 등록
======================================================

이 항목에서는 Twitter를 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

**참고**

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Twitter 계정이 있어야 합니다. 새 Twitter 계정을 만들려면 [twitter.com](http://go.microsoft.com/fwlink/p/?LinkID=268287)으로 이동하십시오.

1.  [Twitter 개발자](http://go.microsoft.com/fwlink/p/?LinkId=268300)(영문) 웹 사이트로 이동하고 Twitter 계정 자격 증명을 사용하여 로그인한 후 **Create a new application**을 클릭합니다.

    ![][1]

2.  앱의 **Name**, **Description** 및 **Web Site** 값을 입력하고 **Callback URL**에 모바일 서비스의 URL을 입력합니다.

    ![][2]

    > [WACOM.NOTE] Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 *signin-twitter* 경로를 추가한 것입니다(예: `https://todolist.azure-mobile.net/signin-twitter`).
    > **Web Site** 값은 필수이지만 사용되지는 않습니다.

3.  페이지 아래쪽에 있는 약관을 읽고 동의한 후 올바른 CAPTCHA 단어를 입력하고 **Create your Twitter application**을 클릭합니다.

	![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png)
	
	앱이 등록되고 응용 프로그램 세부 정보가 표시됩니다.

4.  **Consumer key** 및 **Consumer secret**의 값을 기록해 둡니다.

    ![][4]

    **보안 정보**

    소비자 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

5.  **Settings** 탭을 클릭하고 아래로 스크롤하여 **Allow this application to be used to sign in with Twitter**를 선택한 후 **Update this Twitter application's settings**를 클릭합니다.

    ![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png)

이제 모바일 서비스에 소비자 키 및 소비자 암호 값을 제공하여 앱에서 Twitter 로그인을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
