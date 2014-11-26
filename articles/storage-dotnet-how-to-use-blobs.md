<properties linkid="dev-net-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# .NET에서 Blob 저장소를 사용하는 방법

이 가이드에서는 Azure Blob 저장소 서비스를 사용하여 일반 시나리오를
수행하는 방법을 보여 줍니다. 샘플은 C#으로 작성되었으며
Azure Storage Client Library for .NET을 사용합니다. Blob
**업로드**,
**나열**,
**다운로드** 및
**삭제** 시나리오를 다룹니다. Blob에
대한 자세한 내용은 [다음 단계][다음 단계] 섹션을 참조하세요.

> [WACOM.NOTE] 이 가이드는 Azure .NET Storage Client Library 2.x 이상을 대상으로 합니다. 권장되는 버전은 [NuGet][NuGet]을 통해 또는 [Azure SDK for .NET][Azure SDK for .NET]의 일부로 사용할 수 있는 Storage Client Library 4.x입니다. 저장소 클라이언트 라이브러리를 구하는 방법은 아래의 [방법: 프로그래밍 방식으로 Blob 저장소 액세스][방법: 프로그래밍 방식으로 Blob 저장소 액세스]를 참조하세요.

## 목차

-   [Blob 저장소 정의][Blob 저장소 정의]
-   [개념][개념]
-   [Azure 저장소 계정 만들기][Azure 저장소 계정 만들기]
-   [저장소 연결 문자열 설정][저장소 연결 문자열 설정]
-   [방법: 프로그래밍 방식으로 Blob 저장소 액세스][방법: 프로그래밍 방식으로 Blob 저장소 액세스]
-   [방법: 컨테이너 만들기][방법: 컨테이너 만들기]
-   [방법: 컨테이너에 Blob 업로드][방법: 컨테이너에 Blob 업로드]
-   [방법: 컨테이너의 Blob 나열][방법: 컨테이너의 Blob 나열]
-   [방법: Blob 다운로드][방법: Blob 다운로드]
-   [방법: Blob 삭제][방법: Blob 삭제]
-   [다음 단계][다음 단계]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"></a><span class="short-header">계정 만들기</span>Azure 저장소 계정 만들기

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span class="short-header">연결 문자열 설정</span>저장소 연결 문자열 설정

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">프로그래밍 방식으로 액세스</span>방법: 프로그래밍 방식으로 Blob 저장소 액세스

### 어셈블리 가져오기

NuGet을 사용하여 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 가져올 수 있습니다. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

