<properties 
	pageTitle="Azure 저장소 시작" 
	description="Visual Studio 클라우드 서비스 프로젝트에서 Azure 저장소를 만들 때 발생하는 상황을 설명합니다." 
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
> - [시작하기](vs-storage-cloud-services-getting-started-blobs.md)
> - [변경된 내용](vs-storage-cloud-services-what-happened.md)

###<span id="whathappened">내 프로젝트에서 무엇이 변경되었습니까?</span>

###### 참조 추가됨

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

######추가된 Azure 저장소에 대한 연결 문자열 
선택한 저장소 계정의 연결 문자열과 키를 포함하는 요소가 생성되었습니다. 다음 파일이 수정되었습니다.

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`


<!--HONumber=46--> 
