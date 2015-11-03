<properties
	pageTitle="Azure 앱 서비스 모바일 앱에서 iOS 앱 만들기 | Microsoft Azure"
	description="이 자습서에 따라 Objective-C 또는 Swift로 iOS용 Azure 모바일 앱 백 엔드 사용을 시작합니다."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="10/14/2015"
	ms.author="krisragh"/>

#iOS 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 개요

이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 iOS 모바일 앱에 추가하는 방법을 보여 줍니다. 새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 _할 일 모음_ iOS 앱을 만듭니다.

이 자습서를 완료해야 Azure 앱 서비스에서 모바일 앱 기능을 사용하는 방법에 대한 다른 모든 iOS 자습서를 진행할 수 있습니다.

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [활성 Azure 계정](http://azure.microsoft.com/pricing/free-trial/)

* [Visual Studio Community 2013] 이상이 설치된 PC

* Xcode v7.0 이상을 사용하는 Mac

* 다운로드한 빠른 시작 프로젝트의 일부로 자동으로 포함된 [Azure 모바일 iOS 프레임워크](https://go.microsoft.com/fwLink/?LinkID=529823)

## 새 Azure 모바일 앱 백 엔드 만들기

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 서버 프로젝트 다운로드

1. 사용자 PC에서 [Azure 포털]을 방문합니다. **모두 찾아보기** > **모바일 앱**을 클릭한 다음 방금 만든 모바일 앱 백 엔드를 클릭합니다.

2. 모바일 앱 블레이드에서 **설정**을 클릭하고 **모바일 앱**에서 **빠른 시작** > **iOS(Objective-C)**를 클릭합니다. Swift를 원하는 경우 **빠른 시작** > **iOS(Swift)**를 대신 클릭합니다.

3. **서버 프로젝트 다운로드 및 실행**에서 **다운로드**를 클릭합니다. 귀하의 PC에 압축된 프로젝트 파일을 추출하고 Visual Studio에서 솔루션을 엽니다.

## Azure에 서버 프로젝트 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## iOS 앱 다운로드 및 실행

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure 포털]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Nov15_HO1-->