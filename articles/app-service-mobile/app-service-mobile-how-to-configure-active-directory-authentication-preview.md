<properties 
	pageTitle="앱 서비스 응용 프로그램에 대해 Azure Active Directory 인증을 구성하는 방법" 
	description="앱 서비스 응용 프로그램에 대해 Azure Active Directory 인증을 구성하는 방법을 알아봅니다." 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="mahender"/>

# Azure Active Directory 로그인을 사용하도록 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

이 항목에서는 Azure Active Directory를 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.

## <a name="register"></a>Azure Active Directory에 응용 프로그램 등록

1. [Preview Azure 관리 포털]에 로그온한 다음 앱 서비스 게이트웨이로 이동합니다.

2. **설정**에서 **ID**를 선택한 다음 **Azure Active Directory**를 선택합니다. **APP URL**을 복사합니다. HTTPS 체계를 사용 중인지 확인합니다.

    ![][1]

3. [Azure 관리 포털]에 로그인한 다음 **Active Directory**로 이동합니다.

    ![][2]

4. 디렉터리를 선택한 다음 맨 위에서 **응용 프로그램** 탭을 선택합니다. 맨 아래에서 **추가**를 클릭하여 새로운 앱 등록을 만듭니다.

5. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

6. 응용 프로그램 추가 마법사에서 응용 프로그램의 **이름**을 입력하고 **웹 응용 프로그램 및/또는 웹 API** 유형을 클릭합니다. 계속하려면 클릭합니다.

7. **로그온 URL** 상자에 게이트웨이의 Active Directory ID 공급자 설정에서 복사한 앱 ID를 붙여넣습니다. **앱 ID URI** 상자에 동일한 URI를 입력합니다. 계속하려면 클릭합니다.

8. 응용 프로그램이 추가되면 **구성** 탭을 클릭합니다. **Single Sign-On** 아래의 **회신 URL**을 앞에 _/signin-aad_ 경로가 추가된 게이트웨이 URL로 편집합니다. 예: `https://contosogateway.azurewebsites.net/signin-aad` HTTPS 체계를 사용 중인지 확인합니다.

    ![][3]

9. **저장**을 클릭합니다. 그런 다음 앱의 **클라이언트 ID**를 복사합니다.

## <a name="secrets"> </a>모바일 앱에 Azure Active Directory 정보 추가

10. Preview 관리 포털 및 게이트웨이에 대한 **사용자 인증** 블레이드로 돌아갑니다. Azure Active Directory ID 공급자에 대한 **클라이언트 ID** 설정을 붙여넣습니다.
  
11. **허용되는 테넌트** 목록에서 응용 프로그램을 등록한 디렉터리의 도메인을 추가해야 합니다(예: contoso.onmicrosoft.com). Azure Active Directory 테넌트의 **도메인** 탭을 클릭하면 기본 도메인 이름을 확인할 수 있습니다. 도메인 이름을 **허용되는 테넌트** 목록에 추가한 후 **저장**을 클릭합니다.

이제 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

Azure Active Directory Single Sign-On으로 모바일 앱의 사용자 인증: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-app-configure.png

<!-- URLs. -->

[Preview Azure 관리 포털]: https://portal.azure.com/
[Azure 관리 포털]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md
 

<!---HONumber=July15_HO4-->