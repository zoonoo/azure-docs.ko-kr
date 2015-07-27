
<properties 
	pageTitle="푸시 알림 시작(Android JavaScript) | 모바일 개발자 센터" 
	description="Azure 모바일 서비스를 사용하여 Android JavaScript 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." 
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
	ms.date="06/01/2015" 
	ms.author="ricksal"/>


# 모바일 서비스 Android 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## 요약

이 항목에서는 GCM(Google Cloud Messaging)을 사용하는 Android 앱에 Azure 모바일 서비스를 사용하여 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서의 필수 구성 요소인 빠른 시작 프로젝트에 푸시 알림을 추가합니다. 모바일 서비스에 포함된 Azure 알림 허브를 사용하여 푸시 알림을 활성화합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

<!-- [AZURE.NOTE] If you would like to see the source code of the completed app, go <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStartedWithPush/AndroidStudio" target="_blank">here</a>.
-->


## 필수 조건

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Google Cloud Messaging 사용

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>푸시 요청을 전송하도록 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>앱에 푸시 알림 추가



다음 단계에서는 Google Play Services를 설치합니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다.

이전 장치로 테스트할 경우 이 값을 적절하게 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정](영문)을 참조하십시오.

###프로젝트에 Google Play Services 추가

[AZURE.INCLUDE [Play 서비스 추가](../../includes/mobile-services-add-Google-play-services.md)]

###코드 추가

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>관리 포털에서 등록된 삽입 스크립트 업데이트

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="test"></a>앱에서 푸시 알림 테스트

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

###테스트를 위해 Android 에뮬레이터 설정

에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

1. 도구 모음의 오른쪽 끝에서 Android Virtual Device Manager를 선택하고 장치를 선택한 다음 오른쪽에 있는 편집 아이콘을 클릭합니다.

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. 장치 설명 줄에서 **Change**를 선택하고 **Google APIs**를 선택한 후 OK를 클릭합니다.

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	AVD 대상이 Google API를 사용하도록 설정됩니다.

###테스트 실행

1. **Run** 메뉴 항목에서 **Run app**을 클릭하여 앱을 시작합니다.

2. 앱에서 _A new Mobile Services task_ 등의 의미 있는 텍스트를 입력하고 **Add** 단추를 클릭합니다.

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. 알림을 보려면 화면의 위쪽에서 아래쪽으로 살짝 밀어서 장치의 Notification Drawer를 엽니다.


이 자습서를 성공적으로 완료했습니다.

## 문제 해결

### Android SDK 버전 확인

[AZURE.INCLUDE [SDK 확인](../../includes/mobile-services-verify-android-sdk-version.md)]


## 이전 코드 버전

이 자습서의 Eclipse 버전을 참조하려면 [푸시 알림 시작(Eclipse)]으로 이동하세요.


<!--
To see a completed version of the source code in an Eclipse project, go <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/Android">here</a>.
-->


## <a name="next-steps"> </a>다음 단계

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [데이터 시작] <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법을 자세히 알아봅니다.

* [인증 시작] <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의] <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [알림 허브 응용 프로그램 디버깅](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>알림 허브 솔루션 문제를 해결하고 디버깅하기 위한 지침을 얻습니다.

* [모바일 서비스용 Android 클라이언트 라이브러리를 사용하는 방법] <br/>Android와 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

* [모바일 서비스 서버 스크립트 참조] <br/>모바일 서비스에서 비즈니스 논리를 구현하는 방법에 대해 자세히 알아봅니다.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[푸시 알림 시작(Eclipse)]: mobile-services-javascript-backend-android-get-started-push-EC.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[데이터 시작]: mobile-services-android-get-started-data.md
[인증 시작]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Google Play Services SDK 설정]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[모바일 서비스용 Android 클라이언트 라이브러리를 사용하는 방법]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[알림 허브 정의]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md
 

<!---HONumber=July15_HO3-->