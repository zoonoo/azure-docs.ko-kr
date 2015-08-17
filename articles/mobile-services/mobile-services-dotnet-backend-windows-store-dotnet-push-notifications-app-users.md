<properties 
	pageTitle="인증된 사용자에게 푸시 알림 보내기(유니버설 Windows 8.1) | Azure 모바일 서비스" 
	description="Azure 모바일 서비스를 사용하여 유니버설 Windows 8.1 앱을 실행하는 특정 인증된 사용자에게 푸시 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="glenga"/>

# 인증된 사용자에게 푸시 알림 보내기

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##개요

이 항목에서는 등록된 모든 장치에서 인증된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이전의 [푸시 알림][Get started with push notifications] 자습서와 달리, 이 자습서에서는 먼저 사용자가 인증된 후에야 클라이언트가 푸시 알림을 받기 위해 알림 허브에 등록할 수 있도록 모바일 서비스를 변경합니다. 할당된 사용자 ID에 따라 태그를 추가하도록 등록도 수정됩니다. 마지막으로 서버 코드는 모든 등록 대신 인증된 사용자에게만 알림을 보내도록 업데이트됩니다.
 
이 자습서에서는 Windows 스토어 및 Windows Phone 스토어 앱이 모두 지원됩니다.

##필수 조건 

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

+ [인증 시작]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [푸시 알림 시작]<br/>알림 허브를 사용하여 푸시 알림에 맞게 TodoList 샘플 앱을 구성합니다.

두 자습서를 모두 완료한 다음에는 인증되지 않은 사용자가 모바일 서비스의 푸시 알림을 등록할 수 없도록 방지할 수 있습니다.

##<a name="register"></a>등록에 인증이 필요하도록 서비스 업데이트

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>등록 전에 로그인하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>앱 테스트

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[인증 시작]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[푸시 알림 시작]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/

 

<!---HONumber=August15_HO6-->