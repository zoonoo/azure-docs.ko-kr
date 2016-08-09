<properties
	pageTitle="Xamarin Android 앱에 푸시 알림 추가 | Microsoft Azure"
	description="Azure 모바일 서비스 및 Azure 알림 허브를 사용하여 Xamarin.Android 앱에 대해 Google Cloud Messaging으로 푸시 알림을 구성하는 방법을 알아봅니다."
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# 모바일 서비스 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 이 항목에 해당하는 모바일 앱 버전은 [Xamarin.Android 앱에 푸시 알림 추가](../app-service-mobile/app-service-mobile-xamarin-android-get-started-push.md)를 참조하세요.

##개요
이 항목에서는 Azure 모바일 서비스를 사용하여 Xamarin.Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 [모바일 서비스 시작] 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

이 자습서를 사용하려면 다음이 필요합니다.

+ 활성 Google 계정.
+ [Google Cloud Messaging 클라이언트 구성 요소]. 자습서에 이 구성 요소가 추가됩니다.

[모바일 서비스 시작]을 완료했다면 프로젝트에 Xamarin 및 [Azure 모바일 서비스 구성 요소]가 설치되어 있을 것입니다.

##<a id="register"></a>Google Cloud Messaging 사용

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>푸시 요청을 전송하도록 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>알림을 보내도록 등록된 삽입 스크립트 업데이트

>[AZURE.TIP] 다음 단계에서는 Azure 클래식 포털의 TodoItem 테이블에서 삽입 작업에 등록된 스크립트를 업데이트하는 방법을 보여 줍니다. 또한 서버 탐색기의 Azure 노드의 Visual Studio에서 이 모바일 서비스 스크립트에 직접 액세스하여 편집할 수도 있습니다.

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="configure-app"></a>푸시 알림에 대한 기존 프로젝트 구성

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>앱에 푸시 알림 코드 추가

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a id="test"></a>앱에서 푸시 알림 테스트

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

이 자습서를 성공적으로 완료했습니다.

## <a name="next-steps"></a>다음 단계

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [인증 시작](mobile-services-android-get-started-users.md) <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의](../notification-hubs/notification-hubs-push-notification-overview.md) <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [알림 허브 응용 프로그램 디버깅](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>알림 허브 솔루션 문제를 해결하고 디버깅하기 위한 지침을 얻습니다.

* [모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법](mobile-services-dotnet-how-to-use-client-library.md) <br/>.NET과 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

* [모바일 서비스 서버 스크립트 참조](mobile-services-how-to-use-server-scripts.md) <br/>모바일 서비스에서 비즈니스 논리를 구현하는 방법에 대해 자세히 알아봅니다.

<!-- URLs. -->
[모바일 서비스 시작]: mobile-services-ios-get-started.md

[Google Cloud Messaging 클라이언트 구성 요소]: http://components.xamarin.com/view/GCMClient/
[Azure 모바일 서비스 구성 요소]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0727_2016-->