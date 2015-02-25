<properties pageTitle="Apple 푸시 알림 설정 방법" description="Azure 모바일 서비스를 사용하여 새 서비스를 만들려면 이 자습서를 따라 작업을 진행합니다." services="mobile-services, notification-hubs" documentationCenter="ios" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# Apple 푸시 알림 설정 방법

이 항목에서는 APNS(Apple Push Notification Service)를 사용하여 푸시 알림을 받도록 iOS 앱을 설정하는 방법을 보여 줍니다. 가져온 인증서는 [Azure 관리 포털][관리 포털]에서 푸시 알림을 받도록 iOS 앱을 등록하는 데 사용됩니다. 앱 업데이트 사항을 비롯한 완전한 종합 자습서는 [푸시 알림 시작]을 참조하세요. 

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

이제 이 .p12 인증서를 사용하여 앱 대신에 서비스에서 APNS에 인증하고 푸시 알림을 보낼 수 있도록 설정할 수 있습니다.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[모바일 서비스 SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[관리 포털]: https://manage.windowsazure.com/



<!--HONumber=42-->
