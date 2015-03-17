<properties 
	pageTitle=".NET에서 Blob 저장소를 사용하는 방법 | Azure" 
	description="Microsoft Azure Blob 저장소를 사용하여 Blob 콘텐츠를 업로드, 다운로드, 나열 및 삭제하는 방법에 대해 알아봅니다. 샘플은 C#으로 작성되었습니다." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>

# .NET에서 Blob 저장소를 사용하는 방법

이 가이드에서는 Azure Blob 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C#으로 작성되었으며 Azure Storage Client Library for .NET을 사용합니다. 여기서 다루는 시나리오에는 Blob **업로드**, **나열**, **다운로드** 및 **삭제**가 포함됩니다. Blob에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

> [AZURE.NOTE] 이 가이드는 Azure .NET Storage Client Library 2.x 이상을 대상으로 합니다. 권장되는 버전은 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/)을 통해 또는 [Azure SDK for .NET](/ko-kr/downloads/)의 일부로 사용할 수 있는 Storage Client Library 4.x입니다. 저장소 클라이언트 라이브러리를 구하는 방법은 아래의 [방법: 프로그래밍 방식으로 Blob 저장소 액세스][]를 참조하세요.

##목차

-   [Blob 저장소 정의][]
-   [개념][]
-   [Azure 저장소 계정 만들기][]
-   [저장소 연결 문자열 설정][]
-   [방법: 프로그래밍 방식으로 Blob 저장소에 액세스][]
-   [방법: 컨테이너 만들기][]
-   [방법: 컨테이너에 Blob 업로드][]
-   [방법: 컨테이너의 Blob 나열][]
-   [방법: Blob 다운로드][]
-   [방법: Blob 삭제][]
-   [방법: 여러 페이지에서 비동기식으로 Blob 나열][]
-   [다음 단계][]

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>방법: 프로그래밍 방식으로 Blob 저장소에 액세스

###어셈블리 가져오기 NuGet을 사용하여  `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 가져오는 것이 좋습니다. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.  온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지 및 종속성을 설치합니다.

`Microsoft.WindowsAzure.Storage.dll`은 <a href="http://azure.microsoft.com/develop/net/#">.NET 개발자 센터</a>에서 다운로드할 수 있는 Azure SDK for .NET에도 포함되어 있습니다. 이 어셈블리는  `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` 디렉터리에 설치됩니다.

###네임스페이스 선언 프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 참조해야 합니다.

###연결 검색 **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. Azure 프로젝트 템플릿을 사용 중이거나 Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조가 있는 경우 **CloudConfigurationManager** 유형을 사용하여 Azure 서비스구성에서 저장소 연결 문자열 및 저장소 계정 정보를 다음과 같이 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조 없이 응용 프로그램을 만드는 중이며 위에 표시된 대로 연결 문자열이 `web.config` 또는 `app.config`에 있는 경우 **ConfigurationManager**를 사용하여 연결 문자열을 검색할 수 있습니다. System.Configuration.dll에 대한 참조를 프로젝트에 추가하고 다른 네임스페이스 선언을 추가해야 합니다.

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

**CloudBlobClient** 유형을 사용하면 Blob 저장소 서비스에 저장된 Blob와 컨테이너를 나타내는 개체를 검색할 수 있습니다. 다음 코드는 위에서 검색한 저장소 계정 개체를 사용하여 **CloudBlobClient** 개체를 만듭니다.

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###ODataLib 종속성 Storage Client Library for .NET의 ODataLib 종속성은 WCF 데이터 서비스가 아니라 NuGet을 통해 사용 가능한 ODataLib(버전 5.0.2) 패키지를 통해 확인됩니다. ODataLib 라이브러리를 직접 다운로드하거나 NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다. 특정 ODataLib 패키지는 [OData], [Edm] 및 [Spatial]입니다.

## <a name="create-container"> </a>방법: 컨테이너 만들기

Azure 저장소의 모든 Blob는 컨테이너에 있어야 합니다. 이 예제에서는 컨테이너가 없는 경우 만드는 방법을 보여 줍니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

기본적으로 새 컨테이너는 전용이며, 이 컨테이너에서 Blob를 다운로드하려면 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있게 하려는 경우 다음 코드를 사용하여 컨테이너를 공용으로 설정할 수 있습니다.

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

인터넷상의 누구든지 공용 컨테이너의 Blob를 볼 수 있지만 해당 액세스 키가 있는 경우에만 수정하거나 삭제할 수 있습니다.

## <a name="upload-blob"> </a>방법: 컨테이너에 Blob 업로드

Azure Blob 저장소는 블록 Blob 및 페이지 Blob를 지원합니다. 대부분의 경우 블록 Blob를 사용하는 것이 좋습니다.

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 이 참조를 사용하여 블록 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **UploadFromStream** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob가 없는 경우 새로 만들고, Blob가 있는 경우 덮어씁니다. 다음 예제에서는 컨테이너에 Blob를 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

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

##<a name="list-blob"> </a>방법: 컨테이너의 Blob 나열

