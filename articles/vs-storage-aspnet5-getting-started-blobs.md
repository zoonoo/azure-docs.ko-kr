<properties 
	pageTitle="Azure 저장소 시작" 
	description="Visual Studio의 ASP.NET 5 프로젝트에서 Azure Blob 저장소를 사용하는 방법" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="kempb"/>

# Azure 저장소 시작(ASP.NET 5 프로젝트)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Azure Blob 저장소는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. 단일 Blob은 임의의 크기일 수 있습니다. Blob은 이미지, 오디오 및 비디오 파일, 원시 데이터, 문서 파일 등일 수 있습니다.

시작하려면 Azure 저장소 계정을 만든 다음 저장소에 컨테이너를 하나 이상 만들어야 합니다. 예를 들어 “Scrapbook”이라는 저장소를 만든 다음 저장소에 사진을 저장할 “images”라는 컨테이너를 만들고 오디오 파일을 저장할 “audio”라는 컨테이너를 만들 수 있습니다. 컨테이너를 만든 후 컨테이너에 개별 Blob 파일을 업로드할 수 있습니다. 프로그래밍 방식으로 Blob을 조작하는 방법에 대한 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법(영문)](storage-dotnet-how-to-use-blobs.md ".NET에서 Blob 저장소를 사용하는 방법(영문)")을 참조하세요.

ASP.NET 5 프로젝트에서 Blob에 프로그래밍 방식으로 액세스하려면 다음 항목(아직 없는 경우)을 추가해야 합니다.

1. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. 다음 코드를 사용하여 구성 설정을 가져옵니다.

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####저장소 연결 문자열 가져오기
Blob를 가지고 작업을 수행하려면, Blob가 저장될 저장소 계정에 대한 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. ASP.NET 5 프로젝트를 사용하는 경우 다음 코드에 나온 대로 구성 개체의 get 메서드를 호출하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져올 수 있습니다.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####컨테이너 만들기
파일이 폴더에 저장되듯이 저장소 Blob은 컨테이너에 저장됩니다. **CloudBlobClient** 개체를 사용하여 기존 컨테이너를 참조하거나, CreateCloudBlobClient() 메서드를 호출하여 새 컨테이너를 만들 수 있습니다.

다음 코드에서는 새 Blob 저장소 컨테이너를 만드는 방법을 보여 줍니다. 이 코드는 먼저 **BlobClient** 개체를 만들므로 저장소 컨테이너 만들기와 같은 개체의 기능에 액세스할 수 있습니다. 그런 다음 코드에서는 “mycontainer”라는 저장소 컨테이너를 참조하려고 합니다. 이 이름의 컨테이너를 찾을 수 없으면 새로 만듭니다.

**참고:** ASP.NET 5에서 Azure 저장소에 대한 호출을 수행하는 API는 비동기적입니다. 자세한 내용은 [Async 및 Await를 사용한 비동기 프로그래밍](http://msdn.microsoft.com/library/hh191443.aspx)을 참조하세요. 아래 코드에서는 비동기 프로그래밍 메서드를 사용한다고 가정합니다.

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();    

기본적으로 새 컨테이너는 전용이며, 이 컨테이너에서 Blob을 다운로드하려면 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있게 하려는 경우 다음 코드를 사용하여 컨테이너를 공용으로 설정할 수 있습니다.

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**참고:** 다음 섹션의 코드 앞에서 이 코드를 모두 사용합니다.

#####컨테이너에 Blob 업로드
컨테이너에 Blob 파일을 업로드하려면 컨테이너 참조를 가져온 후 이 참조를 사용하여 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **UploadFromStreamAsync()** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 다음 예제에서는 컨테이너에 Blob을 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

#####컨테이너의 Blob 나열
컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져옵니다. 컨테이너의 **ListBlobsSegmentedAsync()** 메서드를 호출하여 컨테이너 내의 Blob 및/또는 디렉터리를 검색할 수 있습니다. 반환된 **IListBlobItem**에 대한 풍부한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트해야 합니다. Blob 유형을 알 수 없는 경우 유형 검사를 사용하여 캐스트할 유형을 확인할 수 있습니다. 다음 코드에서는 컨테이너에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
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
        } while (token != null);

Blob 컨테이너의 콘텐츠를 나열하는 다른 방법이 있습니다. 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법](storage-dotnet-how-to-use-blobs.md/#list-blob)(영문)을 참조하세요.

#####Blob 다운로드
Blob을 다운로드하려면 먼저 Blob에 대한 참조를 가져온 다음 **DownloadToStreamAsync()** 메서드를 호출합니다. 다음 예제에서는 **DownloadToStreamAsync()** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송합니다. 그러면 이 개체를 로컬 파일에 저장할 수 있습니다.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Blob을 파일로 저장하는 다른 방법이 있습니다. 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법](storage-dotnet-how-to-use-blobs.md/#download-blobs)(영문)을 참조하세요.

#####Blob 삭제
Blob을 삭제하려면 먼저 Blob에 대한 참조를 가져온 다음 **DeleteAsync()** 메서드를 호출합니다.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[Azure 저장소에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/storage/) [서버 탐색기에서 저장소 리소스 탐색](http://msdn.microsoft.com/library/azure/ff683677.aspx) 및 [ASP.NET 5](http://www.asp.net/vnext)도 참조하세요.

<!---HONumber=58-->