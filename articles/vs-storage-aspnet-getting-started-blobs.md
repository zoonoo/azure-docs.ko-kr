<properties title="Getting Started with Azure Storage" pageTitle="Azure 저장소 시작" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="kempb" />

> [AZURE.SELECTOR]
> - [시작하기](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [변경된 내용](/documentation/articles/vs-storage-aspnet-what-happened/)

## Azure 저장소 시작(ASP.NET 프로젝트)

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [큐](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [테이블](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

Azure Blob 저장소는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. 단일 Blob은 임의의 크기일 수 있습니다. Blob은 이미지, 오디오 및 비디오 파일, 원시 데이터, 문서 파일 등일 수 있습니다.

시작하려면 Azure 저장소 계정을 만든 다음 저장소에 컨테이너를 하나 이상 만들어야 합니다. 예를 들어 "Scrapbook"이라는 저장소를 만든 다음 저장소에 사진을 저장할 "images"라는 컨테이너를 만들고 오디오 파일을 저장할 "audio"라는 컨테이너를 만들 수 있습니다. 컨테이너를 만든 후 컨테이너에 개별 Blob 파일을 업로드할 수 있습니다. 프로그래밍 방식으로 Blob을 조작하는 방법에 대한 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET")(영문)을 참조하세요.

프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

#####저장소 연결 문자열 가져오기
Blob으로 작업을 수행하려면 먼저 Blob을 저장할 저장소 계정의 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. ASP.NET 프로젝트의 경우 다음 코드에 나온 대로 **ConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 검색할 수 있습니다.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

자세한 내용은 [ASP.NET](http://www.asp.net)(영문)을 참조하세요.
