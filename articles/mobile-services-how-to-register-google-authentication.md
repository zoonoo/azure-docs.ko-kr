<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 모바일 서비스에 Google 로그인을 사용하기 위해 앱 등록

이 항목에서는 Google을 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

<div class="dev-callout">

**참고**
이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com][accounts.google.com]으로 이동하십시오.

</div>

1.  Navigate to the [Google apis][Google apis] website, sign-in with your Google account credentials, click **Create Project**, provide a **Project name**, then click **Create**.

    ![][0]

2.  Click **API & Auth**, click **Credentials**, then click **Create new Client ID**.

    ![][1]

3.  Select **Web application**, type your mobile service URL in **Authorized JavaScript Origins**, replace the generated URL in **Authorized Redirect URI** with the URL of your mobile service appended with the path */login/google*, and then click **Create client ID**.

    > [WACOM.NOTE]For a .NET backend mobile service published to Azure by using Visual Studio, the redirect URL is the URL of your mobile service appended with the path *signin-google* your mobile service as a .NET service, such as `https://todolist.azure-mobile.net/signin-google`.

    ![][2]

4.  **Client ID for web applications**에 있는 **Client ID** 및 **Client secret**의 값을 기록해 둡니다.

    ![][3]

    <div class="dev-callout">

    **보안 정보**
    클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

    </div>

이제 모바일 서비스에 클라이언트 ID 및 클라이언트 암호 값을 제공하여 앱에서 Google 로그인을 인증에 사용할 준비가 되었습니다.



  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  [0]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
