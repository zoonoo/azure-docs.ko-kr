<properties 
	pageTitle="Windows Phone Silverlight SDK 개요" 
	description="Azure Mobile Engagement용 Windows Phone Silverlight SDK의 개요" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/03/2016" 
	ms.author="piyushjo" />

#Azure Mobile Engagement의 Windows Phone Silverlight SDK 개요

이 문서에서는 Windows Phone Silverlight 앱에 Azure Mobile Engagement를 통합하는 방법에 대한 세부 사항을 확인할 수 있습니다. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-windows-phone-get-started.md)를 완료합니다.

[SDK 콘텐츠](mobile-engagement-windows-phone-sdk-content.md)를 보려면 클릭하세요.

##통합 절차

1. 시작: [Windows Phone Silverlight 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-windows-phone-integrate-engagement.md)

2. 알림: [Windows Phone Silverlight 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. 태그 계획 구현: [Windows Phone Silverlight 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-windows-phone-use-engagement-api.md)

##릴리스 정보

###3\.3.0(2016/04/19)
*MicrosoftAzure.MobileEngagement* Nuget 패키지 **v3.4.0**의 일부

-   SDK로 내보낸 콘솔 로그를 사용/사용 안 함/필터링하기 위해 "TestLogLevel" API를 추가했습니다.

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-windows-phone-release-notes.md)를 참조하세요.

##업그레이드 절차

이전 버전의 SDK를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 전체 [업그레이드 절차](mobile-engagement-windows-phone-upgrade-procedure.md)를 참조하세요. 예를 들어 0.10.1에서 0.11.0으로 마이그레이션하는 경우에는 먼저 "0.9.0에서 0.10.1로 마이그레이션" 절차를 수행한 후에 "0.10.1에서 0.11.0으로 마이그레이션" 절차를 수행해야 합니다.

###2\.0.0에서 3.3.0으로

####테스트 로그

이제 SDK에서 생성된 콘솔 로그를 사용/사용 안 함/필터링할 수 있습니다. 이를 사용자 지정하려면 속성 `EngagementAgent.Instance.TestLogEnabled`를 `EngagementTestLogLevel` 열거형에서 사용 가능한 값 중 하나로 업데이트합니다. 예를 들어 다음과 같습니다.

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

### 이전 버전에서 업그레이드

[업그레이드 절차](mobile-engagement-windows-phone-upgrade-procedure/)를 참조하세요.
 

<!---HONumber=AcomDC_0504_2016-->