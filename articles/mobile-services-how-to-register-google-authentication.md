<properties urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 모바일 서비스에 Google 로그인을 사용하기 위해 앱 등록

이 항목에서는 Google을 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

<div class="dev-callout">
<strong>참고</strong>
<p>이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>으로 이동하십시오.</p>
</div>

1.  <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> 웹 사이트로 이동하고 Google 계정 자격 증명으로 로그인하고 **Create Project** 를 클릭하고 **Project name** 을 제공한 후 **Create** 를 클릭합니다.

    ![][0]

2.  **API & Auth**, **Credentials**, **Create new Client ID**를 차례로 클릭합니다.

    ![][1]

3.  **Web application**을 선택하고 **Authorized JavaScript Origins**에 모바일 서비스 URL을 입력하고 **Authorized Redirect URI**의 생성된 URL을 경로 _/login/google_ 뒤에 추가된 모바일 서비스 URL로 바꾼 후 **Create client ID**를 클릭합니다.

    > [WACOM.NOTE]Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 _signin-google_ 경로를 추가한 것입니다. 예를 들면 다음과 같습니다 <code>https://todolist.azure-mobile.net/signin-google</code>.

    ![][2]

4.  **Client ID for web applications**에 있는 **Client ID** 및 **Client secret**의 값을 기록해 둡니다.

    ![][3]

    <div class="dev-callout">
    <strong>보안 정보</strong>
    <p>클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.</p>
    </div>

이제 모바일 서비스에 클라이언트 ID 및 클라이언트 암호 값을 제공하여 앱에서 Google 로그인을 인증에 사용할 준비가 되었습니다.



  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [0]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
