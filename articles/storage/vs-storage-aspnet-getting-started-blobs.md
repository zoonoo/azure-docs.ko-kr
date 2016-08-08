<properties
	pageTitle="Blob 저장소 및 Visual Studio 연결된 서비스 시작(ASP.NET) | Microsoft Azure"
	description="Visual Studio 연결된 서비스를 사용하여 저장소 계정에 연결한 후 Visual Studio ASP.NET 프로젝트에서 Azure Blob 저장소 사용을 시작하는 방법입니다."
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="tarcher"/>

# Blob 저장소 및 Visual Studio 연결된 서비스 시작(ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## 개요

이 문서에서는 Visual Studio **연결된 서비스 추가** 대화 상자를 사용하여 ASP.NET 앱에서 Azure 저장소 계정을 만들거나 참조한 후 Azure Blob 저장소를 사용하는 방법을 설명합니다. 이 문서에서는 Blob 컨테이너를 만들고 Blob 업로드, 나열, 다운로드 및 삭제 등과 같은 기타 일반적인 작업을 수행하는 방법을 보여줍니다. 샘플은 C#으로 작성되었으며 [.NET용 Microsoft Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 사용합니다.

 - Azure Blob 저장소 사용에 대한 자세한 내용은 [.NET을 사용하여 Azure Blob 저장소 시작](storage-dotnet-how-to-use-blobs.md)을 참조하세요.
 - ASP.NET 프로젝트에 대한 자세한 내용은 [ASP.NET](http://www.asp.net)을 참조하세요.


Azure Blob 저장소는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. 단일 Blob은 임의의 크기일 수 있습니다. Blob은 이미지, 오디오 및 비디오 파일, 원시 데이터, 문서 파일 등일 수 있습니다.

파일이 폴더에 저장되듯이 저장소 Blob은 컨테이너에 저장됩니다. 저장소 계정을 만든 후 해당 저장소에 하나 이상의 컨테이너를 만듭니다. 예를 들어 “Scrapbook”이라는 저장소를 만든 다음 저장소에 사진을 저장할 “images”라는 Blob 컨테이너를 만들고 오디오 파일을 저장할 “audio”라는 컨테이너를 만들 수 있습니다. 컨테이너를 만든 후 컨테이너에 개별 Blob 파일을 업로드할 수 있습니다.




## 코드에서 Blob 컨테이너에 액세스하기

ASP.NET 프로젝트에서 Blob에 프로그래밍 방식으로 액세스하려면 다음 항목(아직 없는 경우)을 추가해야 합니다.

1. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

		using Microsoft.Azure;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;


2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    > [AZURE.NOTE] 다음 섹션의 코드 앞에서 이전 코드를 모두 사용합니다.

3. 저장소 계정의 기존 컨테이너를 참조하는 **CloudBlobClient** 개체를 가져옵니다.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] ASP.NET 5에서 Azure 저장소에 대한 호출을 수행하는 일부 API는 비동기식입니다. 자세한 내용은 [Async 및 Await를 사용한 비동기 프로그래밍](http://msdn.microsoft.com/library/hh191443.aspx)을 참조하세요.


## 코드에서 Blob 컨테이너 만들기

**CloudBlobClient** 개체를 사용하여 저장소 계정에 컨테이너를 만들 수도 있습니다. 다음 예제와 같이 위의 코드에 **CreateIfNotExistsAsync** 호출을 추가하면 됩니다.

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();

## 컨테이너에 Blob 업로드

Azure Blob 저장소는 블록 Blob 및 페이지 Blob을 지원합니다. 대부분의 경우 블록 Blob을 사용하는 것이 좋습니다.

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 이 참조를 사용하여 블록 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **UploadFromStream** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 다음 예제에서는 컨테이너에 Blob을 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## 컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 **ListBlobs** 메서드를 사용하여 컨테이너 내의 Blob 및/또는 디렉터리를 검색합니다. 반환된 **IListBlobItem**에 대한 풍부한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트해야 합니다. 형식을 알 수 없는 경우 형식 검사를 사용하여 캐스트할 형식을 확인할 수 있습니다. 다음 코드에서는 **photos** 컨테이너에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

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

이전 예제에 나온 것처럼 Blob 서비스에도 컨테이너 내의 디렉터리 개념이 있습니다. 따라서 폴더와 유사한 구조로 Blob을 구성할 수 있습니다. 예를 들어 **photos**라는 컨테이너에 있는 다음 블록 Blob 집합을 고려합니다.

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

이전 예제와 같이 'photos' 컨테이너에서 **ListBlobs**를 호출하면 반환된 컬렉션에 디렉터리를 나타내는 **CloudBlobDirectory** 및 **CloudBlockBlob** 개체와 최상위 수준의 Blob이 포함됩니다. 다음 예제에서는 결과를 보여줍니다.

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


선택적으로, **ListBlobs** 메서드의 **UseFlatBlobListing** 매개 변수를 **true**로 설정할 수 있습니다. 이 경우 디렉터리에 관계없이 모든 Blob이 **CloudBlockBlob**으로 반환됩니다. 다음 예제에서는 **ListBlobs** 호출을 보여 줍니다.

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

또한 다음 예제에서는 결과를 보여줍니다.

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg



## Blob 다운로드

Blob을 다운로드하려면 **DownloadToStream** 메서드를 사용합니다. 다음 예제에서는 **DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송한 다음 이 개체를 로컬 파일에 저장할 수 있습니다.

    // Retrieve a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

**DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 텍스트 문자열로 다운로드할 수도 있습니다.

	// Retrieve a reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Blob 삭제

Blob을 삭제하려면 **Delete** 메서드를 사용합니다.

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## 여러 페이지에서 비동기식으로 Blob 나열

많은 수의 Blob을 나열하거나 한 번의 나열 작업에서 반환되는 결과 수를 제어하려는 경우에는 여러 결과 페이지에 Blob을 나열할 수 있습니다. 다음 예제에서는 큰 결과 집합이 반환되기를 기다리는 동안 실행이 중단되지 않도록 여러 페이지에서 비동기식으로 결과를 반환하는 방법을 보여줍니다.

이 예제에서는 플랫 Blob 나열을 보여 주지만 **ListBlobsSegmentedAsync** 메서드의 **useFlatBlobListing** 매개 변수를 **false**로 설정하여 계층적 나열을 수행할 수도 있습니다.

샘플 메서드는 비동기 메서드를 호출하므로 앞에 **async** 키워드를 추가해야 하며 **Task** 개체를 반환해야 합니다. **ListBlobsSegmentedAsync** 메서드에 대해 지정된 await 키워드는 나열 작업이 완료될 때까지 샘플 메서드의 실행을 일시 중단합니다.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## 다음 단계

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=AcomDC_0727_2016-->