<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro](../includes/vs-storage-aspnet-getting-started-intro.md)]

### Azure 저장소 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/vs-storage-aspnet-getting-started-blobs" title="Blob" class="current">Blob</a><a href="/ko-kr/documentation/articles/vs-storage-aspnet-getting-started-queues" title="큐">큐</a><a href="/ko-kr/documentation/articles/vs-storage-aspnet-getting-started-tables" title="테이블">테이블</a></div>

Azure 테이블 저장소 서비스를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다. 자세한 내용은 [.NET에서 테이블 저장소를 사용하는 방법][.NET에서 테이블 저장소를 사용하는 방법](영문)을 참조하세요.

ASP.NET 프로젝트에서 프로그래밍 방식으로 테이블에 액세스하려면 다음 작업을 수행해야 합니다.

1.  Microsoft.WindowsAzure.Storage.dll 어셈블리를 가져옵니다. NuGet을 사용하여 가져올 수 있습니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다. 온라인에서 "WindowsAzure.Storage"를 검색하고 설치를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다. 이 어셈블리에 대한 참조를 프로젝트에 추가합니다.
2.  프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

###### 저장소 연결 문자열 가져오기

테이블로 작업을 수행하려면 먼저 테이블을 저장할 저장소 계정의 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. ASP.NET 프로젝트의 경우 다음 코드에 나온 대로 **ConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [Blob]: /ko-kr/documentation/articles/vs-storage-aspnet-getting-started-blobs "Blob"
  [큐]: /ko-kr/documentation/articles/vs-storage-aspnet-getting-started-queues "큐"
  [테이블]: /ko-kr/documentation/articles/vs-storage-aspnet-getting-started-tables "테이블"
  [.NET에서 테이블 저장소를 사용하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-tables/#create-table ".NET에서 테이블 저장소를 사용하는 방법"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
