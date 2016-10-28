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
	ms.date="08/11/2016"
	ms.author="glenga"/>

# Apache Cordova 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## 개요

이 자습서에서는 푸시 알림을 [Apache Cordova 빠른 시작] 프로젝트에 추가하여 레코드가 삽입될 때마다 푸시 알림이 전송됩니다. 이 자습서는 먼저 완료해야 하는 [Apache Cordova 빠른 시작 안내서]를 기반으로 합니다. ASP.NET 백 엔드가 있고 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용]을 참조하세요.

##<a name="prerequisites"></a>필수 조건

이 자습서는 Visual Studio 2015 내에서 개발되고 Google Android 에뮬레이터, Android 장치, Windows 장치 및 iOS 장치에서 실행되는 Apache Cordova 응용 프로그램을 다룹니다.

이 자습서를 완료하려면 다음이 필요합니다.

* [Visual Studio Community 2015] 이상이 설치된 PC
* [Visual Studio Tools for Apache Cordova]
* [활성 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)
* 완료된 [Apache Cordova 빠른 시작] 프로젝트. 다른 자습서를 먼저 완료(예: [인증])할 수 있지만 필수는 아닙니다.
* (Android) 검증된 메일 주소 및 Android 장치를 사용하는 [Google 계정]
* (iOS) Apple 개발자 프로그램 멤버 자격 및 iOS 장치(iOS 시뮬레이터는 푸시를 지원하지 않음)
* (Windows) Windows 스토어 개발자 계정 및 Windows 10 장치

푸시 알림은 Android 에뮬레이터에서 지원되지만 불안정하므로 에뮬레이터에서 푸시 알림을 테스트하지 않는 것이 좋습니다.

##<a name="create-hub"></a>알림 허브 만들기

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

