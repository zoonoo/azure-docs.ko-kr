<properties 
	pageTitle="" 
	description="Cordova에서 Azure 모바일 서비스 프로젝트의 변경 사항을 설명합니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/12/2015" 
	ms.author="patshea"/>

# 내 프로젝트에서 무엇이 변경되었습니까?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

##참조 추가됨

모든 다중 장치 하이브리드 앱에 포함된 Azure 모바일 서비스 클라이언트 플러그 인이 사용하도록 설정되었습니다.
  
##모바일 서비스에 대한 연결 문자열 값

`services\mobileServices\settings` 아래에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 새 JavaScript(.js) 파일이 **MobileServiceClient**를 사용하여 생성되었습니다. 이 파일에는 모바일 서비스 클라이언트 개체의 초기화가 포함되어 있는데, 이 코드는 다음과 유사합니다.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[모바일 서비스에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=August15_HO7-->