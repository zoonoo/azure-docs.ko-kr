<properties
	pageTitle="Azure Mobile Engagement iOS SDK 개요"
	description="Azure Mobile Engagement용 iOS SDK의 최신 업데이트 및 절차"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
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

#Azure Mobile Engagement용 iOS SDK

이 문서에서는 iOS 앱에 Azure Mobile Engagement를 통합하는 방법에 대한 모든 세부 사항을 확인할 수 있습니다. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-ios-get-started.md)의 단계를 진행하세요

[SDK 콘텐츠](mobile-engagement-ios-sdk-content.md)를 보려면 클릭하세요.

##통합 절차
1. 시작: [iOS 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-ios-integrate-engagement.md)

2. 알림: [iOS 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-ios-integrate-engagement-reach.md)

3. 태그 계획 구현: [iOS 앱에서 고급 Mobile Engagement API 태깅을 사용하는 방법](mobile-engagement-ios-use-engagement-api.md)


##릴리스 정보

###3\.2.1(2015/12/11)

-   딥 링크를 사용하는 알림에서 새 앱 인스턴스를 트리거할 때의 지연을 수정했습니다. 

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-ios-release-notes.md)를 참조하세요.

##업그레이드 절차

이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 관련 정보는 전체 [업그레이드 절차](mobile-engagement-ios-upgrade-procedure.md)를 참조하세요

SDK의 각 새 버전에 대해 먼저 EngagementSDK 및 EngagementReach 폴더를 대체해야 합니다. 즉, xcode에서 이 폴더를 제거한 후에 다시 가져와야 합니다.

###2\.0.0에서 3.0.0으로
iOS 4.X에 대한 지원을 삭제했습니다. 이 버전부터 응용 프로그램의 배포 대상은 iOS 6 이상이어야 합니다.

응용 프로그램에서 도달률을 사용하는 경우 원격 알림을 받기 위해 `remote-notification` 값을 Info.plist 파일의 `UIBackgroundModes` 배열에 추가해야 합니다.

메서드 `application:didReceiveRemoteNotification:`은 응용 프로그램 대리자의 `application:didReceiveRemoteNotification:fetchCompletionHandler:`로 대체해야 합니다.

"AEPushDelegate.h"는 더 이상 사용되지 않는 인터페이스이므로 모든 참조를 제거해야 합니다. 여기에는 응용 프로그램 대리자에서 `[[EngagementAgent shared] setPushDelegate:self]` 및 대리자 메서드를 제거하는 일도 포함됩니다.

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

<!---HONumber=AcomDC_1217_2015-->