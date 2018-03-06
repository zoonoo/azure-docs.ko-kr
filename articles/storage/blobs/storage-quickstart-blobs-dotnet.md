---
title: "Azure 빠른 시작 - .NET을 사용하여 Azure Storage에 BLOB 업로드, 다운로드 및 나열 | Microsoft Docs"
description: "이 빠른 시작에서는 저장소 계정과 컨테이너를 만듭니다. 그런 다음, .NET용 저장소 클라이언트 라이브러리를 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다."
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: 265691ff189c628156f234083645a4b2ca4b637b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>빠른 시작: .NET을 사용하여 BLOB 업로드, 다운로드 및 나열

이 빠른 시작에서 Azure Storage에 대해 .NET 클라이언트 라이브러리를 사용하여 컨테이너에 블록 Blob을 업로드, 다운로드 및 나열하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) 또는 [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x)용 .NET Core 2.0을 설치합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드

이 빠른 시작 가이드에서 사용되는 샘플 응용 프로그램은 기본적인 콘솔 응용 프로그램입니다. 

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Visual Studio 솔루션을 storage-blobs-dotnet-quickstart 폴더를 찾아 열고 storage-blobs-dotnet-quickstart.sln을 두 번 클릭합니다. 

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성

응용 프로그램에서 저장소 계정에 대한 연결 문자열을 제공해야 합니다. 이 연결 문자열은 응용 프로그램을 실행하는 로컬 컴퓨터의 환경 변수 내에 저장하는 것이 좋습니다. 운영 체제에 따라 아래 예제 중 하나를 따라 환경 변수를 만듭니다.  \<yourconnectionstring\>을 실제 연결 문자열로 바꿉니다.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>샘플 실행

이 샘플은 내 문서에 테스트 파일을 만들고, Blob Storage에 업로드한 후, 컨테이너의 blob을 나열하고, 해당 파일을 새 이름으로 다운로드하여 이전 파일과 새 파일을 비교할 수 있도록 합니다. 

응용 프로그램 디렉터리로 이동하여 `dotnet run` 명령으로 응용 프로그램을 실행합니다.

```
dotnet run
```

표시된 출력은 다음 예제와 유사합니다.

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

아무 키나 눌러 계속하면 저장소 컨테이너 및 파일이 삭제됩니다. 계속하기 전에 MyDocuments에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다. 콘솔 창에서 blob에 대한 URL을 복사하고 브라우저에 붙여 넣어 Blob Storage의 파일 콘텐츠를 봅니다.

[Azure Storage 탐색기](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 저장소 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 

파일을 확인한 후에 아무 키나 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 Program.cs 파일을 열고 코드를 확인합니다. 

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해하도록 샘플 코드를 따라 진행합니다.

### <a name="get-references-to-the-storage-objects"></a>저장소 개체에 대한 참조 가져오기

가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 빌드되며, 각 개체는 목록의 다음 개체에 의해 사용됩니다.

* 저장소 계정을 가리키는 [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet) 개체의 인스턴스를 만듭니다.

* 저장소 계정의 Blob service를 가리키는 [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet) 개체의 인스턴스를 만듭니다.

* 액세스하는 컨테이너를 나타내는 [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) 개체의 인스턴스를 만듭니다. 컨테이너는 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 blob을 구성하는 데 사용됩니다.

[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)가 있으면 관심 있는 특정 Blob을 가리키는 [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet) 개체의 인스턴스를 만들고, 업로드, 다운로드, 복사 등을 수행할 수 있습니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

이 섹션에서는 개체의 인스턴스를 만들고, 새 컨테이너를 만든 다음, 컨테이너에 대해 사용 권한을 설정하여 Blob을 공용 Blob으로 유지하고 URL을 통해서만 액세스할 수 있게 합니다. 컨테이너를 **quickstartblobs**로 지칭합니다.

이 예제에서는 샘플이 실행될 때마다 새 컨테이너를 만들려고 하기 때문에 [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet)를 사용합니다. 응용 프로그램 전체에서 동일한 컨테이너를 사용하는 프로덕션 환경에서는 **CreateIfNotExists**를 한 번만 호출하는 것이 더 좋은 방법입니다. 또는, 코드에서 만들 필요가 없도록 컨테이너를 미리 만드는 것이 좋습니다.

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 blob이 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다.

Blob에 파일을 업로드하려면 대상 컨테이너에 blob에 대한 참조를 가져옵니다. Blob 참조가 있으면 [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync)를 사용하여 데이터를 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 만들고, Blob이 이미 있는 경우 덮어씁니다.

샘플 코드는 업로드 및 다운로드에 사용할 로컬 파일을 만들고 해당 파일이 **localFileName**에 **fileAndPath** 및 blob의 이름으로 업로드될 수 있게 저장합니다. 다음 예제에서는 **quickstartblobs**라는 저장소에 이 파일을 업로드합니다.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

Blob 저장소에서 사용할 수 있는 몇 가지 업로드 메서드가 있습니다. 예를 들어 메모리 스트림이 있는 경우 UploadFromFileAsync 대신 UploadFromStreamAsync 메서드를 사용할 수 있습니다.

블록 Blob은 모든 유형의 텍스트 또는 이진 파일이 될 수 있습니다. 페이지 blob은 IaaS VM을 백업하는 데 사용되는 VHD 파일에 주로 사용됩니다. 추가 Blob은 파일에 쓰고 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 사용됩니다. Blob Storage에 저장된 대부분의 개체는 블록 Blob입니다.

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync)를 사용하여 컨테이너의 파일 목록을 가져올 수 있습니다. 다음 코드는 blob 목록을 검색하고, 이 과정을 반복하여 발견된 Blob의 URI를 표시합니다. 명령 창에서 URI를 복사한 후 브라우저에 붙여 넣어 파일을 봅니다.

컨테이너에 있는 blob이 5,000개 이하인 경우 모든 blob 이름이 ListBlobsSegmentedAsync의 단일 호출에서 검색됩니다. 컨테이너에 있는 blob이 5,000개보다 많을 경우 서비스는 모든 blob 이름이 검색될 때까지 5,000개씩 목록을 검색합니다. 이 API가 처음 호출되면 처음 5,000개 blob 이름과 연속 토큰이 반환됩니다. 두 번째로 호출될 때는 이 토큰을 제공해야 합니다. 그러면 서비스는 다음 blob 이름 집합을 검색합니다. 이와 같이 진행하다가 연속 토큰이 null이 되어 모든 blob 이름이 검색되었음을 나타내면 중단됩니다.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>Blob 다운로드

[CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync)를 사용하여 blob을 로컬 디스크에 다운로드합니다.

다음 코드는 이전 섹션에서 업로드된 blob을 다운로드하고, blob 이름에 "_DOWNLOADED" 접미사를 추가하여 로컬 디스크에서 두 파일을 모두 볼 수 있도록 합니다.

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 가이드에서는 업로드된 blob이 더 이상 필요하지 않으므로 [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync)를 사용하여 전체 컨테이너를 삭제해도 됩니다. 만든 파일도 더 이상 필요하지 않으면 삭제합니다.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 .NET을 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 계속해서 Blob Storage 방법을 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](storage-dotnet-how-to-use-blobs.md)

다운로드하여 실행할 수 있는 추가 Azure Storage 코드 샘플에 대해서는 [.NET을 사용하는 Azure Storage 샘플](../common/storage-samples-dotnet.md) 목록을 확인하세요.

Storage 탐색기 및 Blob에 대한 자세한 내용은 [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
