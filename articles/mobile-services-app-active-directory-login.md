<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="dwrede" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="dwrede"></tags>

# Azure Active Directory 계정 로그인 사용을 위한 앱 등록

이 항목에서는 Azure 모바일 서비스를 위한 인증 공급자로 Azure Active Directory를 사용할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

<div class="dev-callout"><b>참고</b>
<p>SSO(Single Sign-On) 또는 Windows 스토어 앱의 푸시 알림을 위해 클라이언트 기반 인증을 제공하려면 Windows 스토어에도 앱을 등록해야 합니다. 자세한 내용은 <a href="/ko-kr/develop/mobile/how-to-guides/register-for-single-sign-on">Windows Live Connect 인증을 위해 Windows 스토어 앱 등록</a>을 참조하십시오.</p>
</div>

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.

2.  관리 포털에서 **Active Directory**로 이동한 다음 디렉터리를 클릭합니다.

  ![][]

1.  **응용 프로그램** 탭을 클릭한 후 **앱 추가**를 클릭합니다.

  ![][1]

1.  새 응용 프로그램 마법사의 지침을 따르고 XXX의 **웹 응용 프로그램 및/또는 웹 API**를 선택합니다. Single Sign-On을 사용하도록 설정합니다. **앱 URL**을 입력하라는 메시지가 나타나면 모바일 서비스 응용 프로그램 URL을 붙여넣습니다.

2.  **내용 추가할 예정**

이제 모바일 서비스에 클라이언트 ID 및 클라이언트 암호 값을 제공하여 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Live Connect 인증을 위해 Windows 스토어 앱 등록]: /ko-kr/develop/mobile/how-to-guides/register-for-single-sign-on
  [Azure 관리 포털]: https://manage.windowsazure.com/
  []: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png
