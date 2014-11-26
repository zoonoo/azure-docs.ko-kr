<properties pageTitle="How to enable Apple push notifications" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to Apple push notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Apple 푸시 알림 설정 방법

이 항목에서는 APNS(Apple Push Notification Service)를 사용하여 푸시 알림을 받도록 iOS 앱을 설정하는 방법을 보여 줍니다. 가져온 인증서는 [Azure 관리 포털][Azure 관리 포털]에서 푸시 알림을 받도록 iOS 앱을 등록하는 데 사용됩니다. 앱 업데이트 사항을 비롯한 완전한 종합 자습서는 [푸시 알림 시작][푸시 알림 시작]을 참조하세요.

[WACOM.INCLUDE [Apple 푸시 알림 설정](../includes/enable-apple-push-notifications.md)]

이제 이 .p12 인증서를 사용하여 앱 대신에 서비스에서 APNS에 인증하고 푸시 알림을 보낼 수 있도록 설정할 수 있습니다.



  [Azure 관리 포털]: https://manage.windowsazure.com/
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
