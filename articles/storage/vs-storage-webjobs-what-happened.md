<properties 
	pageTitle="Azure 저장소 시작" 
	description="Visual Studio Azure WebJob 프로젝트에 Azure 저장소를 만들 때 변경 사항에 대해 설명합니다." 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# 내 프로젝트에서 무엇이 변경되었습니까?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

###<span id="whathappened">내 프로젝트에서 무엇이 변경되었습니까?</span>

##### 참조 추가됨

Azure Storage NuGet 패키지가 Visual Studio 프로젝트에 추가 또는 업데이트되었습니다. 이 패키지는 다음.NET 참조를 추가합니다.

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.ConfigurationManager`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####추가된 Azure 저장소에 대한 연결 문자열 
프로젝트의 App.config 파일에서 `AzureWebJobsStorage` 및 `AzureWebJobsDashboard`가 선택한 저장소 계정의 연결 문자열과 키로 업데이트되었습니다.

자세한 내용은 [Azure WebJob 권장 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.

<!---HONumber=July15_HO5-->