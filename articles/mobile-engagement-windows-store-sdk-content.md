<properties 
	pageTitle="Azure Mobile Engagement Windows 스토어 SDK 콘텐츠" 
	description="Azure Mobile Engagement용 Windows 스토어 SDK의 최신 업데이트 및 절차" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#SDK 콘텐츠

이 문서에서는 SDK 보관 파일의 콘텐츠를 소개 및 설명합니다.

## `Root` 폴더

이 폴더에는 소프트웨어 라이선스 복사본과 온라인 설명서의 링크가 포함되어 있습니다.

`documentation.html` : Windows 8 C\# Metro 응용 프로그램용 Engagement SDK 온라인 설명서의 링크가 포함되어 있습니다.

또한 라이선스 파일도 들어 있습니다.

## `/lib` 폴더

이 폴더에는 Engagement SDK를 다운로드할 수 있는 위치에 대한 정보가 포함되어 있습니다.

`azuresdk-mobileengagement-windows-X.X.X.nupkg` : SDK 통합용 NuGet 패키지입니다.

## `/Resources` 폴더

이 폴더에는 Engagement에 필요한 모든 리소스가 포함되어 있습니다. 앱에 맞게 리소스를 사용자 지정할 수도 있습니다.

`EngagementConfiguration.xml` : Engagement 구성 파일입니다. 이 파일에서 Engagement 연결 문자열, 작동 중단 보고 등의 Engagement 설정을 사용자 지정할 수 있습니다.

### /html 폴더

`EngagementNotification.html` :  `알림(Notification)` 웹 보기 html 디자인입니다.

`EngagementAnnouncement.html` :  `알림(announcement)` 웹 보기 html 디자인입니다.

### /images 폴더

`EngagementIconNotification.png` : 알림 왼쪽에 표시되는 브랜드 아이콘입니다.

`EngagementIconOk.png` : 도달률 콘텐츠 페이지의 작업 또는 유효성 검사 단추용 `확인` 아이콘입니다.

`EngagementIconNOK.png` : 도달률 콘텐츠 페이지의 유효성 검사 단추를 사용하지 않도록 설정하면 `NOK` 아이콘이 사용됩니다.

`EngagementIconClose.png` : 도달률 알림 및 콘텐츠의 해제 단추용 `닫기` 아이콘입니다.

### /overlay 폴더

`EngagementOverlayAnnouncement.xaml` :  `알림(announcement)` xaml 디자인입니다.

`EngagementOverlayAnnouncement.xaml.cs` :  `EngagementOverlayAnnouncement.xaml` 연결된 코드입니다.

`EngagementOverlayNotification.xaml` :  `알림(Notification)` xaml 디자인입니다.

`EngagementOverlayNotification.xaml.cs` :  `EngagementOverlayNotification.xaml` 연결된 코드입니다.

`EngagementPageOverlay.cs` :  `오버레이` 알림(announcement 및 notification) 표시 코드입니다.

## `/src/agent` 폴더

이 폴더에는 EngagementPage가 포함되어 있습니다.

`EngagementPage.cs` : Engagement에 대한 활동을 자동으로 보고하는 페이지의 기본 클래스입니다.

<!--HONumber=47-->
