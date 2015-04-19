<properties 
	pageTitle="Azure Mobile Engagement iOS SDK 개요" 
	description="Azure Mobile Engagement용 iOS SDK의 최신 업데이트 및 절차"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#2.0.0

이 문서에서는 iOS 앱에 Azure Mobile Engagement를 통합하는 방법에 대한 모든 세부 사항을 확인할 수 있습니다. 먼저 통합을 연습해 보려면 [15분 자습서]의 단계를 진행하세요(mobile-engagement-ios-get-started.md).

[SDK 콘텐츠를 보려면 클릭하세요.](mobile-engagement-ios-sdk-content.md)

##통합 절차
1. 시작: [iOS 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-ios-integrate-engagement.md)

2. 알림: [iOS 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-ios-integrate-engagement-reach.md)

3. 태그 계획 구현: [iOS 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-ios-use-engagement-api.md)


##릴리스 정보

###2.0.0(2015/02/17)

-   Azure Mobile Engagement의 최초 릴리스입니다.
-   appId/sdkKey 구성이 연결 문자열 구성으로 바뀌었습니다.
-   임의의 XMPP 엔터티에서 임의의 XMPP 메시지를 보내고 받기 위한 API가 제거되었습니다.
-   장치 간에 메시지를 보내고 받기 위한 API가 제거되었습니다.
-   보안이 개선되었습니다.
-   SmartAd 추적 기능이 제거되었습니다.

이전 버전에 대한 내용은 [전체 릴리스 정보]를 참조하세요.(mobile-engagement-ios-release-notes.md)

##업그레이드 절차

이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 관련 정보는 전체 [업그레이드 절차]를 참조하세요(mobile-engagement-ios-upgrade-procedure.md).

SDK의 각 새 버전에 대해 먼저 EngagementSDK 및 EngagementReach 폴더를 대체해야 합니다. 즉, xcode에서 이 폴더를 제거한 후에 다시 가져와야 합니다.

###1.16.0에서 2.0.0으로 마이그레이션
아래에서는 SDK 통합을 Capptain SAS 제공 Capptain 서비스에서 Azure Mobile Engagement 구동 앱으로 마이그레이션하는 방법을 설명합니다. 

>[Azure.IMPORTANT] Capptain과 Mobile Engagement는 같은 서비스가 아니며, 아래에서 제공하는 절차에서는 클라이언트 앱을 마이그레이션하는 방법만 중점적으로 설명합니다. 앱에서 SDK를 마이그레이션해도 데이터가 Capptain 서버에서 Mobile Engagement 서버로 마이그레이션되지는 않습니다.

이전 버전에서 마이그레이션하는 경우에는 Capptain 웹 사이트를 참조하여 1.16으로 먼저 마이그레이션한 후에 다음 절차를 따르세요.

#### 에이전트

`registerApp:` 메서드가 새 메서드인 'init'로 바뀌었습니다. 그에 따라 응용 프로그램 대리자를 업데이트하고 다음과 같이 연결 문자열을 사용해야 합니다.

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

SmartAd 추적이 SDK에서 제거되었으므로 모든 `AETrackModule` 클래스 인스턴스를 제거해야 합니다.

#### 클래스 이름 변경

브랜드 변경의 일환으로 몇 가지 클래스/파일 이름을 변경해야 합니다.

"CP" 접두사가 붙은 모든 클래스의 이름이 "AE"로 바뀌었습니다.

예제:

-   `CPModule.h`의 이름이 `AEModule.h`로 바뀌었습니다.

"Capptain" 접두사가 붙은 모든 클래스의 이름이 "Engagement"로 바뀌었습니다.

예제:

-   클래스 `CapptainAgent`의 이름은 `EngagementAgent`로 바뀌었습니다.
-   클래스 `CapptainTableViewController`의 이름은 `EngagementTableViewController`로 바뀌었습니다.
-   클래스 `CapptainUtils`의 이름은 `EngagementUtils`로 바뀌었습니다.
-   클래스 `CapptainViewController`의 이름은 `EngagementViewController`로 바뀌었습니다.

<!--HONumber=47-->
