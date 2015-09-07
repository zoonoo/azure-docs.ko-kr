<properties
	pageTitle="Objective C에서 iOS용 Azure Mobile Engagement 시작"
	description="iOS 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

# Objective C에서 iOS 앱용 Azure Mobile Engagement 시작

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

이 항목에서는 Azure Mobile Engagement를 사용하여 앱 사용을 이해하고 iOS 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 기본 데이터를 수집하고 APNS(Apple 푸시 알림 시스템)를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다. 이 자습서를 완료하면 장치 속성에 따라 특정 사용자를 대상으로 지정하거나 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. Mobile Engagement를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다.

이 자습서를 사용하려면 다음이 필요합니다.

+ MAC 앱 스토어에서 설치할 수 있는 XCode
+ [Mobile Engagement iOS SDK]
+ Apple 개발자 센터에서 가져올 수 있는 푸시 알림 인증서(.p12)

이 자습서를 완료해야 다른 모든 iOS 앱용 Mobile Engagement 자습서를 진행할 수 있습니다.

> [AZURE.IMPORTANT]iOS 앱용 다른 모든 Mobile Engagement 자습서를 사용하기 전에 이 자습서를 완료해야 하며, 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-KR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>앱용 Mobile Engagement 설정

1. Azure 포털에 로그인한 다음 화면 아래쪽에서 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **Mobile Engagement** 및 **만들기**를 차례로 클릭합니다.

	![][7]

3. 표시되는 팝업에서 다음 정보를 입력합니다.

   ![][8]

   - **응용 프로그램 이름**: 응용 프로그램의 이름을 입력합니다. 원하는 모든 문자를 사용할 수 있습니다.
   - **플랫폼**: 앱의 대상 플랫폼(**iOS**)을 선택합니다. 앱의 대상 플랫폼이 여러 개이면 각 플랫폼에 대해 이 자습서의 작업을 반복합니다.
   - **응용 프로그램 리소스 이름**: API 및 URL을 통해 이 응용 프로그램에 액세스하는 데 사용되는 이름입니다. 기본 URL 문자만 사용해야 합니다. 자동으로 생성되는 이름을 기준으로 사용할 수 있습니다. 또한 이 이름은 고유해야 하므로 이름 충돌을 방지하기 위해 플랫폼 이름을 추가해야 합니다.
   - **위치**: 이 앱과 해당 컬렉션을 호스트할 데이터 센터를 선택합니다.
   - **컬렉션**: 응용 프로그램을 이미 만든 경우 이전에 만든 **컬렉션**을 선택하고 그러지 않은 경우 **새 컬렉션**을 선택합니다.
   - **컬렉션 이름**: 응용 프로그램 그룹을 나타냅니다. 또한 그룹을 사용하는 경우에는 모든 앱이 그룹에 포함되므로 메트릭의 집계 계산이 가능합니다. 해당하는 경우 여기에 회사 이름이나 부서를 사용해야 합니다.

4. **응용 프로그램** 탭에서 방금 만든 앱을 선택합니다.

5. **연결 정보**를 클릭하여 모바일 앱의 SDK 통합에 적용할 연결 설정을 표시합니다.

	![][10]

6. **연결 문자열** 복사 - 응용 프로그램 코드에서 이 앱을 식별하고 Phone 앱에서 Mobile Engagement와 연결하는 데 필요합니다.

	![][11]

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 완벽한 통합 설명서는 Mobile Engagement iOS SDK 설명서에서 찾아볼 수 있습니다.

여기서는 통합을 시연하기 위해 XCode를 사용하여 기본적인 앱을 만듭니다.

###새 iOS 프로젝트 만들기

이미 앱이 있으며 iOS 개발에 익숙한 경우 이 단계를 건너뛸 수 있습니다.

1. Xcode를 시작하고 팝업에서 **Create a new Xcode project**를 선택합니다.

	![][12]

2. **Single View Application**을 선택하고 **Next**를 클릭합니다.

	![][14]

3. **Product Name**, **Organization Name** 및 **Organization Identifier**를 입력합니다. **언어** 상자에서 **Objective-C**를 선택했는지 확인합니다.

	![][13]

그러면 Xcode가 Mobile Engagement를 통합할 데모 앱을 만듭니다.

###Mobile Engagement 백 엔드에 앱 연결

1. [Mobile Engagement iOS SDK]를 다운로드합니다.
2. 컴퓨터의 폴더에 .tar.gz 파일의 압축을 풉니다.
3. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **파일을 추가할 위치**를 선택합니다.

	![][17]

4. SDK를 추출한 폴더로 이동하고 `EngagementSDK` 폴더를 선택한 후 **확인**을 누릅니다.

	![][18]

5. **Build Phases(빌드 단계)** 탭을 열고 **Link Binary With Libraries(이진과 라이브러리 연결)** 메뉴에서 아래와 같이 프레임워크를 추가합니다.

	![][19]

6. Azure 포털의 앱 **연결 정보** 페이지로 돌아가서 연결 문자열을 복사합니다.

	![][11]

7. 응용 프로그램 대리자 구현 파일을 열고 다음 코드 줄을 추가합니다.

		#import "EngagementAgent.h"

8. 이제 연결 문자열을 `didFinishLaunchingWithOptions` 대리자에 붙여넣습니다.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##<a id="monitor"></a>실시간 모니터링 사용

데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다.