[유사한 단계를 보여 주는 비디오 보기](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##푸시 알림을 전송하도록 서버 프로젝트 업데이트

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>푸시 알림을 받도록 Cordova 앱 수정

Apache Cordova 앱 프로젝트가 푸시 알림을 처리할 준비가 되었는지 확인해야 합니다. Cordova 푸시 플러그 인과 플랫폼별 푸시 서비스를 설치해야 합니다.

#### 프로젝트에서 Cordova 버전을 업데이트합니다.

이전 버전을 사용하여 프로젝트를 구성하는 경우 클라이언트 프로젝트를 Cordova 6.1.1로 업데이트하는 것이 좋습니다. 프로젝트를 업데이트하려면 config.xml을 마우스 오른쪽 단추로 클릭하여 구성 디자이너를 엽니다. 플랫폼 탭을 선택하고 **Cordova CLI** 텍스트 상자에서 6.1.1을 선택합니다.

**빌드** 및 **솔루션 빌드**를 차례로 선택하여 프로젝트를 업데이트합니다.

#### 푸시 플러그 인 설치

Apache Cordova 응용 프로그램에서는 기본적으로 장치 또는 네트워크 기능을 처리하지 않습니다. 이러한 기능은 [npm](https://www.npmjs.com/) 또는 GitHub에 게시된 플러그 인에서 제공됩니다. `phonegap-plugin-push` 플러그 인은 네트워크 푸시 알림을 처리하는 데 사용됩니다.

이러한 방법 중 하나로 푸시 플러그 인을 설치할 수 있습니다.

**명령 프롬프트에서:**

다음 명령을 실행합니다.

    cordova plugin add phonegap-plugin-push

**Visual Studio 내에서:**

1.  솔루션 탐색기에서 `config.xml` 파일을 열고, **플러그 인** > **사용자 지정**을 클릭하고, 설치 원본으로 **Git**를 선택한 후 원본으로 `https://github.com/phonegap/phonegap-plugin-push`를 입력합니다.

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  설치 원본 옆의 화살표를 클릭합니다.

3. **SENDER\_ID**에서 Google 개발자 콘솔 프로젝트에 대해 숫자 프로젝트 ID가 이미 지정된 경우 여기에 추가할 수 있습니다. 그렇지 않은 경우 777777 등의 자리 표시자 값을 입력하고, Android를 대상으로 하는 경우 나중에 config.xml에서 이 값을 업데이트할 수 있습니다.

4. **추가**를 클릭합니다.

이제 푸시 플러그 인이 설치되었습니다.

####장치 플러그 인 설치

푸시 플러그 인을 설치할 때와 동일한 절차를 따르되, 코어 플러그 인 목록에서 장치 플러그 인을 찾습니다(**플러그 인** > **코어**를 클릭하여 찾음). 플랫폼 이름(`device.platform`)을 가져오려면 이 플러그 인이 필요합니다.

#### 시작 시 푸시용 장치 등록

처음에 Android에 대한 최소한의 코드를 포함합니다. 나중에 iOS 또는 Windows 10에서 실행하기 위해 약간 수정할 것입니다.

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

2. 다음과 같이 새 **registerForPushNotifications** 메서드를 추가합니다.

		// Register for Push Notifications. Requires that phonegap-plugin-push be installed.
		var pushRegistration = null;
		function registerForPushNotifications() {
		  pushRegistration = PushNotification.init({
		      android: { senderID: 'Your_Project_ID' },
		      ios: { alert: 'true', badge: 'true', sound: 'true' },
		      wns: {}
		  });

		// Handle the registration event.
		pushRegistration.on('registration', function (data) {
		  // Get the native platform of the device.
		  var platform = device.platform;
		  // Get the handle returned during registration.
		  var handle = data.registrationId;
		  // Set the device-specific message template.
		  if (platform == 'android' || platform == 'Android') {
		      // Register for GCM notifications.
		      client.push.register('gcm', handle, {
		          mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'iOS') {
		      // Register for notifications.            
		      client.push.register('apns', handle, {
		          mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'windows') {
		      // Register for WNS notifications.
		      client.push.register('wns', handle, {
		          myTemplate: {
		              body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
		              headers: { 'X-WNS-Type': 'wns/toast' } }
		      });
		  }
		});

		pushRegistration.on('notification', function (data, d2) {
		  alert('Push Received: ' + data.message);
		});

		pushRegistration.on('error', handleError);
		}

3. (Android) 위 코드에서는 `Your_Project_ID`를 [Google 개발자 콘솔]의 앱에 대한 숫자 프로젝트 ID로 바꿉니다.

## (선택 사항) Android에서 앱 구성 및 실행

이 섹션을 완료하여 Android에 대한 푸시 알림을 사용하도록 설정합니다.

####<a name="enable-gcm"></a>Google Cloud Messaging 사용

처음에는 Google Android 플랫폼을 대상으로 하므로 Google Cloud Messaging을 사용하도록 설정해야 합니다. 마찬가지로 Microsoft Windows 장치를 대상으로 하는 경우 WNS 지원을 사용하도록 설정합니다.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

[유사한 단계를 보여 주는 비디오 보기](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-4-Set-up-gcm-for-push)

####<a name="configure-backend"></a>GCM을 사용하여 푸시 요청을 보내도록 모바일 앱 백 엔드 구성

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####Android용 Cordova 앱 구성

Cordova 앱에서 config.xml을 열고 `Your_Project_ID`를 [Google 개발자 콘솔]의 앱에 대한 숫자 프로젝트 ID로 바꿉니다.

		<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
			<variable name="SENDER_ID" value="Your_Project_ID" />
		</plugin>

Index.js를 열고 숫자 프로젝트 ID를 사용하도록 코드를 업데이트합니다.

		pushRegistration = PushNotification.init({
			android: { senderID: 'Your_Project_ID' },
			ios: { alert: 'true', badge: 'true', sound: 'true' },
			wns: {}
		});

####<a name="configure-device"></a>USB 디버깅을 위해 Android 장치 구성

Android 장치에 응용 프로그램을 배포하려면 먼저 USB 디버깅을 사용하도록 설정해야 합니다. Android 휴대폰에서 다음 단계를 수행합니다.

1. **설정** > **전화 정보**로 이동한 다음 개발자 모드가 사용하도록 설정될 때까지 **빌드 번호**를 누릅니다(약 7회).

2. **설정** > **개발자 옵션**으로 돌아가서 **USB 디버깅**을 사용하도록 설정한 다음 USB 케이블을 사용하여 Android 휴대폰을 개발 PC에 연결합니다.

이 방법은 Android 6.0(Marshmallow)을 실행하는 Google Nexus 5X를 사용하여 테스트되었습니다. 그러나 기술은 모든 최신 Android 릴리스에서 공통적입니다.

#### Google Play Services 설치

푸시 플러그 인은 푸시 알림용으로 Google Play Services를 사용합니다.

1.  **Visual Studio**에서 **도구** > **Android** > **Android SDK Manager**를 클릭하고, **Extras** 폴더를 확장한 후 다음 SDK가 모두 설치되도록 확인란을 선택합니다.
    * Android 지원 리포지토리 버전 20 이상
    * Google Play Services 버전 27 이상
    * Google 리포지토리 버전 22 이상

2.  **패키지 설치**를 클릭하고 설치가 완료될 때까지 기다립니다.

현재 필요한 라이브러리가 [phonegap-plugin-push 설치 설명서]에 나와 있습니다.

#### Android 앱에서 푸시 알림 테스트

이제 앱을 실행하고 TodoItem 테이블에 항목을 삽입하여 푸시 알림을 테스트할 수 있습니다. 동일한 백 엔드를 사용하는 한, 같은 장치에서 테스트해도 되고 두 번째 장치에서 테스트해도 됩니다. 다음 방법 중 하나로 Android 플랫폼에서 Cordova 앱을 테스트합니다.

- **실제 장치에서:** USB 케이블을 사용하여 Android 장치를 개발 컴퓨터에 연결합니다. **Google Android 에뮬레이터** 대신 **장치**를 선택합니다. Visual Studio에서 장치에 응용 프로그램을 배포하고 실행할 것입니다. 이제 장치에서 응용 프로그램과 상호 작용할 수 있습니다. 개발 환경을 개선합니다. [Mobizen]과 같은 화면 공유 응용 프로그램은 Android 화면을 PC의 웹 브라우저에 표시하여 Android 응용 프로그램을 개발하는 데 도움을 줍니다.

- **Android 에뮬레이터에서:** 에뮬레이터에서 실행할 때 필요한 추가 구성 단계가 있습니다.

	아래와 같이 AVD(Android 가상 장치) 관리자에서 대상으로 설정된 Google API가 있는 가상 장치에 배포하거나 해당 가상 장치에서 디버그해야 합니다.

	![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

	더 빠른 x86 에뮬레이터를 사용하려는 경우 [HAXM 드라이버를 설치](https://taco.visualstudio.com/ko-KR/docs/run-app-apache/#HAXM)하고 해당 드라이버를 사용하도록 에뮬레이터를 구성합니다.

	**앱** > **설정** > **계정 추가**를 클릭하여 Android 장치에 Google 계정을 추가한 다음 표시되는 메시지에 따라 장치에 기존 Google 계정을 추가합니다(새 계정을 만드는 것보다 기존 계정을 사용하는 것이 좋음).

	![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

	이전처럼 todolist 앱을 실행하고 새 todo 항목을 삽입합니다. 이때 알림 아이콘이 알림 영역에 표시됩니다. 알림 서랍을 열어서 전체 알림 텍스트를 볼 수 있습니다.

	![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##(선택 사항) iOS에서 구성 및 실행

이 섹션은 iOS 장치에서 Cordova 프로젝트를 실행하기 위한 것입니다. iOS 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

####Mac 또는 클라우드 서비스에서 iOS remotebuild 에이전트를 설치하고 실행합니다.

Visual Studio를 사용하여 iOS에서 Cordova 앱을 실행하려면 먼저 [iOS 설치 가이드](http://taco.visualstudio.com/ko-KR/docs/ios-guide/)의 단계에 따라 remotebuild 에이전트를 설치하고 실행합니다.

IOS용 앱을 빌드할 수 있는지 확인합니다. 설치 가이드의 단계는 Visual Studio에서 iOS를 빌드하는 데 필요합니다. Mac이 없는 경우 MacInCloud와 같은 서비스에서 remotebuild 에이전트를 사용하여 iOS를 빌드할 수 있습니다. 자세한 내용은 [클라우드에서 iOS 앱 실행](http://taco.visualstudio.com/ko-KR/docs/build_ios_cloud/)을 참조하세요.

>[AZURE.NOTE] XCode 7 이상에서는 iOS에 대한 푸시 플러그 인을 사용해야 합니다.

####앱 ID로 사용할 ID 찾기

푸시 알림을 위해 앱을 등록하기 전에 Cordova 앱에서 config.xml을 열고, 위젯 요소에서 `id` 특성 값을 찾은 후 나중에 사용할 수 있도록 복사합니다. 다음 XML에서 ID는 `io.cordova.myapp7777777`입니다.

		<widget defaultlocale="ko-KR" id="io.cordova.myapp7777777"
  		version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
			xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

나중에 Apple 개발자 포털에서 앱 ID를 만들 때 이 식별자를 사용합니다. (개발자 포털에서 다른 앱 ID를 만들고 사용하려면 이 자습서의 뒷부분에 나오는 몇 가지 추가 단계를 수행하여 config.xml 파일에서 이 ID를 변경해야 합니다. 위젯 요소의 ID는 개발자 포털의 앱 ID와 일치해야 합니다.)

####Apple 개발자 포털의 푸시 알림에 대한 앱 등록

[AZURE.INCLUDE [알림 허브 Xamarin이 Apple 푸시 알림 사용](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

[유사한 단계를 보여 주는 비디오 보기](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####푸시 알림을 전송하도록 Azure 구성

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. **찾아보기** > **모바일 앱** > 사용 중인 모바일 앱 > **설정** > **푸시** > **Apple(APNS)** > **인증서 업로드**를 차례로 클릭합니다. 앞에서 내보낸 .p12 푸시 인증서 파일을 업로드합니다. 개발 및 테스트에 대한 개발 푸시 인증서를 만든 경우 **샌드박스**를 선택합니다. 그렇지 않은 경우 **프로덕션**을 선택합니다. 이제 iOS의 푸시 알림과 작동하도록 서비스가 구성되었습니다.

	![](./media/app-service-mobile-cordova-get-started-push/mobile-app-upload-apns-cert.png)

####앱 ID가 Cordova 앱과 일치하는지 확인

Apple 개발자 계정에서 만든 앱 ID가 config.xml의 위젯 요소 ID와 일치할 경우 이 단계를 건너뛸 수 있습니다. 그러나 ID가 일치하지 않는 경우 다음 단계를 수행합니다.

1. 프로젝트에서 플랫폼 폴더를 삭제합니다.

2. 프로젝트에서 플러그 인 폴더를 삭제합니다.

3. 프로젝트에서 node\_modules 폴더를 삭제합니다.

4. Apple 개발자 계정에서 만든 앱 ID를 사용하도록 config.xml 파일에 있는 위젯 요소의 ID 특성을 업데이트합니다.

5. 프로젝트를 다시 빌드합니다.

#####iOS 앱에서 푸시 알림 테스트

1. Visual Studio에서 **iOS**가 배포 대상으로 선택되어 있는지 확인한 후 **장치**를 선택하여 연결된 iOS 장치에서 실행되도록 합니다.

	ITunes를 사용하여 PC에 연결된 iOS 장치에서 실행할 수 있습니다. iOS 시뮬레이터는 푸시 알림을 지원하지 않습니다.

2. Visual Studio에서 **실행** 단추 또는 **F5** 키를 눌러 프로젝트를 빌드하고 iOS 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

	>[AZURE.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

3. 앱에서 작업을 입력하고 더하기(+) 아이콘을 클릭합니다.

4. 알림이 수신되는지 확인한 다음 확인을 클릭하여 알림을 해제합니다.

##(선택 사항) Windows에서 구성 및 실행

이 섹션은 Windows 10 장치에서 Apache Cordova 앱 프로젝트를 실행하기 위해 작성되었습니다(PhoneGap 푸시 플러그 인은 Windows 10에서 지원됨). Windows 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

####WNS를 사용하여 푸시 알림에 대해 Windows 앱 등록

Visual Studio에서 저장소 옵션을 사용하려면 솔루션 플랫폼 목록에서 Windows 대상(예: **Windows-x64** 또는 **Windows-x86**)을 선택합니다(푸시 알림에 대해 **Windows-AnyCPU** 선택 안 함).

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[유사한 단계를 보여 주는 비디오 보기](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####WNS에 대한 알림 허브 구성

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####Windows 푸시 알림을 지원하도록 Cordova 앱 구성

구성 디자이너를 열고(config.xml을 마우스 오른쪽 단추로 클릭하고 **뷰 디자이너** 선택) **Windows** 탭을 선택한 후 **Windows 대상 버전**에서 **Windows 10**을 선택합니다.

>[AZURE.NOTE] Cordova 5.1.1 이전의 Cordova 버전을 사용하는 경우(6.1.1 권장됨) config.xml에서 알림 가능 플래그를 true로 설정해야 합니다.

기본(디버그) 빌드에서 푸시 알림을 지원하려면 build.json 파일을 엽니다. "릴리스" 구성을 디버그 구성에 복사합니다.

		"windows": {
			"release": {
				"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
				"publisherId": "CN=yourpublisherID"
			}
		}

업데이트가 수행된 후 위의 코드는 다음과 같아야 합니다.

	"windows": {
		"release": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			},
		"debug": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			}
		}

앱을 빌드하고 오류가 없는지 확인합니다. 이제 클라이언트 앱이 모바일 앱 백 엔드에서 알림을 등록해야 합니다. 솔루션의 모든 Windows 프로젝트에 대해 이 섹션을 반복합니다.

####Windows 앱에서 푸시 알림 테스트

Visual Studio에서 Windows 플랫폼이 배포 대상(예: **Windows-x64** 또는 **Windows-x86**)으로 선택되어 있는지 확인합니다. Visual Studio를 호스트하는 Windows 10 PC에서 앱을 실행하려면 **로컬 컴퓨터**를 선택합니다.

실행 단추를 눌러 프로젝트를 빌드하고 앱을 시작합니다.

앱에서 새 todoitem에 대한 이름을 입력한 다음 더하기(+) 아이콘을 클릭하여 추가합니다.

항목이 추가될 때 알림을 받았는지 확인합니다.

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
[알림 허브]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET 서버 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 서버 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0907_2016-->