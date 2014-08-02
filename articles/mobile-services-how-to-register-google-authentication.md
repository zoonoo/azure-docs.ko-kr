<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Register your apps for Google login with Mobile Services" authors="" solutions="" manager="" editor="" />

모바일 서비스에 Google 로그인을 사용하기 위해 앱 등록
=====================================================

이 항목에서는 Google을 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

**참고**

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하십시오.

1.  [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 웹 사이트로 이동하고 Google 계정 자격 증명을 사용하여 로그인한 후 **Create project...**를 클릭합니다.

    ![][1]   

2.  **API Access**를 클릭한 후 **Create an OAuth 2.0 client ID...**를 클릭합니다.

    ![][2]

3.  **Branding Information**에서 **Product name**을 입력한 후 **Next**를 클릭합니다.

    ![][3]

4.  **Client ID Settings** 아래에 있는 **Web application**을 선택하고 **Your site or hostname**에 모바일 서비스 URL을 입력한 후 **more options**를 클릭합니다. **Authorized Redirect URIs**에 있는 생성된 URL을 */login/google* 경로가 추가된 모바일 서비스 URL로 바꾼 다음 **Create client ID**를 클릭합니다.

    ![][4]

    > [WACOM.NOTE] Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 *signin-google* 경로를 추가한 것입니다(예: `https://todolist.azure-mobile.net/signin-google`).

5.  **Client ID for web applications**에 있는 **Client ID** 및 **Client secret**의 값을 기록해 둡니다.

    ![][5]

    **보안 정보**

    클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

이제 모바일 서비스에 클라이언트 ID 및 클라이언트 암호 값을 제공하여 앱에서 Google 로그인을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