- **ViewController.h** 파일을 열고 **EngagementViewController.h**를 가져온 후 아래와 같이 **ViewController** 인터페이스의 슈퍼클래스를 **EngagementViewController**로 바꿉니다.

![][22]

###앱이 실시간 모니터링 기능과 연결되어 있는지 확인

이 섹션에서는 Mobile Engagement의 실시간 모니터링 기능을 사용하여 앱이 Mobile Engagement 백 엔드에 연결되어 있는지 확인하는 방법을 보여 줍니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 현재 이 프로젝트에 사용하고 있는 앱에 있는지 확인한 다음 아래쪽에서 **연결** 단추를 클릭합니다.

	![][26]

2. 그러면 앱에 대한 Mobile Engagement 포털의 **설정** 페이지가 표시됩니다. 여기에서 **모니터** 탭을 클릭합니다.

	![][30]

3. 모니터는 앱을 시작할 장치를 실시간으로 표시할 준비가 되어 있습니다.

	![][31]

4. Xcode로 돌아가 시뮬레이터 또는 연결된 장치에서 앱을 시작합니다.

5. 앱이 정상적으로 작동하면 모니터에 세션 하나가 표시됩니다.

**축하합니다.** 이 자습서의 첫 번째 단계를 완료했습니다. 앱을 Mobile Engagement 백 엔드에 연결했으며 이미 데이터를 보내고 있습니다.

6. 시뮬레이터의 **홈** 단추를 클릭하면 위와 같이 모니터의 세션 수가 0으로 다시 돌아갑니다.

	![][33]

##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자 및 도달률과 상호 작용할 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다. 다음 섹션에서는 이러한 알림과 메시지를 받도록 앱을 설정합니다.

### 앱이 자동 푸시 알림을 받을 수 있도록 설정

[AZURE.INCLUDE [mobile-engagement-ios-자동-푸시](../../includes/mobile-engagement-ios-silent-push.md)]


### 프로젝트에 도달률 라이브러리 추가

1. 프로젝트를 마우스 오른쪽 단추로 클릭합니다.
2. **파일을 추가할 위치**를 선택합니다.
3. SDK를 추출한 폴더로 이동합니다.
4. `EngagementReach` 폴더를 선택합니다.
5. **추가**를 클릭합니다.

### 응용 프로그램 대리자 수정

1. 구현 파일 맨 위에서 Engagement 도달률 모듈을 가져옵니다.

		#import "AEReachModule.h"

2. 다음과 같이 **application:didFinishLaunchingWithOptions** 메서드 내부에서 도달률 모듈을 생성하여 기존 Engagement 초기화 줄에 전달합니다.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###앱이 APNS 푸시 알림을 받을 수 있도록 설정

1. **application:didFinishLaunchingWithOptions** 메서드에 다음 줄을 추가합니다.

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. 다음과 같이 **application:didRegisterForRemoteNotificationsWithDeviceToken** 메서드를 추가합니다.

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. 다음과 같이 **didReceiveRemoteNotification:fetchCompletionHandler** 메서드를 추가합니다.

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

###Mobile Engagement에 푸시 인증서에 대한 액세스 권한 부여

사용자를 대신하여 Mobile Engagement에서 푸시 알림을 보내도록 허용하려면 Mobile Engagement에 인증서에 대한 액세스 권한을 부여해야 합니다. 인증서를 구성하고 Mobile Engagement 포털에 입력하여 이를 수행합니다. Apple 설명서에서 설명한 대로 .p12 인증서를 가져와야 합니다.

1. Mobile Engagement 포털로 이동합니다. 이 프로젝트용으로 사용 중인 앱이 열려 있는지 확인하고 맨 아래에서 **연결** 단추를 클릭합니다.

	![][26]

2. 그러면 Engagement 포털의 **설정** 페이지가 표시됩니다. 여기에서 **네이티브 푸시** 섹션을 클릭하여 p12 인증서를 업로드합니다.

	![][27]

3. p12를 선택하여 업로드하고 암호를 입력합니다.

	![][28]

4. 그런 다음 프로비전 프로필을 추가하고 대상 장치의 앱을 빌드합니다.

모든 설정을 완료했습니다. 이제 이 기본 통합을 제대로 수행했는지 확인합니다.

##<a id="send"></a>앱에 알림 보내기

이제 앱에 푸시를 보낼 간단한 푸시 알림 캠페인을 만듭니다.

1. Mobile Engagement 포털에서 **도달률** 탭으로 이동합니다.

2. **새 공지**를 클릭하여 푸시 알림 캠페인을 만듭니다.

	![][35]

3. 캠페인의 첫 번째 필드를 설정합니다.

	![][36]

	- 	캠페인 이름을 원하는 이름으로 지정합니다.
	- 	**배달 시간**을 **Out of app only(앱 외부에서만)**으로 선택합니다. 이는 일부 텍스트를 제공하는 간단한 Apple 푸시 알림 유형입니다.
	- 	알림 텍스트에서 먼저 푸시의 첫 줄에 표시될 제목을 입력합니다.
	- 	그런 다음 두 번째 줄이 될 메시지를 입력합니다.


4. 아래로 스크롤하여 **콘텐츠** 섹션에서 "알림만"을 선택합니다.

	![][37]

5. 가능한 가장 기본적인 캠페인 설정을 완료했습니다. 이제 다시 아래로 스크롤하고 캠페인을 만들어 저장합니다. ![][38]

6. 마지막 단계: 캠페인을 활성화합니다. ![][39]

7. 장치에 푸시 알림이 표시됩니다!

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->