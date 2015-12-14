<properties
	pageTitle="Azure 모바일 앱을 사용하여 Android 앱에 푸시 알림 추가"
	description="Azure 모바일 앱을 사용하여 Android 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다."
	services="app-service\mobile"
	documentationCenter="android"
	manager="dwrede"
	editor=""
	authors="ysxu"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="yuaxu"/>

# Android 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 개요
이 자습서에서는 푸시 알림을 [Android 빠른 시작] 프로젝트에 추가하여 레코드가 삽입될 때마다 푸시 알림이 전송됩니다. 이 자습서는 먼저 완료해야 하는 [Android 빠른 시작 안내서]를 기반으로 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 검증된 메일 주소를 사용하는 [Google 계정](http://go.microsoft.com/fwlink/p/?LinkId=268302)
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* [빠른 시작 자습서](../app-service-mobile-android-get-started.md) 완료

##<a name="create-hub"></a>알림 허브 만들기

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## Google Cloud Messaging 사용

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##푸시 요청을 보내도록 모바일 앱 백 엔드 구성

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>푸시 알림을 전송하도록 서버 프로젝트 업데이트

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="publish-the-service"></a>Azure에 모바일 백 엔드 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## 앱에 푸시 알림 추가

###Android SDK 버전 확인

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

다음 단계에서는 Google Play Services를 설치합니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다.

이전 장치로 테스트할 경우 이 값을 적절하게 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정](영문)을 참조하세요.

###프로젝트에 Google Play Services 추가

[AZURE.INCLUDE [Play 서비스 추가](../../includes/app-service-mobile-add-google-play-services.md)]

###코드 추가

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## 게시된 모바일 서비스에 대해 앱 테스트

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

##<a id="more"></a>추가

* 태그를 사용하면 푸시를 사용하여 여러 조각으로 나뉜 고객을 대상으로 할 수 있습니다. [Azure 모바일 앱에 대해 .NET 백 엔드 서버 SDK로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)은 장치 설치에 태그를 추가하는 방법을 보여줍니다.

<!-- URLs -->
[Android 빠른 시작]: app-service-mobile-android-get-started.md
[Android 빠른 시작 안내서]: app-service-mobile-android-get-started.md

<!---HONumber=AcomDC_1203_2015-->