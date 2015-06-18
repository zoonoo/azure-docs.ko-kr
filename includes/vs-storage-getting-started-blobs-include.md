#####컨테이너 만들기
파일이 폴더에 저장되듯이 저장소 Blob은 컨테이너에 저장됩니다. **CloudBlobClient** 개체를 사용하여 기존 컨테이너를 참조하거나, CreateCloudBlobClient() 메서드를 호출하여 새 컨테이너를 만들 수 있습니다.

다음 코드에서는 새 Blob 저장소 컨테이너를 만드는 방법을 보여 줍니다. 이 코드는 먼저 **BlobClient** 개체를 만들므로 저장소 컨테이너 만들기와 같은 개체의 기능에 액세스할 수 있습니다. 그런 다음 코드에서는 "mycontainer"라는 저장소 컨테이너를 참조하려고 합니다. 이 이름의 컨테이너를 찾을 수 없으면 새로 만듭니다.

	// Create a blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Get a reference to a container named "mycontainer."
	CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// If "mycontainer" doesn't exist, create it.
	container.CreateIfNotExists();

기본적으로 새 컨테이너는 전용이며, 이 컨테이너에서 Blob을 다운로드하려면 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있게 하려는 경우 다음 코드를 사용하여 컨테이너를 공용으로 설정할 수 있습니다.

	container.SetPermissions(
    	new BlobContainerPermissions { PublicAccess = 
	    BlobContainerPublicAccessType.Blob }); 


**참고:** 이 코드 블록을 다음 섹션에서 코드 앞에 사용하세요.

#####컨테이너에 Blob 업로드
컨테이너에 Blob 파일을 업로드하려면 컨테이너 참조를 가져온 후 이 참조를 사용하여 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **UploadFromStream()** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 다음 예제에서는 컨테이너에 Blob을 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

	// Get a reference to a blob named "myblob".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");
	
	// Create or overwrite the "myblob" blob with the contents of a local file
	// named "myfile".
	using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
	{
    	blockBlob.UploadFromStream(fileStream);
	}

#####컨테이너의 Blob 나열
컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져옵니다. 컨테이너의 **ListBlobs()** 메서드를 호출하여 컨테이너 내의 Blob 및/또는 디렉터리를 검색할 수 있습니다. 반환된 **IListBlobItem**에 대한 풍부한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트해야 합니다. Blob 유형을 알 수 없는 경우 유형 검사를 사용하여 캐스트할 유형을 확인할 수 있습니다. 다음 코드에서는 "photos"라는 컨테이너에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

	// Get a reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop through items in the container and output the length and URI for each 
	// item.
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

Blob 컨테이너의 콘텐츠를 나열하는 다른 방법이 있습니다. 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob)(영문)을 참조하세요.

#####Blob 다운로드
Blob을 다운로드하려면 먼저 Blob에 대한 참조를 가져온 다음 the DownloadToStream() 메서드를 호출합니다. 다음 예제에서는 DownloadToStream() 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송합니다. 그러면 이 개체를 로컬 파일에 저장할 수 있습니다.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named "myfile".
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	blockBlob.DownloadToStream(fileStream);
	}

Blob을 파일로 저장하는 다른 방법이 있습니다. 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs)(영문)을 참조하세요.

#####Blob 삭제
Blob을 삭제하려면 먼저 Blob 참조를 가져온 다음 Delete() 메서드를 호출합니다.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	blockBlob.Delete();

[Azure 저장소에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/storage/)
또한 [서버 탐색기로 저장소 리소스 탐색](http://msdn.microsoft.com/library/azure/ff683677.aspx)(영문)을 참조하세요.


<!--HONumber=42-->
