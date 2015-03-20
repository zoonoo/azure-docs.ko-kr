<properties 
	pageTitle="iOS용 Azure Mobile Engagement 시작" 
	description="iOS에서 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다." 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# Mobile Engagement 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows 스토어 </a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone </a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS </a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

이 항목에서는 Azure Mobile Engagement를 사용하여 앱 사용을 이해하고 iOS 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 
이 자습서에서는 기본 데이터를 수집하고 APNS(Apple 푸시 알림 시스템)를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다. 이 시나리오를 완료하면 모든 장치 또는 해당 장치 속성에 따라 특정 대상 사용자에게 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. Mobile Engagement를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다. 

이 자습서의 작업을 수행하려면 다음이 필요합니다.

+ MAC 앱 스토어에서 설치할 수 있는 Xcode
+ [Mobile Engagement iOS SDK]
+ Apple 개발자 센터에서 가져올 수 있는 푸시 알림 인증서(.p12)

이 자습서를 완료해야 다른 모든 iOS 앱용 Mobile Engagement 자습서를 진행할 수 있습니다. 

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>앱에 대해 Mobile Engagement 설정

1. [Azure 관리 포털]에 로그온하여 화면 하단의 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **Mobile Engagement**, **만들기**를 차례로 클릭합니다.

   	![][7]

3. 표시되는 팝업의 몇 가지 필드를 입력합니다.
 
   	![][8]

	1. *응용 프로그램 이름*: 응용 프로그램의 이름을 입력할 수 있습니다. 어떤 문자라도 사용할 수 있습니다.
	2. *플랫폼*: 해당 앱의 대상 플랫폼을 선택합니다. 앱이 여러 플랫폼을 대상으로 하는 경우 각 플랫폼에 대해 이 자습서를 반복합니다.
	3. *응용 프로그램 리소스 이름*: API 및 URL을 통해 이 응용 프로그램에 액세스하는 데 사용되는 이름입니다. 기준 URL 문자를 사용하는 것이 좋으며, 자동 생성된 이름을 기본으로 사용하는 것이 좋습니다. 또한 이 이름은 고유해야 하므로 이름 충돌을 방지하기 위해 플랫폼 이름을 추가하는 것도 좋습니다.
	4. *위치*: 이 앱(및 해당 컬렉션 - 아래 참조)을 호스트할 데이터 센터를 선택합니다.
	5. *컬렉션*: 응용 프로그램을 이미 만든 경우 이전에 만든 컬렉션을 선택하고, 그렇지 않은 경우 새 컬렉션을 선택합니다.


	완료하면 확인 단추를 클릭하여 앱 생성을 완료합니다.

4. 이제 응용 프로그램 탭에서 다음과 같이 방금 만든 앱을 클릭/선택합니다.
 
   	![][9]

5. 그런 다음 연결 설정을 표시하여 SDK 통합에 넣으려면 "연결 정보"를 클릭합니다.
 
   	![][10]

6. 마지막으로 "연결 문자열"을 기록해 둡니다. 이 문자열은 응용 프로그램 코드에서 이 앱을 식별하는 데 필요합니다.

   	![][11]

	>[AZURE.TIP] 연결 문자열의 오른쪽에 있는 "복사" 아이콘을 사용하면 편리하게 클립보드에 복사할 수 있습니다.

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 전송하는 데 필요한 최소 설정인 "기본 통합"을 보여 줍니다. 완벽한 통합 설명서는 [모바일 고객 관리 iOS SDK 설명서]에서 찾아볼 수 있습니다.

이제 Android Studio로 기본 앱을 만들어 통합을 보여 주겠습니다.

###새 iOS 프로젝트 만들기

이미 앱이 있으며 iOS 개발에 익숙한 경우 이 단계를 건너뛸 수 있습니다.

1. Xcode를 시작하고 팝업에서 "Create a new Xcode project"를 선택합니다.

   	![][12]

2. 앱 이름, 회사 이름 및 ID를 입력합니다. 나중에 필요하므로 이 정보를 기록해둔 후 Next를 클릭합니다.

   	![][13]

3. 이제 Single View Application을 선택하고 Next를 클릭합니다.

   	![][14]


그러면 Xcode가 모바일 고객 관리를 통합할 데모 앱을 만듭니다.

###프로젝트에 SDK 라이브러리 포함

SDK 라이브러리 다운로드 및 통합

1. [Mobile Engagement iOS SDK]를 다운로드합니다.
2. .tar.gz 파일을 컴퓨터의 폴더로 추출합니다.
3. 프로젝트를 마우스 오른쪽 단추로 클릭하고 "파일을 추가할 위치..."를 선택합니다.

	![][17]

4. SDK를 추출한 폴더로 이동하고 `EngagementSDK` 폴더를 선택한 후 확인을 누릅니다.

	![][18]

5. 그런 다음 `Build Phases(빌드 단계)` 탭을 열고 `Link Binary With Libraries(이진과 라이브러리 연결)` 메뉴에서 아래와 같이 프레임워크를 추가합니다.

	![][19]


###연결 문자열로 Mobile Engagement 백 엔드에 앱 연결

1. 응용 프로그램 대리자 구현 파일의 위쪽에서 다음 코드 줄을 복사합니다.

		#import "EngagementAgent.h"

2. Azure 포털로 이동하여 앱의 *연결 정보* 페이지로 돌아가 연결 문자열을 복사합니다.

	![][11]

3. 복사한 문자열을 `didFinishLaunchingWithOptions` 대리자에 붙여넣습니다.		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##Mobile Engagement에 화면 보내기

