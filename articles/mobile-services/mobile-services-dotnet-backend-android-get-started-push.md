<properties 
	pageTitle="푸시 시작(Android) | Microsoft Azure" 
	description="Azure 모바일 서비스를 사용하여 Android .Net 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="ricksal"/>

# 모바일 서비스 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 퀵 스타트 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기] 또는 [데이터 시작하기]를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 따라서 이 자습서를 사용하려면 Visual Studio 2013이 있어야 합니다.

>[AZURE.NOTE]이 자습서의 Eclipse 버전은 [푸시 알림 시작(Eclipse)]을 참조하세요.
 
##<a id="register"></a>Google Cloud Messaging 사용

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>푸시 요청을 전송하도록 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>푸시 알림을 전송하도록 모바일 서비스 업데이트

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a name="update-app"></a>앱에 푸시 알림 추가

###Android SDK 버전 확인

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


다음 단계에서는 Google Play Services를 설치합니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다.

이전 장치로 테스트할 경우 이 값을 적절하게 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정](영문)을 참조하세요.

###프로젝트에 Google Play Services 추가

[AZURE.INCLUDE [Play 서비스 추가](../../includes/mobile-services-add-Google-play-services.md)]

###코드 추가

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]

##<a name="test-app"></a>게시된 모바일 서비스에 대해 앱 테스트

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

###<a id="local-testing"></a> 로컬 테스트에 푸시 알림 사용

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

이 자습서를 성공적으로 완료했습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보낼 수 있도록 Android 앱을 설정하는 작업에 대한 기본 사항을 설명했습니다. 다음으로는 태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 보여 주는 다음 자습서인 [인증된 사용자에게 푸시 알림 보내기]를 완료할 수 있습니다.

+ [인증된 사용자에게 푸시 알림 보내기] <br/>태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 알아봅니다.

+ [구독자에게 브로드캐스트 알림 보내기] <br/>관심이 있는 범주에 대해 푸시 알림을 등록하고 받아보는 방법에 대해 알아봅니다.

+ [사용자에게 플랫폼 간 알림 보내기] <br/>백 엔드에 플랫폼 특정 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법을 알아봅니다.

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [앱에 인증 추가][Get started with authentication] <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의] <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [알림 허브 응용 프로그램 디버깅](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>알림 허브 솔루션 문제를 해결하고 디버깅하기 위한 지침을 얻습니다.

* [모바일 서비스용 Android 클라이언트 라이브러리를 사용하는 방법] <br/>Android와 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[푸시 알림 시작(Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-EC.md
[모바일 서비스 시작하기]: mobile-services-dotnet-backend-android-get-started.md
[데이터 시작하기]: mobile-services-dotnet-backend-android-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[모바일 서비스용 Android 클라이언트 라이브러리를 사용하는 방법]: mobile-services-android-how-to-use-client-library.md

[인증된 사용자에게 푸시 알림 보내기]: mobile-services-dotnet-backend-android-push-notifications-app-users.md

[알림 허브 정의]: ../notification-hubs-overview.md
[구독자에게 브로드캐스트 알림 보내기]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[사용자에게 플랫폼 간 알림 보내기]: ../notification-hubs-windows-store-dotnet-send-localized-breaking-news.md
[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO7-->