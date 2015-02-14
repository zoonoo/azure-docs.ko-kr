<properties 
	pageTitle="Azure 저장소 시작" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [시작하기](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [변경된 내용](/documentation/articles/vs-storage-aspnet-what-happened/)

## Azure 저장소 시작(ASP.NET 프로젝트)

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [큐](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [테이블](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

Azure 큐 저장소는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다. 단일 큐 메시지의 크기는 최대 64KB일 수 있으며, 하나의 큐에 저장소 계정의 총 용량 제한까지 수백만 개의 메시지가 포함될 수 있습니다. 자세한 내용은 [.NET에서 큐 저장소를 사용하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-queues/)(영문)을 참조하세요.

프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

##### 저장소 연결 문자열 가져오기
큐로 작업을 수행하려면 먼저 큐를 저장할 저장소 계정의 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. ASP.NET 프로젝트의 경우 다음 코드에 나온 대로 **ConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 검색할 수 있습니다.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

자세한 내용은 [ASP.NET](http://www.asp.net)(영문)을 참조하세요.<!--HONumber=42-->
