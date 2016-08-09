<properties
	pageTitle="Facebook 인증 등록 - Azure 모바일 서비스"
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
	ms.date="07/21/2016"
	ms.author="glenga"/>

# 모바일 서비스에 Facebook 인증을 사용하기 위해 앱 등록

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)] &nbsp;


[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

이 토픽에서는 Facebook을 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다. 이 페이지는 [인증 시작](mobile-services-ios-get-started-users.md) 자습서를 보완하는 역할을 하며, 사용자를 앱에 기록하는 방법을 설명합니다. 모바일 서비스를 처음 사용하는 경우 [모바일 서비스 시작](mobile-services-ios-get-started.md) 자습서를 완료하는 것이 좋습니다.

이 토픽의 절차를 완료하려면 검증된 전자 메일 주소와 휴대폰 번호가 포함된 Facebook 계정이 있어야 합니다. 새 Facebook 계정을 만들려면 [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285)으로 이동하십시오.

1. [Facebook 개발자](http://go.microsoft.com/fwlink/p/?LinkId=268285) 웹 사이트로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.

2. (선택 사항) 아직 등록하지 않은 경우 **My Apps**, **Register as a Developer**를 차례로 클릭하고 정책에 동의한 후 등록 단계를 따릅니다.

3. **My Apps** > **Add a New App** > **Website** > **Skip and Create App ID**를 클릭합니다.

4. **Display Name**에 앱의 고유한 이름을 입력하고 앱에 대한 **Category**를 선택한 다음 **Create App ID**를 클릭하고 보안 검사를 완료합니다. 새 Facebook 앱에 대한 개발자 대시보드로 이동됩니다.

5. **App Secret** 필드에서 **Show**를 클릭하고 요청될 경우 암호를 제공한 다음 **App ID** 및 **App Secret** 값을 적어 둡니다. 나중에 이 값을 사용하여 Azure에서 응용 프로그램을 구성합니다.

	> [AZURE.NOTE] **보안 정보** 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.

5. 왼쪽 탐색 모음에서 **Settings**를 클릭하고, **App Domains**에 모바일 서비스의 도메인을 입력하고, 선택적 **Contact Email**을 입력하고, **Add Platform**을 클릭하고, **Website**를 선택합니다.

   	![][3]

6. **Site URL**에 모바일 서비스의 URL을 입력하고 **Save Changes**를 클릭합니다.

7. **Show**를 클릭하고 요청될 경우 암호를 제공한 후 **App ID** 및 **App Secret** 값을 기록해 둡니다.

   	![][5] &nbsp;

    >[AZURE.IMPORTANT] 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요. &nbsp;

8. **Advanced** 탭을 클릭하고, **Valid OAuth redirect URIs**에 다음 URL 형식 중 하나를 입력한 다음 **Save Changes**를 클릭합니다.

	+ **.NET 백 엔드**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **JavaScript 백 엔드**: `https://<mobile_service>.azure-mobile.net/login/facebook`

	 >[AZURE.NOTE]*HTTPS* 체계를 사용하여 모바일 서비스 백 엔드 유형에 맞는 URL 리디렉션 경로 형식을 사용하고 있는지 확인합니다. 형식이 올바르지 않으면 인증이 실패합니다.


12. 응용 프로그램을 등록하는 데 사용된 Facebook 계정이 앱의 관리자입니다. 지금은 관리자만 이 응용 프로그램에 로그인할 수 있습니다. 다른 Facebook 계정을 인증하려면 **App Review**를 클릭하고 **Make todolist-complete-nodejs public**을 사용하도록 설정하여 Facebook 인증을 사용한 일반 공용 액세스를 사용하도록 설정합니다.

이제 모바일 서비스에 앱 ID 및 앱 암호 값을 제공하여 앱에서 Facebook 로그인을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!---HONumber=AcomDC_0727_2016-->