데이터를 보내고 사용자가 활성인지 확인하려면 Mobile Engagement 백 엔드에 하나 이상의 화면(작업)을 보내야 합니다.  `UIViewController` 클래스를 SDK에서 제공하는  `EngagementViewController`와 함께 오버로드하여 이 작업을 수행할 수 있습니다.
이렇게 하려면 아래와 같이 `ViewController.h` 파일을 열어  `EngagementViewController.h`를 가져오고  `ViewController` 인터페이스의 상위 클래스를  `EngagementViewController`로 바꿉니다.

![][22]

##<a id="monitor"></a>앱이 실시간 모니터링과 연결되어 있는지 확인하는 방법

이 섹션에서는 Mobile Engagement의 실시간 모니터링 기능을 사용하여 앱이 Mobile Engagement 백 엔드에 연결되어 있는지 확인하는 방법을 보여 줍니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 이 프로젝트에 사용하고 있는 앱으로 이동한 후 하단에 있는 "Engage" 단추를 클릭합니다.

	![][26]

2. 그러면 앱에 대한 Mobile Engagement 포털의 설정 페이지가 표시됩니다. 여기에서 다음과 같이 "모니터" 탭을 클릭합니다.

	![][30]

3. 모니터는 앱을 시작할 장치를 실시간으로 표시할 준비가 되어 있습니다.

	![][31]

4. Xcode로 돌아가 시뮬레이터 또는 연결된 장치에서 앱을 시작합니다.

5. 앱이 작동하는 경우 이제 모니터에서 한 세션을 볼 수 있습니다! 

**축하합니다.** 이 자습서의 첫 번째 단계를 완료하였습니다. 앱을 Mobile Engagement 백 엔드에 연결하고 이미 데이터를 보냈습니다.

6. 시뮬레이터의 홈 단추를 클릭하면 위와 같이 모니터의 세션 수가 0으로 다시 돌아갑니다.

	![][33]



##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement를 통해 캠페인의 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 도달할 수 있습니다. 이 모듈을 Mobile Engagement 포털에서 도달률이라고 합니다.
다음 섹션에서는 알림 또는 메시지를 수신하도록 앱을 설정합니다.

### 프로젝트에 도달률 라이브러리 추가

1. 프로젝트를 마우스 오른쪽 단추로 클릭합니다.
2. `파일을 추가할 위치...`를 선택합니다.
3. SDK를 추출한 폴더로 이동합니다.
4.  `EngagementReach` 폴더를 선택합니다.
5. 추가를 클릭합니다.

### 응용 프로그램 대리자 수정

1. 구현 파일의 맨 위에서 참여 도달률 모듈을 가져옵니다.

		#import "AEReachModule.h"
	
2. 다음과 같이 `application:didFinishLaunchingWithOptions` 내부에서 도달률 모듈을 생성하여 기존 참여 초기화 줄에 전달합니다.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}	

###앱이 APNS 푸시 알림을 받을 수 있도록 설정

1. 다음 줄을 `application:didFinishLaunchingWithOptions` 메서드에 추가합니다.

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. 다음과 같이 `application:didRegisterForRemoteNotificationsWithDeviceToken` 메서드를 추가합니다.
 
		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 		   [[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. 다음과 같이 `didReceiveRemoteNotification` 메서드를 추가합니다.
4. 
		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###Mobile Engagement에 푸시 인증서에 대한 액세스 권한 부여

사용자를 대신하여 Mobile Engagement에서 푸시 알림을 보내도록 허용하려면 Mobile Engagement에 인증서에 대한 액세스 권한을 부여해야 합니다. 인증서를 구성하고 Mobile Engagement 포털에 입력하여 이를 수행합니다. Apple 설명서에서 설명한 대로 .p12 인증서를 가져와야 합니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 이 프로젝트에 사용하고 있는 앱으로 이동한 후 하단에 있는 "Engage" 단추를 클릭합니다.

	![][26]

2. 그러면 Engagement 포털의 설정 페이지가 표시됩니다. 여기에서 다음과 같이 "네이티브 푸시" 섹션을 클릭하여 p12 인증서를 업로드합니다.

	![][27]

3. 다음과 같이 p12를 선택하여 업로드하고 암호를 입력합니다.

	![][28]

4. 그런 다음 프로비저닝 프로필을 추가하고 대상 장치의 앱을 빌드합니다.

모든 설정을 완료하였으므로 이제 이 기본 통합을 제대로 수행했는지 확인합니다.

##<a id="send"></a>앱에 알림을 보내는 방법

이제 앱에 푸시를 보낼 간단한 푸시 알림 캠페인을 만듭니다.

1. Mobile Engagement 포털에서 도달률 탭으로 이동합니다.

2. "새 알림"을 클릭하여 푸시 캠페인을 만듭니다.
	
	![][35]

3. 다음과 같이 캠페인의 첫 번째 필드를 설정합니다.

	![][36]

	1. 원하는 이름으로 캠페인의 이름을 지정합니다.
	2. 배달 시간을 "Out of app only(앱 외부에서만)"으로 선택합니다. 이는 일부 텍스트를 제공하는 간단한 Apple 푸시 알림 유형입니다.
	3. 알림 텍스트에서 먼저 푸시의 첫 줄에 표시될 제목을 입력합니다.
	4. 그런 다음 두 번째 줄이 될 메시지를 입력합니다.

4. 아래로 스크롤하여 콘텐츠 섹션에서 "Notification only(알림만)"를 선택합니다.

	![][37]

5. 가능한 대부분의 기본 캠페인을 설정했습니다. 이제 다시 아래로 스크롤하여 캠페인을 만들어 저장합니다!
![][38]

6. 마지막 단계로 캠페인을 활성화합니다.
![][39]

7. 장치에 푸시 알림이 표시됩니다!

<!-- URLs. -->
[모바일 고객 관리 iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK 설명서]: http://go.microsoft.com/?linkid=9874682
[Azure 관리 포털]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
