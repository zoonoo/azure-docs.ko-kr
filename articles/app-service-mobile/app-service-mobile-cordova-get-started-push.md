<properties
	pageTitle="Azure 모바일 앱을 사용하여 Apache Cordova 앱에 푸시 알림 추가 | Azure 앱 서비스"
	description="Azure 모바일 앱을 사용하여 Apache Cordova 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다."
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Apache Cordova 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## 개요

이 자습서에서는 푸시 알림을 [Apache Cordova 빠른 시작] 프로젝트에 추가하여 레코드가 삽입될 때마다 푸시 알림이 전송됩니다. 이 자습서는 먼저 완료해야 하는 [Apache Cordova 빠른 시작 안내서]를 기반으로 합니다. ASP.NET 백 엔드가 있고 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용]을 참조하세요.

##<a name="prerequisites"></a>필수 조건

이 자습서는 Visual Studio 2015 내에서 개발되고 Google Android 에뮬레이터에서 실행되는 Apache Cordova 응용 프로그램을 다룹니다. 또한 다른 에뮬레이터나 장치 및 다른 개발 플랫폼에서 이 자습서를 완료할 수 있습니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 검증된 메일 주소를 사용하는 [Google 계정]
* [Visual Studio Community 2015] 이상이 설치된 PC
* [Visual Studio Tools for Apache Cordova].
* [활성 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)
* 완료된 [Apache Cordova 빠른 시작] 프로젝트. 다른 자습서를 먼저 완료(예: [인증])할 수 있지만 필수는 아닙니다.
* Android 장치

푸시 알림은 Android 에뮬레이터에서 지원되지만 불안정하므로 에뮬레이터에서 푸시 알림을 테스트하지 않는 것이 좋습니다.

##<a name="create-hub"></a>알림 허브 만들기

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>Google Cloud Messaging 사용

Google Android 플랫폼을 대상으로 하므로 Google Cloud Messaging을 사용하도록 설정해야 합니다. Apple iOS 장치를 대상으로 하는 경우 APNS 지원을 사용하도록 설정합니다. 마찬가지로 Microsoft Windows 장치를 대상으로 하는 경우 WNS 또는 MPNS 지원을 사용하도록 설정합니다.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>푸시 요청을 보내도록 모바일 앱 백 엔드 구성

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>푸시 알림을 전송하도록 서버 프로젝트 업데이트

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a>USB 디버깅을 위해 Android 장치 구성

Android 장치에 응용 프로그램을 배포하려면 먼저 USB 디버깅을 사용하도록 설정해야 합니다. Android 휴대폰에서 다음 단계를 수행합니다.

1. **설정** > **전화 정보**로 이동합니다.
2. 개발자 모드가 사용하도록 설정될 때까지 **빌드 번호**를 탭합니다.
3. **설정**으로 돌아갑니다.
4. **개발자 옵션**을 선택합니다.
5. **USB 디버깅**을 켭니다.
6. USB 케이블로 Android 휴대폰을 개발 PC에 연결합니다.

이 자습서를 테스트할 때 Android 6.0(Marshmallow) 릴리스를 실행하는 Google Nexus 5X를 사용했습니다. 그러나 기술은 모든 최신 Android 릴리스에서 공통적입니다.

##<a name="add-push-to-app"></a>앱에 푸시 알림 추가

Apache Cordova 앱 프로젝트가 푸시 알림을 처리할 준비가 되었는지 확인해야 합니다.

### Apache Cordova 푸시 플러그 인 설치

