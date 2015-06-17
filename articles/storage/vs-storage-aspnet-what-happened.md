<properties 
	pageTitle="Azure 저장소 시작" 
	description="Visual Studio ASP.NET 프로젝트에는 Azure 저장소를 만들 때 변경 사항에 대해 설명 합니다." 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [시작하기](vs-storage-aspnet-getting-started-blobs.md)
> - [변경된 내용](vs-storage-aspnet-what-happened.md)

###<span id="whathappened">내 프로젝트에서 무엇이 변경되었나요?</span>

##### 참조 추가됨

Azure Storage NuGet 패키지가 Visual Studio 프로젝트에 추가되었습니다.  
이 패키지는 다음.NET 참조를 추가합니다.

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####추가된 Azure 저장소에 대한 연결 문자열 
프로젝트의 web.config 파일에 선택한 저장소 계정의 연결 문자열과 키를 포함하는 요소가 생성되었습니다.

자세한 내용은 [ASP.NET](http://www.asp.net)를 참조하세요.
<!--HONumber=47-->
 