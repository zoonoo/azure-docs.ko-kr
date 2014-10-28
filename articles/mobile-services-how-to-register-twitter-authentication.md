<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 모바일 서비스에 Twitter 로그인을 사용하기 위해 앱 등록

이 항목에서는 Twitter를 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

<div class="dev-callout">

**참고**
이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Twitter 계정이 있어야 합니다. 새 Twitter 계정을 만들려면 [twitter.com][twitter.com]으로 이동하십시오.

</div>

1.  Navigate to the [Twitter Developers][Twitter Developers] website, sign-in with your Twitter account credentials, and click **Create a new application**.

    ![][]

2.  Type the **Name**, **Description**, and **Website** values for your app, then type the URL of your mobile service appended with the path */login/twitter* in **Callback URL**.

    > [WACOM.NOTE]For a .NET backend mobile service published to Azure by using Visual Studio, the redirect URL is the URL of your mobile service appended with the path *signin-twitter* your mobile service as a .NET service, such as `https://todolist.azure-mobile.net/signin-twitter`.

    ![][1]

3.  페이지 아래쪽에 있는 약관을 읽고 동의한 후 올바른 CAPTCHA 단어를 입력하고 **Create your Twitter application**을 클릭합니다.

    ![][2]

    앱이 등록되고 응용 프로그램 세부 정보가 표시됩니다.

4.  **Consumer key** 및 **Consumer secret**의 값을 기록해 둡니다.

    ![][3]

    <div class="dev-callout">

    **보안 정보**
    소비자 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

    </div>

5.  **Settings** 탭을 클릭하고 아래로 스크롤하여 **Allow this application to be used to sign in with Twitter**를 선택한 후 **Update this Twitter application's settings**를 클릭합니다.

    ![][4]

이제 모바일 서비스에 소비자 키 및 소비자 암호 값을 제공하여 앱에서 Twitter 로그인을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [twitter.com]: http://go.microsoft.com/fwlink/p/?LinkID=268287
  [Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  []: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
  [1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
  [2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
  [3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
  [4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png
