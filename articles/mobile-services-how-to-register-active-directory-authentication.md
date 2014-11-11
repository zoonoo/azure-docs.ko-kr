<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Azure Active Directory 계정 로그인 사용을 위한 앱 등록

이 항목에서는 Azure 모바일 서비스를 위한 인증 공급자로 Azure Active Directory를 사용할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

> [WACOM.NOTE] Azure Active Directory를 사용하여 SSO(Single Sign-On)를 위해 클라이언트 기반 인증을 제공하려면 [Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증][Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증] 자습서를 참조하세요.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

    ![][0]

2.  모바일 서비스의 **ID** 탭을 클릭합니다.

    ![][1]

3.  **Azure active directory** ID 공급자 섹션까지 아래로 스크롤하여 나열된 **앱 URL**을 복사합니다.

    ![][2]

4.  관리 포털에서 **Active Directory**로 이동한 다음 디렉터리를 클릭합니다.

    ![][3]

5.  맨 위에 있는 **응용 프로그램** 탭을 클릭한 다음 앱을 클릭하여 **추가**합니다.

    ![][4]

6.  **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

7.  응용 프로그램 추가 마법사에서 응용 프로그램의 **이름**을 입력하고 **웹 응용 프로그램 및/또는 웹 API** 유형을 클릭합니다. 계속하려면 클릭합니다.

    ![][5]

8.  **로그온 URL** 상자에 모바일 서비스의 Active Directory ID 공급자 설정에서 복사한 앱 ID를 붙여넣습니다. **앱 ID URI** 상자에 동일한 URI를 입력합니다. 계속하려면 클릭합니다.

    ![][6]

9.  응용 프로그램이 추가되면 **구성** 탭을 클릭합니다. 그런 다음 앱에 대한 **클라이언트 ID**를 클릭하여 복사합니다.

    모바일 서비스에 .Net 백 엔드를 사용하도록 모바일 서비스를 만든 경우에는 경로 *signin-aad*가 뒤에 추가되는 모바일 서비스의 URL이 되도록 **Single Sign-on** 아래의 **회신 URL**도 편집합니다. 예를 들면 `https://todolist.azure-mobile.net/signin-aad`와 같습니다.

    ![][7]

10. 모바일 서비스의 **ID** 탭으로 돌아갑니다. 맨 아래에 있는 Azure Active Directory ID 공급자에 대한 **클라이언트 ID** 설정에 붙여넣습니다.

11. **허용되는 테넌트** 목록에서 응용 프로그램을 등록한 디렉터리의 도메인을 추가해야 합니다(예: contoso.onmicrosoft.com). Active Directory의 **도메인** 탭을 클릭하면 기본 도메인 이름을 확인할 수 있습니다.

    ![][8]

    도메인 이름을 **허용되는 테넌트** 목록에 추가한 후 **저장**을 클릭합니다.

    ![][9]

이제 앱에서 인증하는 데 Azure Active Directory를 사용할 준비가 되었습니다.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
  [2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
  [3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
  [4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
  [5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
  [6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
  [7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
  [8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png
  [9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
