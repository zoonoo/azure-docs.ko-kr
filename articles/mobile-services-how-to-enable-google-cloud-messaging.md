<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Google Cloud Messaging 설정 방법

이 항목에서는 GCM(Google Cloud Messaging)을 사용해서 푸시 알림을 받도록 Android 앱을 설정하는 방법을 보여 줍니다. 가져온 API 키는 [Azure 관리 포털][Azure 관리 포털]에서 푸시 알림을 받도록 Android 앱을 등록하는 데 사용됩니다. 앱 업데이트 사항을 비롯한 완전한 종합 자습서는 [푸시 알림 시작][푸시 알림 시작]을 참조하세요.

[WACOM.INCLUDE [GCM 사용][GCM 사용]]

이제 이 API 키 값을 사용하여 앱 대신에 서비스에서 GCM에 인증하고 푸시 알림을 보내도록 설정할 수 있습니다.



  [Azure 관리 포털]: https://manage.windowsazure.com/
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [GCM 사용]: ../includes/mobile-services-enable-Google-cloud-messaging.md
