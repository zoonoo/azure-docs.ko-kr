<properties
	pageTitle="Azure 앱 서비스를 사용하여 Xamarin.Android 앱에 푸시 알림 추가"
	description="Azure 앱 서비스와 Azure 알림 허브를 사용하여 Xamarin Android 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다."
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/18/2015" 
	ms.author="glenga"/>

# Xamarin.Android 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##개요

이 자습서에서는 푸시 알림을 [Xamarin.Android quickstart] 프로젝트에 추가하여 레코드가 삽입될 때마다 푸시 알림이 전송됩니다. 이 자습서는 먼저 완료해야 하는 [Xamarin.Android 빠른 시작] 자습서를 기반으로 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##필수 조건

이 자습서를 사용하려면 다음이 필요합니다.

+ 활성 Google 계정. [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)에서 Google 계정을 등록할 수 있습니다.
   
+ [Google Cloud Messaging 클라이언트 구성 요소](http://components.xamarin.com/view/GCMClient/). 자습서에 이 구성 요소가 추가됩니다.

+ [Xamarin.Android 빠른 시작] 자습서를 완료했습니다.


##<a name="create-hub"></a>알림 허브 만들기

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a id="register"></a>Google Cloud Messaging 사용

[AZURE.INCLUDE [GCM 사용](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##푸시 요청을 보내도록 모바일 앱 백 엔드 구성

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-server"></a>푸시 알림을 전송하도록 서버 프로젝트 업데이트

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>푸시 알림에 대한 클라이언트 프로젝트 구성

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>앱에 푸시 알림 코드 추가

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>앱에서 푸시 알림 테스트

에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다. 에뮬레이터에서 실행할 때 필요한 추가 구성 단계가 있습니다.

1. 아래와 같이 AVD(Android 가상 장치) 관리자에서 대상으로 설정된 Google API가 있는 가상 장치에 배포하거나 해당 가상 장치에서 디버그해야 합니다. 

	![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. **앱** > **설정** > **계정 추가**를 클릭하여 Android 장치에 Google 계정을 추가한 다음 표시되는 메시지에 따라 장치에 기존 Google 계정 추가를 사용하여 새로운 계정을 만듭니다.

	![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. 이전처럼 todolist 앱을 실행하고 새 todo 항목을 삽입합니다. 이때 알림 아이콘이 알림 영역에 표시됩니다. 알림 서랍을 열어서 전체 알림 텍스트를 볼 수 있습니다.

	![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quickstart]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Android 빠른 시작]: app-service-mobile-xamarin-android-get-started.md

[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!----HONumber=AcomDC_1223_2015-->