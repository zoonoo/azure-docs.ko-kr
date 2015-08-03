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
	ms.date="05/04/2015" 
	ms.author="piyushjo" />

#Azure Mobile Engagement용 iOS SDK

이 문서에서는 iOS 앱에 Azure Mobile Engagement를 통합하는 방법에 대한 모든 세부 사항을 확인할 수 있습니다. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-ios-get-started.md)의 단계를 진행하세요

[SDK 콘텐츠](mobile-engagement-ios-sdk-content.md)를 보려면 클릭하세요.

##통합 절차
1. 시작: [iOS 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-ios-integrate-engagement.md)

2. 알림: [iOS 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-ios-integrate-engagement-reach.md)

3. 태그 계획 구현: [iOS 앱에서 고급 Mobile Engagement API 태깅을 사용하는 방법](mobile-engagement-ios-use-engagement-api.md)


##릴리스 정보

###2.2.0(05/21/2015)

-   iOS 6 이전 장치에 대한 Mobile Engagement 장치 ID는 설치 시 생성된 GUID를 따릅니다.

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-ios-release-notes.md)를 참조하세요.

##업그레이드 절차

이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 관련 정보는 전체 [업그레이드 절차](mobile-engagement-ios-upgrade-procedure.md)를 참조하세요

SDK의 각 새 버전에 대해 먼저 EngagementSDK 및 EngagementReach 폴더를 대체해야 합니다. 즉, xcode에서 이 폴더를 제거한 후에 다시 가져와야 합니다.

###2.0.0에서 2.1.0으로 마이그레이션
없음
 

<!---HONumber=July15_HO4-->