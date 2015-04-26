<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [시작](vs-mobile-services-cordova-getting-started.md)
> - [변경된 내용](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">내 프로젝트에서 무엇이 변경되었나요?</span>

#####참조 추가됨

모든 다중 장치 하이브리드 앱에 포함된 Azure 모바일 서비스 클라이언트 플러그 인이 사용하도록 설정되었습니다.
  
#####모바일 서비스에 대한 연결 문자열 값

 `services\mobileServices\settings` 아래에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 새 JavaScript(.js) 파일이 **MobileServiceClient**를 사용하여 생성되었습니다. 이 파일에는 모바일 서비스 클라이언트 개체의 초기화가 포함되어 있는데, 이 코드는 다음과 유사합니다.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[모바일 서비스에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/mobile-services/)


<!--HONumber=42-->
