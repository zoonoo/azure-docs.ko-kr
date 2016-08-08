<properties 
	pageTitle="Visual Studio 연결된 서비스를 사용하여 Javascript 앱에 모바일 서비스를 추가할 경우 발생하는 상황 | Microsoft Azure" 
	description="Visual Studio에서 Azure 모바일 서비스 프로젝트의 변경 사항을 설명합니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="mlhoop" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="07/21/2016" 
	ms.author="mlearned"/>

# 연결 Visual Studio 서비스를 사용하여 Azure 모바일 서비스를 추가하면 내 Javascript 프로젝트는 어떻게 되나요?

##NuGet 패키지가 추가됨

`js\MobileServices.js` 파일의 Azure 모바일 서비스 라이브러리를 포함하여 **WindowsAzure.MobileServices.WinJS** NuGet 패키지가 설치되었습니다.
  
##모바일 서비스에 대한 연결 문자열 값 

`services\mobileServices\settings` 폴더에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 새 JavaScript(.js) 파일이 **MobileServiceClient**를 사용하여 생성되었습니다.

##default.html에 참조가 추가됨

`MobileServices.js` 및 설정 파일에 대한 참조가 `default.html`에 추가되었습니다.

##연결된 서비스 파일이 추가됨

서비스 폴더에 연결된 서비스 구성 파일이 추가되었습니다.



 

<!---HONumber=AcomDC_0727_2016-->