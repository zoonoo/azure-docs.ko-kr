<properties title="Azure 저장소 시작" pageTitle="Azure 저장소 시작" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [시작하기][시작하기]
> -   [변경된 내용][변경된 내용]

## Azure 저장소 시작(ASP.NET vNext 프로젝트)

> [AZURE.SELECTOR]
>
> -   [Blob][시작하기]
> -   [큐][큐]
> -   [테이블][테이블]

Azure Blob 저장소는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. 단일 Blob은 임의의 크기일 수 있습니다. Blob은 이미지, 오디오 및 비디오 파일, 원시 데이터, 문서 파일 등일 수 있습니다.

시작하려면 Azure 저장소 계정을 만든 다음 저장소에 컨테이너를 하나 이상 만들어야 합니다. 예를 들어 “Scrapbook”이라는 저장소를 만든 다음 저장소에 사진을 저장할 “images”라는 컨테이너를 만들고 오디오 파일을 저장할 “audio”라는 컨테이너를 만들 수 있습니다. 컨테이너를 만든 후 컨테이너에 개별 Blob 파일을 업로드할 수 있습니다. 프로그래밍 방식으로 Blob을 조작하는 방법에 대한 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법][.NET에서 Blob 저장소를 사용하는 방법](영문)을 참조하세요.

ASP.NET vNext 프로젝트에서 프로그래밍 방식으로 blob에 액세스하려면 다음 작업을 수행해야 합니다.

1.  프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

        using Microsoft.Framework.ConfigurationModel;

2.  다음 코드를 사용하여 구성 설정을 가져옵니다.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### 저장소 연결 문자열 가져오기

Blob으로 작업을 수행하려면 먼저 Blob을 저장할 저장소 계정의 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. ASP.NET vNext 프로젝트를 사용하는 경우 다음 코드에 나온 대로 구성 개체의 get 메서드를 호출하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져올 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

자세한 내용은 [ASP.NET vNext][ASP.NET vNext](영문)를 참조하세요.

  [시작하기]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [변경된 내용]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [큐]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [테이블]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [.NET에서 Blob 저장소를 사용하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-blobs/ ".NET에서 Blob 저장소를 사용하는 방법"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
