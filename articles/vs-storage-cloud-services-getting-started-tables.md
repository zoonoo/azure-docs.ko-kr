<properties title="Getting Started with Azure Storage" pageTitle="Azure 저장소 시작" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [시작하기](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)
> - [변경된 내용](/documentation/articles/vs-storage-cloud-services-what-happened/)

##Azure 저장소 시작(클라우드 서비스 프로젝트)

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [큐](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [테이블](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Azure 테이블 저장소 서비스를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다.  자세한 내용은 [.NET에서 테이블 저장소를 사용하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET")(영문)을 참조하세요.

프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

#####저장소 연결 문자열 가져오기
테이블로 작업을 수행하려면 먼저 테이블을 저장할 저장소 계정의 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. 클라우드 서비스 프로젝트의 경우 다음 코드에 나온 대로 **CloudConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 검색할 수 있습니다.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]
