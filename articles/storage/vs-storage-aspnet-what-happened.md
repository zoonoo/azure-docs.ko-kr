<properties
	pageTitle="내 ASP.NET 프로젝트의 변경 내용 | Microsoft Azure | Visual Studio 연결 서비스"
	description="Visual Studio 연결 서비스를 사용하여 ASP.NET 프로젝트에 Azure 저장소를 추가한 후 변경 내용에 대해 설명합니다."
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="tarcher"/>

# 내 ASP.NET 프로젝트(Visual Studio Azure 저장소 연결 서비스)의 변경 내용

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-aspnet-getting-started-blobs.md)
> - [What happened](vs-storage-aspnet-what-happened.md)

## 참조 추가됨

Azure Storage NuGet 패키지가 Visual Studio 프로젝트에 추가되었습니다. 이 패키지는 다음.NET 참조를 추가합니다.

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

##추가된 Azure 저장소에 대한 연결 문자열
프로젝트의 web.config 파일에 선택한 저장소 계정의 연결 문자열과 키를 포함하는 요소가 생성되었습니다.

자세한 내용은 [ASP.NET](http://www.asp.net)을 참조하세요.

<!---HONumber=Nov15_HO3-->