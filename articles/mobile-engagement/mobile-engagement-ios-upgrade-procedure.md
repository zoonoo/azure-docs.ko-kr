<properties
	pageTitle="Azure Mobile Engagement iOS SDK 업그레이드 절차"
	description="Azure Mobile Engagement용 iOS SDK의 최신 업데이트 및 절차"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr" />

#업그레이드 절차

이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

SDK의 각 새 버전에 대해 먼저 EngagementSDK 및 EngagementReach 폴더를 대체해야 합니다. 즉, xcode에서 이 폴더를 제거한 후에 다시 가져와야 합니다.

##2\.0.0에서 3.0.0으로
iOS 4.X에 대한 지원을 삭제했습니다. 이 버전부터 응용 프로그램의 배포 대상은 iOS 6 이상이어야 합니다.

응용 프로그램에서 도달률을 사용하는 경우 원격 알림을 받기 위해 `remote-notification` 값을 Info.plist 파일의 `UIBackgroundModes` 배열에 추가해야 합니다.

메서드 `application:didReceiveRemoteNotification:`은 응용 프로그램 대리자의 `application:didReceiveRemoteNotification:fetchCompletionHandler:`로 대체해야 합니다.

"AEPushDelegate.h"는 더 이상 사용되지 않는 인터페이스이므로 모든 참조를 제거해야 합니다. 여기에는 응용 프로그램 대리자에서 `[[EngagementAgent shared] setPushDelegate:self]` 및 대리자 메서드를 제거하는 일도 포함됩니다.

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##1\.16.0에서 2.0.0으로 마이그레이션
아래에서는 SDK 통합을 Capptain SAS 제공 Capptain 서비스에서 Azure Mobile Engagement 구동 앱으로 마이그레이션하는 방법을 설명합니다. 이전 버전에서 마이그레이션하는 경우 Capptain 웹 사이트를 참조하여 먼저 1.16으로 마이그레이션한 후 다음 절차를 적용하세요.

>[Azure.IMPORTANT]Capptain과 Mobile Engagement는 같은 서비스가 아니며, 아래에서 제공하는 절차에서는 클라이언트 앱을 마이그레이션하는 방법만 중점적으로 설명합니다. 앱에서 SDK를 마이그레이션해도 데이터가 Capptain 서버에서 Mobile Engagement 서버로 마이그레이션되지는 않습니다.

### 에이전트

메서드 `registerApp:`이(가) 새 메서드인 `init:`(으)로 바뀌었습니다. 그에 따라 응용 프로그램 대리자를 업데이트하고 다음과 같이 연결 문자열을 사용해야 합니다.

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

SmartAd 추적이 SDK에서 제거되었으므로 `AETrackModule` 클래스의 모든 인스턴스를 제거해야 합니다.

### 클래스 이름 변경

브랜드 변경의 일환으로 몇 가지 클래스/파일 이름을 변경해야 합니다.

"CP" 접두사가 붙은 모든 클래스의 이름이 "AE"로 바뀌었습니다.

예제:

-   `CPModule.h`의 이름이 `AEModule.h`(으)로 바뀌었습니다.

"Capptain" 접두사가 붙은 모든 클래스의 이름이 "Engagement"로 바뀌었습니다.

예제:

-   클래스 `CapptainAgent`의 이름은 `EngagementAgent`(으)로 바뀌었습니다.
-   클래스 `CapptainTableViewController`의 이름은 `EngagementTableViewController`(으)로 바뀌었습니다.
-   클래스 `CapptainUtils`의 이름은 `EngagementUtils`(으)로 바뀌었습니다.
-   클래스 `CapptainViewController`의 이름은 `EngagementViewController`(으)로 바뀌었습니다.

<!---HONumber=Oct15_HO3-->