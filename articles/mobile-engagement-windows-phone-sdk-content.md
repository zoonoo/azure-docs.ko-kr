<properties 
	pageTitle="Azure Mobile Engagement Windows Phone SDK 통합" 
	description="Azure Mobile Engagement용 Windows Phone SDK의 콘텐츠에 대해 살펴봅니다." 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
#SDK 콘텐츠

이 문서에서는 SDK 보관 파일의 콘텐츠를 소개 및 설명합니다.

## `Root` 폴더

이 폴더에는 소프트웨어 라이선스 복사본과 온라인 설명서의 링크가 포함되어 있습니다.

`documentation.html`: Windows Phone용 Engagement SDK 온라인 설명서의 링크가 포함되어 있습니다.

또한 라이선스 파일도 들어 있습니다.

## `/lib` 폴더

이 폴더에는 Engagement SDK를 다운로드할 수 있는 위치에 대한 정보가 포함되어 있습니다.

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` : SDK 통합용 NuGet 패키지입니다.

## `/Resources` 폴더

이 폴더에는 Engagement에 필요한 모든 리소스가 포함되어 있습니다. 앱에 맞게 리소스를 사용자 지정할 수도 있습니다.

`EngagementIconNotification.png` : 알림 왼쪽에 표시되는 브랜드 아이콘입니다.

`EngagementIconOk.png` : 도달률 페이지 ApplicationBar의 "확인" 아이콘입니다.

`EngagementIconCancel.png` : 도달률 페이지 ApplicationBar의 "취소" 아이콘입니다.

`EngagementIconCloseLight.png` : Windows Phone 밝은 테마용 Engagement 도달률 알림의 "닫기" 아이콘입니다.

`EngagementIconCloseDark.png` : Windows Phone 어두운 테마용 Engagement 도달률 알림의 "닫기" 아이콘입니다.

`EngagementConfiguration.xml` : Engagement 구성 파일입니다. 이 파일에서 Engagement 연결 문자열, 작동 중단 보고 등의 Engagement 설정을 사용자 지정할 수 있습니다.

## `/src/agent` 폴더

이 폴더에는 EngagementPage가 포함되어 있습니다.

`EngagementPage.cs` : Engagement에 대한 활동을 자동으로 보고하는 페이지의 기본 클래스입니다.

## `/src/reach` 폴더

마지막으로 이 폴더에는 각 페이지의 기본 XAML과 그에 해당하는 C\# 항목을 확인할 수 있습니다.

직접 페이지를 만들 때 이 폴더의 항목을 기준으로 사용할 수 있습니다. 주석에 따라 작업을 수행하면 됩니다.

### TextView 알림

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### WebView 알림

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### 설문 조사

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### 알림

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
