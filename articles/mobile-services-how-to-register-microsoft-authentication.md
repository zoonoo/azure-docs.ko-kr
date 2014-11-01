<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Microsoft 계정 로그인을 사용하도록 앱 등록

이 항목에서는 Azure 모바일 서비스를 위한 인증 공급자로 Live Connect를 사용할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

> [WACOM.NOTE]Windows 8.1 또는 Windows Phone 8.1 앱에서 Microsoft 계정 인증을 사용하도록 구성하려면 [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록][Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]을 참조하십시오.

1.  Live Connect 개발자 센터의 [내 응용 프로그램][내 응용 프로그램] 페이지로 이동하고 필요한 경우 Microsoft 계정으로 로그온합니다.

2.  **응용 프로그램 만들기**를 클릭한 후 **응용 프로그램 이름**을 입력하고 **I accept**를 클릭합니다.

    ![][]

    이를 통해 응용 프로그램이 Live Connect에 등록됩니다.

3.  **API 설정**을 클릭하고 **리디렉션 URL**에 `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` 값을 제공하고 **저장**을 클릭합니다.

    > [WACOM.NOTE]Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 *signin-microsoft* 경로를 추가한 것입니다. 예를 들면 다음과 같습니다. <code>https://todolist.azure-mobile.net/signin-microsoft</code>.

    ![][1]

    이렇게 하면 앱에 대해 Microsoft 계정 인증이 사용하도록 설정됩니다.

    > [WACOM.NOTE]기존 Live Connect 앱 등록의 경우 먼저 **강화된 리디렉션 보안**을 사용하도록 설정해야 할 수도 있습니다.

4.  **앱 설정**을 클릭하고 **클라이언트 ID** 및 **클라이언트 암호** 값을 기록해 둡니다.

    ![][2]

    <div class="dev-callout"><b>보안 정보</b>
<p>클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 클라이언트 암호를 공유하거나 앱과 함께 배포하지 마십시오.</p>
</div>

이제 모바일 서비스에 클라이언트 ID 및 클라이언트 암호 값을 제공하여 앱에서 Microsoft 계정을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]: /ko-kr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  []: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