Apache Cordova 응용 프로그램에서는 기본적으로 장치 또는 네트워크 기능을 처리하지 않습니다. 이러한 기능은 [npm](https://www.npmjs.com/) 또는 GitHub에 게시된 플러그 인에서 제공됩니다. `phonegap-plugin-push` 플러그 인은 네트워크 푸시 알림을 처리하는 데 사용됩니다.

이러한 방법 중 하나로 푸시 플러그 인을 설치할 수 있습니다.

**명령 프롬프트에서:**

    cordova plugin add phonegap-plugin-push

**Visual Studio 내에서:**

1.  솔루션 탐색기에서 `config.xml` 파일을 엽니다.
2.  **플러그 인** > **사용자 지정**을 클릭하고 설치 원본으로 **Git**를 선택한 다음 원본으로 `https://github.com/phonegap/phonegap-plugin-push`를 입력합니다.

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

4.  설치 원본 옆의 화살표를 클릭한 다음 **추가**를 클릭합니다.

이제 푸시 플러그 인이 설치되었습니다.

### Android Google Play Services 설치

PhoneGap 푸시 플러그 인은 푸시 알림용으로 Google Play Services를 사용합니다. 설치하려면 다음을 수행합니다.

1.  **Visual Studio**를 엽니다.
2.  **도구** > **Android** > **Android SDK Manager**를 클릭합니다.
3.  Extras 폴더에서 설치되어 있지 않은 필요한 SDK 옆의 확인란을 각각 선택합니다. 다음과 같은 패키지가 필요합니다.
    * Android 지원 라이브러리 버전 23 이상
    * Android 지원 리포지토리 버전 20 이상
    * Google Play Services 버전 27 이상
    * Google 리포지토리 버전 22 이상
4.  **패키지 설치**를 클릭합니다.
5.  설치가 완료될 때까지 기다립니다.

현재 필요한 라이브러리가 [phonegap-plugin-push 설치 설명서]에 나열되어 있습니다.

### 시작 시 푸시용 장치 등록

1. 로그인 프로세스에 대한 콜백 중에 또는 **onDeviceReady** 메서드 맨 아래에 **registerForPushNotifications** 호출을 추가합니다.


		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');

		        // Refresh the todoItems
		        refreshDisplay();

		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);

				// Added to register for push notifications.
		        registerForPushNotifications();

		    }, handleError);

	이 예제에서는 인증에 성공한 후 앱에 푸시 알림 및 인증을 모두 사용하는 경우 권장되는 **registerForPushNotifications** 호출을 보여 줍니다.

2. 다음과 같이 새 `registerForPushNotifications()` 메서드를 추가합니다.

	    // Register for Push Notifications.
		// Requires that phonegap-plugin-push be installed.
	    var pushRegistration = null;
	    function registerForPushNotifications() {
	        pushRegistration = PushNotification.init({
	            android: {
	                senderID: 'Your_Project_ID'
	            },
	            ios: {
	                alert: 'true',
	                badge: 'true',
	                sound: 'true'
	            },
	            wns: {

	            }
	        });

	        pushRegistration.on('registration', function (data) {
	            client.push.register('gcm', data.registrationId);
	        });

	        pushRegistration.on('notification', function (data, d2) {
	            alert('Push Received: ' + data.message);
	        });

	        pushRegistration.on('error', handleError);
	    }

3. 위 코드에서는 `Your_Project_ID`를 [Google 개발자 콘솔]의 앱에 대한 숫자 프로젝트 ID로 바꿉니다.

## 게시된 모바일 서비스에 대해 앱 테스트

Android 휴대폰을 USB 케이블로 직접 연결하여 앱을 테스트할 수 있습니다. **Google Android 에뮬레이터** 대신 **장치**를 선택합니다. Visual Studio에서 장치에 응용 프로그램을 다운로드하고 응용 프로그램을 실행합니다. 그런 다음 장치에서 응용 프로그램을 조작합니다.

개발 환경을 개선합니다. [Mobizen]과 같은 화면 공유 응용 프로그램은 Android 화면을 PC의 웹 브라우저에 표시하여 Android 응용 프로그램을 개발하는 데 도움을 줍니다.

또한 Android 에뮬레이터에서 Android 앱을 테스트할 수 있습니다. 먼저 에뮬레이터에 Google 계정을 추가해야 합니다.

##<a name="next-steps"></a>다음 단계

* 푸시 알림에 대한 자세한 내용은 [알림 허브]를 참조하세요.
* 아직 Apache Cordova 앱에 [인증을 추가]하지 않은 경우 추가하여 자습서를 계속합니다.

SDK 사용 방법을 알아봅니다.

* [Apache Cordova SDK]
* [ASP.NET 서버 SDK]
* [Node.js 서버 SDK]

<!-- URLs -->
[인증을 추가]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova 빠른 시작]: app-service-mobile-cordova-get-started.md
[Apache Cordova 빠른 시작 안내서]: app-service-mobile-cordova-get-started.md
[인증]: app-service-mobile-cordova-get-started-users.md
[Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google 계정]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google 개발자 콘솔]: https://console.developers.google.com/home/dashboard
[phonegap-plugin-push 설치 설명서]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/ko-KR/features/cordova-vs.aspx
[알림 허브]: ../notification-hubs/notification-hubs-overview.md
[Apache Cordova SDK]: app-service-mobile-codova-how-to-use-client-library.md
[ASP.NET 서버 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 서버 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0323_2016-->