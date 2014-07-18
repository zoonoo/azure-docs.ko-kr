<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스에 Facebook 인증을 사용하기 위해 앱 등록
=====================================================

이 토픽에서는 Facebook을 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

**참고**

이 토픽의 절차를 완료하려면 검증된 전자 메일 주소와 휴대폰 번호가 포함된 Facebook 계정이 있어야 합니다. 새 Facebook 계정을 만들려면 [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285)으로 이동하십시오.

1.  [Facebook 개발자](http://go.microsoft.com/fwlink/p/?LinkId=268286) 웹 사이트로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.

2.  (옵션) 아직 등록하지 않은 경우 **Apps**, **Register as a Developer**를 차례로 클릭하고 정책에 동의한 후 등록 단계를 따릅니다.

	![][0]

3.  **Apps**를 클릭한 후 **Create a New App**을 클릭합니다.

	![][1]

4.  앱의 고유 이름을 선택하고 **Apps for Pages**를 선택한 후 **Create App**을 클릭하고 챌린지 질문에 대답합니다.

	![][2]

    앱이 Facebook에 등록됩니다.

5.  **Settings**를 클릭하고 **App Domains**에 모바일 서비스의 도메인을 입력한 후 **Add Platform**을 클릭하고 **Website**를 선택합니다.

	![][3]

6.  **Site URL**에 모바일 서비스의 URL을 입력하고 **Save Changes**를 클릭합니다.

    ![](./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png)

7.  **Show**를 클릭하고 요청될 경우 암호를 제공한 후 **App ID** 및 **App Secret** 값을 기록해 둡니다.

	![][5]

    **보안 정보**

    앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

이제 모바일 서비스에 앱 ID 및 앱 암호 값을 제공하여 앱에서 Facebook 로그인을 인증에 사용할 준비가 되었습니다.


<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/