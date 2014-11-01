<properties title="Azure 저장소 시작" pageTitle="Azure 저장소 시작" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [시작하기][시작하기]
> -   [변경된 내용][변경된 내용]

## Azure 저장소 시작(ASP.NET vNext 프로젝트)

> [AZURE.SELECTOR]
>
> -   [Blob][Blob]
> -   [큐][큐]
> -   [테이블][시작하기]

Azure 테이블 저장소 서비스를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다. 자세한 내용은 [.NET에서 테이블 저장소를 사용하는 방법][.NET에서 테이블 저장소를 사용하는 방법](영문)을 참조하세요.

ASP.NET vNext 프로젝트에서 프로그래밍 방식으로 테이블에 액세스하려면 다음 작업을 수행해야 합니다.

1.  프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

        using Microsoft.Framework.ConfigurationModel;

2.  다음 코드를 사용하여 구성 설정을 가져옵니다.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### 저장소 연결 문자열 가져오기

테이블로 작업을 수행하려면 먼저 테이블을 저장할 저장소 계정의 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. ASP.NET vNext 프로젝트를 사용하는 경우 다음 코드에 나온 대로 구성 개체의 get 메서드를 호출하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져올 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

자세한 내용은 [ASP.NET vNext][ASP.NET vNext](영문)를 참조하세요.

  [시작하기]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [변경된 내용]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Blob]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [큐]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [.NET에서 테이블 저장소를 사용하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-tables/#create-table ".NET에서 테이블 저장소를 사용하는 방법"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
