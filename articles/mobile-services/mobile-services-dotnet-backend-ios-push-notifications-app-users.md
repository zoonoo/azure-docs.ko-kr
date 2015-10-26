<properties
	pageTitle="인증된 사용자에게 푸시 알림 보내기(.NET 백 엔드)"
	description="특정 사용자에게 푸시 알림을 보내는 방법에 대해 알아봅니다."
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# 인증된 사용자에게 푸시 알림 보내기

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

이 항목에서는 iOS에서 인증된 사용자에게 푸시 알림을 보내는 방법에 대해 배웁니다. 이 자습서를 시작하기 전에 [인증 시작] 및 [푸시 알림 시작]을 먼저 완료합니다.

이 자습서에서는 인증된 사용자만 해당 알림을 보내기 위해 먼저 인증하고, 푸시 알림에 대한 알림 허브에 등록하고, 서버 스크립트를 업데이트하도록 사용자에게 요청합니다.

##<a name="register"></a>등록에 인증이 필요하도록 서비스 업데이트

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>등록 전에 로그인하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>앱 테스트

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[인증 시작]: mobile-services-dotnet-backend-ios-get-started-users.md
[푸시 알림 시작]: mobile-services-dotnet-backend-ios-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=Oct15_HO3-->