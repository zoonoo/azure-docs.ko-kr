<properties
	pageTitle="Azure Mobile Engagement Android SDK 통합"
	description="Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="piyushjo" />


#Azure Mobile Engagement용 Android SDK

Android 앱에서 Azure Mobile Engagement를 통합하는 방법에 대한 모든 자세한 내용을 보려면 여기에서 시작하세요. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-android-get-started.md)의 단계를 진행하세요

[SDK 콘텐츠](mobile-engagement-android-sdk-content.md)를 보려면 클릭하세요.

##통합 절차
1. 시작: [Android 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-android-integrate-engagement.md)

2. 알림: [Android 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [GCM과 Mobile Engagement를 통합하는 방법](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [ADM과 Mobile Engagement를 통합하는 방법](mobile-engagement-android-adm-integrate.md)

3. 태그 계획 구현: [Android 앱에서 고급 Mobile Engagement API 태깅을 사용하는 방법](mobile-engagement-android-use-engagement-api.md)


##릴리스 정보

##4\.1.3(2015/12/9)

- 안정성 향상

##4\.1.0(08/25/2015)

- Android M에 대한 새 권한 모델을 다룹니다.
- `AndroidManifest.xml`을 사용하는 대신 이제 런타임 시 위치 기능을 구성할 수 있습니다.
- 사용 권한 버그 수정: `ACCESS_FINE_LOCATION`을 사용하는 경우 `ACCESS_COARSE_LOCATION`이 더 이상 필요하지 않습니다.
- 안정성 향상

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-android-release-notes.md)를 참조하세요.

##업그레이드 절차

이전 버전의 SDK를 응용 프로그램에 이미 통합한 경우에는 [업그레이드 절차](mobile-engagement-android-upgrade-procedure.md)를 참조하세요.

<!---HONumber=AcomDC_1210_2015-->