`Microsoft.WindowsAzure.Storage.dll`은 [.NET 개발자 센터][.NET 개발자 센터](영문)에서 다운로드할 수 있는 Azure SDK for .NET에도 포함되어 있습니다. 이 어셈블리는 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` 디렉터리에 설치됩니다.

### 네임스페이스 선언

프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의
맨 위에 다음과 같은 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

`Microsoft.WindowsAzure.Storage.dll` 어셈블리를 참조해야 합니다.

### 연결 문자열 검색

**CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를
나타낼 수 있습니다. Azure 프로젝트
템플릿을 사용 중이거나 Microsoft.WindowsAzure.CloudConfigurationManager에
대한 참조가 있는 경우 **CloudConfigurationManager**
유형을 사용하여 Azure 서비스
구성에서 저장소 연결 문자열 및 저장소 계정 정보를
다음과 같이 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조 없이 응용 프로그램을 만드는 중이며 위에 표시된 대로 연결 문자열이 `web.config` 또는 `app.config`에 있는 경우 **ConfigurationManager**를 사용하여 연결 문자열을 검색할 수 있습니다. System.Configuration.dll에 대한 참조를 프로젝트에 추가하고 다른 네임스페이스 선언을 추가해야 합니다.

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

**CloudBlobClient** 유형을 사용하면 Blob 저장소 서비스에 저장된
Blob과 컨테이너를 나타내는 개체를 검색할 수 있습니다. 다음
코드는 위에서 검색한 저장소 계정 개체를 사용하여 **CloudBlobClient** 개체를
만듭니다.

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### ODataLib 종속성

Storage Client Library for .NET의 ODataLib 종속성은 WCF 데이터 서비스가 아니라 NuGet을 통해 사용 가능한 ODataLib(버전 5.0.2) 패키지를 통해 확인됩니다. ODataLib 라이브러리를 직접 다운로드하거나 NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다. 특정 ODataLib 패키지는 [OData][OData], [Edm][Edm] 및 [Spatial][Spatial]입니다.

## <a name="create-container"> </a><span class="short-header">컨테이너 만들기</span>방법: 컨테이너 만들기

모든 저장소 Blob은 컨테이너에 있습니다.
**CloudBlobClient** 개체를 사용하여 사용할 컨테이너에 대한 참조를 가져올
수 있습니다. 컨테이너가 없는 경우 새로 만들 수 있습니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

기본적으로 새 컨테이너는 전용이며, 이 컨테이너에서 Blob을
다운로드하려면 저장소 액세스 키를
지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있게 하려는 경우
다음 코드를 사용하여 컨테이너를 공용으로 설정할 수
있습니다.

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 

인터넷상의 누구든지 공용 컨테이너의 Blob을 볼 수 있지만 해당 액세스 키가
있는 경우에만 수정하거나 삭제할 수 있습니다.

## <a name="upload-blob"> </a><span class="short-header">컨테이너에 업로드</span>방법: 컨테이너에 Blob 업로드

Azure Blob 저장소는 블록 Blob 및 페이지 Blob을 지원합니다. 대부분의 경우 블록 Blob을 사용하는 것이 좋습니다.

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 이 참조를 사용하여
블록 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **UploadFromStream** 메서드를
호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이
있는 경우 덮어씁니다. 다음 예제에서는 컨테이너에 Blob을 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span class="short-header">컨테이너의 Blob 나열</span>방법: 컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져옵니다. 컨테이너의
**ListBlobs** 메서드를 사용하여 컨테이너 내의 Blob 및/또는 디렉터리를
검색할 수 있습니다. 반환된 **IListBlobItem**에 대한
풍부한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**,
**CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트해야 합니다. 형식을 알 수 없는 경우 형식 검사를
사용하여 캐스트할 형식을 확인할 수 있습니다. 다음 코드에서는
`photos` 컨테이너에 있는 각 항목의
URI를 검색하고 출력하는 방법을 보여 줍니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;
            
            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

위에 표시된 것처럼 Blob 서비스에도 컨테이너 내의 디렉터리
개념이 있습니다. 따라서 폴더와 유사한 구조로 Blob을 구성할 수
있습니다. 예를 들어 `photos`라는 컨테이너에 있는 다음
블록 Blob 집합을 고려합니다.

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

위 샘플과 같이 'photos' 컨테이너에서 **ListBlobs**를 호출하면 반환된 컬렉션에 디렉터리를
나타내는 **CloudBlobDirectory** 및 **CloudBlockBlob** 개체와
최상위 수준의 Blob이 포함됩니다. 결과 출력은 다음과 같습니다.

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

선택적으로, **ListBlobs** 메서드의 **UseFlatBlobListing** 매개 변수를
**true**로 설정할 수 있습니다. 이 경우 디렉터리에 관계없이 모든 Blob이 **CloudBlockBlob**
으로 반환됩니다. **ListBlobs** 호출은 다음과 같습니다.

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

결과는 다음과 같습니다.

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

자세한 내용은 [CloudBlobContainer.ListBlobs][CloudBlobContainer.ListBlobs]를 참조하세요.

## <a name="download-blobs"> </a><span class="short-header">Blob 다운로드</span>방법: Blob 다운로드

Blob을 다운로드하려면 먼저 Blob 참조를 검색한 다음 **DownloadToStream** 메서드를 호출합니다. 다음 예제에서는
**DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로
전송한 다음 이 개체를 로컬 파일에 저장할 수 있습니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

**DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 텍스트 문자열로 다운로드할 수도 있습니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"> </a><span class="short-header">Blob 삭제</span>방법: Blob 삭제

Blob을 삭제하려면 먼저 Blob 참조를 가져온 다음
**Delete** 메서드를 호출합니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 

## <a name="next-steps"></a><span class="short-header">다음 단계</span>다음 단계

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라
좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보세요.

-   사용 가능한 API에 대한 자세한 내용은 Blob 서비스 참조 설명서를 참조하세요.
    -   [Storage Client Library for .NET 참조][Storage Client Library for .NET 참조]
    -   [REST API 참조][REST API 참조]

-   Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 [Azure에 데이터 저장 및 액세스][Azure에 데이터 저장 및 액세스]를 참조하세요.
-   [Azure WebJobs SDK 시작][Azure WebJobs SDK 시작](영문)에서 Azure 웹 사이트에 대한 백 엔드 프로세스에서 Azure 저장소를 사용하는 방법을 알아보세요.
-   Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하세요.
    -   [테이블 저장소][테이블 저장소]를 사용하여 구조화된 데이터를 저장합니다.
    -   [큐 저장소][큐 저장소]를 사용하여 구조화되지 않은 데이터를 저장합니다.
    -   [SQL 데이터베이스][SQL 데이터베이스]를 사용하여 관계형 데이터를 저장합니다.

</p>

  [다음 단계]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK for .NET]: /ko-kr/downloads/
  [방법: 프로그래밍 방식으로 Blob 저장소 액세스]: #configure-access
  [Blob 저장소 정의]: #what-is
  [개념]: #concepts
  [Azure 저장소 계정 만들기]: #create-account
  [저장소 연결 문자열 설정]: #setup-connection-string
  [방법: 컨테이너 만들기]: #create-container
  [방법: 컨테이너에 Blob 업로드]: #upload-blob
  [방법: 컨테이너의 Blob 나열]: #list-blob
  [방법: Blob 다운로드]: #download-blobs
  [방법: Blob 삭제]: #delete-blobs
  [.NET 개발자 센터]: http://www.windowsazure.com/ko-kr/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [Storage Client Library for .NET 참조]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API 참조]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dd179355
  [Azure에 데이터 저장 및 액세스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx
  [Azure WebJobs SDK 시작]: /ko-kr/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [테이블 저장소]: /ko-kr/documentation/articles/storage-dotnet-how-to-use-tables/
  [큐 저장소]: /ko-kr/documentation/articles/storage-dotnet-how-to-use-queues/
  [SQL 데이터베이스]: /ko-kr/documentation/articles/sql-database-dotnet-how-to-use/
