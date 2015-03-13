<properties 
	pageTitle="Google Cloud Messaging 설정 방법" 
	description="Azure 모바일 서비스를 사용하여 새 서비스를 만들려면 이 자습서를 따라 작업을 진행합니다." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Google Cloud Messaging 설정 방법

이 항목에서는 GCM(Google Cloud Messaging)을 사용해서 푸시 알림을 받도록 Android 앱을 설정하는 방법을 보여 줍니다. 가져온 API 키는 [Azure 관리 포털][관리 포털]에서 푸시 알림을 받도록 Android 앱을 등록하는 데 사용됩니다. 앱 업데이트 사항을 비롯한 완전한 종합 자습서는 [푸시 알림 시작]을 참조하세요. 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

이제 이 API 키 값을 사용하여 앱 대신에 서비스에서 GCM에 인증하고 푸시 알림을 보내도록 설정할 수 있습니다.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[모바일 서비스 SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[관리 포털]: https://manage.windowsazure.com/
[.NET 백 엔드 버전]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started


<!--HONumber=42-->
