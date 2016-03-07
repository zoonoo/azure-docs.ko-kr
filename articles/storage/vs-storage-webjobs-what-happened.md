<properties
	pageTitle="내 WebJob 프로젝트(Visual Studio Azure 저장소 연결 서비스)의 변경 내용 | Microsoft Azure"
	description="Visual Studio 연결 서비스를 사용하여 저장소 계정에 연결한 후 Azure WebJob 프로젝트의 변경 내용에 대해 설명합니다."
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# 내 WebJob 프로젝트(Visual Studio Azure 저장소 연결 서비스)의 변경 내용

## 참조 추가됨

Azure Storage NuGet 패키지가 Visual Studio 프로젝트에 추가 또는 업데이트되었습니다. 이 패키지는 다음.NET 참조를 추가합니다.

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## 추가된 Azure 저장소에 대한 연결 문자열
프로젝트의 App.config 파일에서 **AzureWebJobsStorage** 및 **AzureWebJobsDashboard** 항목이 선택한 저장소 계정의 연결 문자열과 키로 업데이트되었습니다.

자세한 내용은 [Azure WebJob 설명서 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.

<!---HONumber=AcomDC_0224_2016-->