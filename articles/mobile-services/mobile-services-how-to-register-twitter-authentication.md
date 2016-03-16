<properties
	pageTitle="Twitter 인증 등록 | Microsoft Azure"
	description="Azure 모바일 서비스 응용 프로그램에서 Twitter 인증을 사용하는 방법에 대해 알아봅니다."
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
	ms.date="02/07/2016" 
	ms.author="glenga"/>

#모바일 서비스에서 Twitter 로그인하기 위해 앱 등록

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

이 항목에서는 Twitter를 사용하여 Azure 모바일 서비스에 인증할 수 있도록 앱을 등록하는 방법을 보여 줍니다.

>[AZURE.NOTE] 이 자습서에서는 플랫폼에 구애받지 않는 확장 가능한 모바일 응용 프로그램을 손쉽게 빌드할 수 있도록 하는 솔루션인 [Azure 모바일 서비스](https://azure.microsoft.com/services/mobile-services/)에 대해 다룹니다. 모바일 서비스를 사용하면 데이터 동기화, 사용자 인증, 푸시 알림 보내기 작업을 간편하게 수행할 수 있습니다. 이 페이지는 [앱에 인증 추가](mobile-services-ios-get-started-users.md) 자습서를 보완하는 역할을 하며, 사용자를 앱에 로그인하는 방법을 설명합니다. 모바일 서비스를 처음 사용하는 경우 [모바일 서비스 시작](mobile-services-ios-get-started.md) 자습서를 완료하는 것이 좋습니다.

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Twitter 계정이 있어야 합니다. 새 Twitter 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>으로 이동합니다.

1. [Twitter 개발자](http://go.microsoft.com/fwlink/p/?LinkId=268300) 웹 사이트로 이동하고 Twitter 계정 자격 증명을 사용하여 로그인한 다음 **Create New App**을 클릭합니다.

2. 앱의 **Name**, **Description** 및 **Website** 값을 입력한 다음 **Callback URL**에 다음 URL 형식 중 하나를 입력합니다.

	+ **.NET 백 엔드**: `https://<mobile_service>.azure-mobile.net/signin-twitter`
	+ **JavaScript 백 엔드**: `https://<mobile_service>.azure-mobile.net/login/twitter`

	 >[AZURE.NOTE]모바일 서비스 백 엔드 유형에 맞는 리디렉션 URL 경로 형식을 사용하고 있는지 확인합니다. 형식이 올바르지 않으면 인증이 실패합니다. &nbsp;

   	![][2]

3.  페이지 아래쪽에 있는 약관을 읽고 동의한 후 **Create your Twitter application**을 클릭합니다.

   	앱이 등록되고 응용 프로그램 세부 정보가 표시됩니다.

6. 앱 대시보드에서 **Keys and Access Tokens** 탭을 클릭하고 **Consumer key** 및 **Consumer secret** 값을 기록해 둡니다.

    > [AZURE.NOTE] 소비자 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

7. **설정** 탭을 클릭하고 아래로 스크롤하여 **Twitter로 로그인하는 데 이 응용 프로그램을 사용하도록 허용** 확인란이 선택되어 있는지 확인한 후 **설정 업데이트**를 클릭합니다.

이제 모바일 서비스에 소비자 키 및 소비자 암호 값을 제공하여 앱에서 Twitter 로그인을 인증에 사용할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_0211_2016-->