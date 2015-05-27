<properties 
	pageTitle="Facebook 인증 등록 - 모바일 서비스" 
	description="Azure 모바일 서비스 앱에서 Facebook 인증을 사용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# 모바일 서비스에 Facebook 인증을 사용하기 위해 앱 등록

이 토픽에서는 Facebook을 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

> [AZURE.NOTE]이 자습서에서는 플랫폼에 구애받지 않는 확장 가능한 모바일 응용 프로그램을 손쉽게 빌드할 수 있도록 하는 솔루션인 [Azure 모바일 서비스]에 대해 다룹니다. 모바일 서비스를 사용하면 데이터 동기화, 사용자 인증, 푸시 알림 보내기 작업을 간편하게 수행할 수 있습니다. 이 페이지는 <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">인증 시작</a> 자습서를 보완하는 역할을 하며, 사용자를 앱에 기록하는 방법을 설명합니다. 모바일 서비스를 처음 사용하는 경우 <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">모바일 서비스 시작</a> 자습서를 완료하는 것이 좋습니다.
	
이 토픽의 절차를 완료하려면 검증된 전자 메일 주소와 휴대폰 번호가 포함된 Facebook 계정이 있어야 합니다. 새 Facebook 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>으로 이동하세요.

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Facebook 개발자</a> 웹 사이트로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.

2. (옵션) 아직 등록하지 않은 경우 **Apps**, **Register as a Developer**를 차례로 클릭하고 정책에 동의한 후 등록 단계를 따릅니다.

   	![][0]

3. **Apps**를 클릭한 후 **Create a New App**을 클릭합니다.

   	![][1]

4. 앱의 고유 이름을 선택하고 **Apps for Pages**를 선택한 후 **Create App**을 클릭하고 챌린지 질문에 대답합니다.

   	![][2]

	앱이 Facebook에 등록됩니다.

5. **Settings**를 클릭하고 **App Domains**에 모바일 서비스의 도메인을 입력합니다. 또한 **Contact Email**을 입력한 후 **Add Platform**을 클릭하고 **Website**를 선택합니다.

   	![][3]

6. **Site URL**에 모바일 서비스의 URL을 입력하고 **Save Changes**를 클릭합니다.

	![][4]

7. **Show**를 클릭하고 요청될 경우 암호를 제공한 후 **App ID** 및 **App Secret** 값을 기록해 둡니다.

   	![][5]

	> [AZURE.NOTE]**보안 정보** 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.


8. **Advanced** 탭을 클릭하고 _Valid OAuth redirect URIs_에 경로 **/login/facebook**이 뒤에 추가되는 모바일 서비스의 URL을 입력한 후 **Save Changes**를 클릭합니다.

	> [AZURE.NOTE]Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 _signin-facebook_ 경로를 추가한 것입니다(예: <code>https://todolist.azure-mobile.net/signin-facebook</code>).
	
	![][7]

9. 새 앱을 정의한 Facebook 계정은 앱의 관리자이며 앱에 관리자 권한으로 액세스할 수 있습니다. 다른 Facebook 계정을 인증하려면 앱에 대한 액세스 권한이 있어야 합니다. 이 단계를 수행하면 일반 공용 액세스 권한을 부여받게 되므로 앱에서 다른 Facebook 계정을 인증할 수 있습니다. **Status & Review**를 클릭합니다. 그런 다음 **Yes**를 클릭하여 일반 공용 액세스를 사용하도록 설정합니다.

    ![][6]



이제 모바일 서비스에 앱 ID 및 앱 암호 값을 제공하여 앱에서 Facebook 로그인을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
[6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
[7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[Azure 모바일 서비스]: http://azure.microsoft.com/services/mobile-services/

<!--HONumber=54-->