컨테이너의 Blob를 나열하려면 먼저 컨테이너 참조를 가져옵니다. 그런 다음 컨테이너의 **ListBlobs** 메서드를 사용하여 컨테이너 내의 Blog 및/또는 디렉터리를 검색할 수 있습니다. 반환된 **IListBlobItem**에 대한 풍부한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트해야 합니다. 형식을 알 수 없는 경우 형식 검사를 사용하여 캐스트할 형식을 확인할 수 있습니다. 다음 코드에서는 `photos` 컨테이너에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

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

위에 표시된 것처럼 Blob 서비스에도 컨테이너 내의 디렉터리 개념이 있습니다. 따라서 폴더와 유사한 구조로 Blob를 구성할 수 있습니다. 예를 들어 `photos`라는 컨테이너에 있는 다음 블록 Blob 집합을 고려합니다.

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

위 샘플과 같이 'photos' 컨테이너에서 **ListBlobs**를 호출하면 반환된 컬렉션에 디렉터리를 나타내는 **CloudBlobDirectory** 및 **CloudBlockBlob** 개체와 최상위 수준의 Blob가 포함됩니다. 결과 출력은 다음과 같습니다.

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


선택적으로 **ListBlobs** 메서드의 **UseFlatBlobListing** 매개 변수를 
**true**로 설정할 수 있습니다. 그러면 디렉터리에 관계없이 모든 Blob가 **CloudBlockBlob**로 반환됩니다. **ListBlobs** 호출은 다음과 같습니다.

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

자세한 내용은 [CloudBlobContainer.ListBlobs][]를 참조하세요.

## <a name="download-blobs"> </a>방법: Blob 다운로드

Blob를 다운로드하려면 먼저 Blog 참조를 검색한 다음 **DownloadToStream** 메서드를 호출합니다. 다음 예제에서는 **DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송한 다음 이 개체를 로컬 파일에 저장할 수 있습니다.

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

##<a name="delete-blobs"> </a>방법: Blob 삭제

Blob를 삭제하려면 먼저 Blob 참조를 가져온 다음 **Delete** 메서드를 호출합니다.

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


##<a name="list-blobs-async"> </a>방법: 여러 페이지에서 비동기식으로 Blob 나열

많은 수의 Blob를 나열하거나 한 번의 나열 작업에서 반환되는 결과 수를 제어하려는 경우에는 여러 결과 페이지에 Blob를 나열할 수 있습니다. 이 예제에서는 여러 페이지에서 비동기식으로 결과를 반환하는 방법을 보여 주므로 큰 결과 집합이 반환되도록 기다리는 동안 실행이 차단되지 않습니다.

이 예제에서는 플랫 Blob 나열을 보여 주지만 **ListBlobsSegmentedAsync** 메서드의  `useFlatBlobListing` 매개 변수를  `false`로 설정하여 계층적 나열을 수행할 수도 있습니다.

샘플 메서드는 비동기 메서드를 호출하므로 앞에  `async` 키워드를 추가해야 하며 **Task** 개체를 반환해야 합니다. **ListBlobsSegmentedAsync** 메서드에 지정된 await 키워드는 나열 작업이 완료될 때까지 샘플 메서드의 실행을 일시 중단합니다.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>다음 단계

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보세요.
<ul>
<li>사용 가능한 API에 대한 자세한 내용은 Blob 서비스 참조 설명서를 참조하세요.
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET 참조</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/windowsazure/dd179355">REST API 참조</a></li>
  </ul>
</li>
<li>Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 <a href="http://msdn.microsoft.com/library/windowsazure/gg433040.aspx">Azure에 데이터 저장 및 액세스</a>를 참조하세요.</li>
<li><a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK를 사용하여 Azure 저장소에서 작동하도록 작성하는 코드를 간소화하는 방법을 알아보세요.</li>
<li>Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하세요.
  <ul>
    <li><a href="/ko-kr/documentation/articles/storage-dotnet-how-to-use-tables/">테이블 저장소</a>를 사용하여 구조화된 데이터를 저장합니다.</li>
    <li><a href="/ko-kr/documentation/articles/storage-dotnet-how-to-use-queues/">큐 저장소</a>를 사용하여 구조화되지 않은 데이터를 저장합니다.</li>
    <li><a href="/ko-kr/documentation/articles/sql-database-dotnet-how-to-use/">SQL 데이터베이스</a>를 사용하여 관계형 데이터를 저장합니다.</li>
  </ul>
</li>
</ul>

  [다음 단계]: #next-steps
  [이란 Blob 저장소]: #what-is
  [개념]: #concepts
  [Azure 저장소 계정 만들기]: #create-account
  [저장소 연결 문자열 설정]: #setup-connection-string
  [방법: 프로그래밍 방식으로 Blob 저장소에 액세스]: #configure-access
  [방법: 컨테이너 만들기]: #create-container
  [방법: 컨테이너에 Blob 업로드]: #upload-blob
  [방법: 컨테이너의 Blob 나열]: #list-blob
  [방법: Blob 다운로드]: #download-blobs
  [방법: Blob 삭제]: #delete-blobs
  [방법: 여러 페이지에서 비동기식으로 Blob 나열]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Azure에서 데이터 저장 및 액세스]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
  [연결 문자열 구성]: http://msdn.microsoft.com/library/windowsazure/ee758697.aspx
  [.NET 클라이언트 라이브러리 참조]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API 참조]: http://msdn.microsoft.com/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
<!--HONumber=42-->
