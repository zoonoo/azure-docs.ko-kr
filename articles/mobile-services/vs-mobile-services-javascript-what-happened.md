<properties 
	pageTitle="" 
	description="Visual Studio에서 Azure 모바일 서비스 프로젝트의 변경 사항을 설명합니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="patshea123"/>

# 내 프로젝트에서 무엇이 변경되었습니까?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###내 프로젝트에서 무엇이 변경되었나요?</id>

#####NuGet 패키지가 추가됨

`js\MobileServices.js` 파일의 Azure 모바일 서비스 라이브러리를 포함하여 **WindowsAzure.MobileServices.WinJS** NuGet 패키지가 설치되었습니다.
  
#####모바일 서비스에 대한 연결 문자열 값 

`services\mobileServices\settings` 폴더에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 새 JavaScript(.js) 파일이 **MobileServiceClient**를 사용하여 생성되었습니다.


#####default.html에 참조가 추가됨

`MobileServices.js` 및 설정 파일에 대한 참조가 `default.html`에 추가되었습니다.


#####연결된 서비스 파일이 추가됨

서비스 폴더에 연결된 서비스 구성 파일이 추가되었습니다.



 

<!---HONumber=July15_HO5-->