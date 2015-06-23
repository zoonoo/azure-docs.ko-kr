<properties 
	pageTitle="Azure Active Directory 인증 등록 - 모바일 서비스" 
	description="모바일 서비스 응용 프로그램에서 Azure Active Directory 인증을 위해 등록하는 방법에 대해 알아봅니다." 
	authors="wesmc7777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="wesmc"/>

# Azure Active Directory 계정 로그인 사용을 위한 앱 등록
## 개요


이 항목에서는 Azure 모바일 서비스를 위한 인증 공급자로 Azure Active Directory를 사용할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

## 앱 등록

>[AZURE.NOTE]이 항목에 설명된 단계는 앱에서 [서비스 지향 로그인 작업](http://msdn.microsoft.com/library/azure/dn283952.aspx)을 사용할 때 [모바일 서비스 앱에 인증 추가](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md) 자습서와 함께 사용하기 위해 작성되었습니다. 또는 앱이 Azure Active Directory 및 .NET 백 엔드 서비스에 대해 [클라이언트에서 지시하는 로그인 작업](http://msdn.microsoft.com/library/azure/jj710106.aspx)을 요구하는 경우 [Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증](mobile-services-windows-store-dotnet-adal-sso-authentication.md) 자습서부터 시작해야 합니다.


1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

    ![][1]

2. 모바일 서비스의 **ID** 탭을 클릭합니다.

    ![][2]

3. **Azure active directory** ID 공급자 섹션까지 아래로 스크롤하여 나열된 **앱 URL**을 복사합니다.

    ![][3]

4. 관리 포털에서 **Active Directory**로 이동한 다음 디렉터리를 클릭합니다.

    ![][4]

5. 맨 위에 있는 **응용 프로그램** 탭을 클릭한 다음 앱을 클릭하여 **추가**합니다.

    ![][10]

6. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

7. 응용 프로그램 추가 마법사에서 응용 프로그램의 **이름**을 입력하고 **웹 응용 프로그램 및/또는 웹 API** 유형을 클릭합니다. 계속하려면 클릭합니다.

    ![][5]

8. **로그온 URL** 상자에 모바일 서비스의 Active Directory ID 공급자 설정에서 복사한 앱 ID를 붙여넣습니다. **앱 ID URI** 상자에 동일한 URI를 입력합니다. 계속하려면 클릭합니다.
 
    ![][6]


9. 응용 프로그램이 추가되면 **구성** 탭을 클릭합니다. 그런 다음 앱에 대한 **클라이언트 ID**를 클릭하여 복사합니다.

    모바일 서비스에 .Net 백 엔드를 사용하도록 모바일 서비스를 만든 경우에는 경로 **signin-aad**가 뒤에 추가되는 모바일 서비스의 URL이 되도록 **Single Sign-on** 아래의 _회신 URL_도 편집합니다. 예: `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. 모바일 서비스의 **ID** 탭으로 돌아갑니다. 맨 아래에 있는 Azure Active Directory ID 공급자에 대한 **클라이언트 ID** 설정에 붙여넣습니다.

  
11. **허용되는 테넌트** 목록에서 응용 프로그램을 등록한 디렉터리의 도메인을 추가해야 합니다(예: contoso.onmicrosoft.com). Active Directory의 **도메인** 탭을 클릭하면 기본 도메인 이름을 확인할 수 있습니다.

    ![][11]
 
    도메인 이름을 **허용되는 테넌트** 목록에 추가한 후 **저장**을 클릭합니다.


    ![][9]



이제 앱에서 인증하는 데 Azure Active Directory를 사용할 준비가 되었습니다.



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Azure 관리 포털]: https://manage.windowsazure.com/


<!--HONumber=54--> 