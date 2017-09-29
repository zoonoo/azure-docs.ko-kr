---
title: "Azure 빠른 시작 - .NET을 사용하여 Azure Blob Storage에서 개체 전송 | Microsoft Docs"
description: ".NET을 사용하여 Azure Blob Storage에서 개체를 전송하는 방법을 간단히 알아봅니다."
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/01/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 97bacc2c1285fe4a467a54f224bb9fabbd851fee
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>.NET을 사용하여 Azure Blob Storage에서 개체 전송

이 빠른 시작에서 C#.NET을 사용하여 Windows의 Azure Blob Storage에서 컨테이너에 블록 blob을 업로드, 다운로드 및 나열하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)을 설치합니다.
    - **Azure 개발**

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Azure Portal을 사용하여 저장소 계정 만들기

먼저 이 빠른 시작 가이드에서 사용할 새 범용 저장소 계정을 만듭니다. 

1. [Azure Portal](https://portal.azure.com)로 이동한 후 Azure 계정을 사용하여 로그인합니다. 
2. 허브 메뉴에서 **새로 만들기** > **저장소** > **저장소 계정 - blob, 파일, 테이블, 큐**를 선택합니다. 
3. 저장소 계정의 이름을 입력합니다. 이 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 또한 고유해야 합니다.
4. `Deployment model`을 **Resource Manager**로 설정합니다.
5. `Account kind`를 **일반적인 용도**로 설정합니다.
6. `Performance`를 **표준**으로 설정합니다. 
7. `Replication`을 **LRS(로컬 중복 저장소)**로 설정합니다.
8. `Storage service encryption`을 **사용 안 함**으로 설정합니다.
9. `Secure transfer required`를 **사용 안 함**으로 설정합니다.
10. 사용 중인 구독을 선택합니다. 
11. `resource group`에 대해 새 리소스 그룹을 만들고 고유한 이름을 지정합니다. 
12. 저장소 계정에 사용할 `Location`을 선택합니다.
13. **대시보드에 고정**을 선택하고 **만들기**를 클릭하여 저장소 계정을 만듭니다. 

저장소 계정을 만들면 대시보드에 고정됩니다. 클릭하여 엽니다. 설정에서 **액세스 키**를 클릭합니다. 키를 선택하고 나중에 사용할 수 있게 연결 문자열을 클립보드에 복사한 후 메모장에 붙여 넣습니다.

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드

이 빠른 시작 가이드에서 사용되는 샘플 응용 프로그램은 기본적인 콘솔 응용 프로그램입니다. 

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Visual Studio 솔루션을 storage-blobs-dotnet-quickstart 폴더를 찾아 열고 storage-blobs-dotnet-quickstart.sln을 두 번 클릭합니다. 

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성

응용 프로그램에서 저장소 계정에 대한 연결 문자열을 제공해야 합니다. Visual Studio의 솔루션 탐색기에서 `app.config` 파일을 엽니다. StorageConnectionString 항목을 찾습니다. **값**에 대해 연결 문자열의 전체 값을 Azure Portal에서 저장한 메모장의 값으로 바꿉니다. 완료하면 다음과 비슷한 결과가 표시됩니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=youraccountname;
    AccountKey=7NGE5jasdfdRzASDFNOMEx1u20W/thisisjustC/anexampleZK/Rt5pz2xNRrDckyv8EjB9P1WGF==" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>샘플 실행

이 샘플은 내 문서에 테스트 파일을 만들고, Blob Storage에 업로드한 후, 컨테이너의 blob을 나열하고, 해당 파일을 새 이름으로 다운로드하여 이전 파일과 새 파일을 비교할 수 있도록 합니다. 

F5 키를 눌러 샘플을 실행합니다. 콘솔 창에 다음과 비슷한 출력이 표시됩니다. 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

아무 키나 눌러 계속하면 저장소 컨테이너 및 파일이 삭제됩니다. 계속하기 전에 MyDocuments에서 두 파일을 열어 동일한지 확인합니다. 콘솔 창에서 blob에 대한 URL을 복사하고 브라우저에 붙여 넣어 Blob Storage의 파일 콘텐츠를 봅니다.

[Azure Storage 탐색기](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 저장소 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 

파일을 확인한 후에 아무 키나 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 Program.cs 파일을 열고 코드를 확인합니다. 

## <a name="get-references-to-the-storage-objects"></a>저장소 개체에 대한 참조 가져오기

가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* 저장소 계정을 가리키는 **CloudStorageAccount** 개체를 인스턴스화합니다. 

* 저장소 계정의 Blob 서비스를 가리키는 **CloudBlobClient** 개체를 인스턴스화합니다. 

* 액세스하는 컨테이너를 나타내는 **CloudBlobContainer** 개체를 인스턴스화합니다. 컨테이너는 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 blob을 구성하는 데 사용됩니다.

**CloudBlobContainer**가 있으면 관심 있는 특정 blob을 가리키는 **CloudBlockBlob** 개체를 인스턴스화하고, 업로드, 다운로드, 복사 등을 수행할 수 있습니다.

이 섹션에서는 개체를 인스턴스화하고, 새 컨테이너를 만든 다음, 컨테이너에 대해 사용 권한을 설정하여 blob을 공용 blob으로 유지하고 URL을 통해서만 액세스할 수 있게 합니다. 컨테이너를 **quickstartblobs**로 지칭합니다. 

이 예제에서는 샘플이 실행될 때마다 새 컨테이너를 만들려고 하기 때문에 CreateIfNotExists를 사용합니다. 응용 프로그램 전체에서 동일한 컨테이너를 사용하는 프로덕션 환경에서는 CreateIfNotExists를 한 번만 호출하거나, 코드에서 만들 필요가 없도록 컨테이너를 미리 만드는 것이 더 좋은 방법입니다.

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

## <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 blob이 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다. 

Blob에 파일을 업로드하려면 대상 컨테이너에 blob에 대한 참조를 가져옵니다. Blob 참조가 있으면 [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync)를 사용하여 데이터를 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 만들고, Blob이 이미 있는 경우 덮어씁니다.

샘플 코드는 업로드 및 다운로드에 사용할 로컬 파일을 만들고 해당 파일이 **localFileName**에 **fileAndPath** 및 blob의 이름으로 업로드될 수 있게 저장합니다. 다음 예제에서는 **quickstartblobs**라는 저장소에 이 파일을 업로드합니다.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Blob 저장소에서 사용할 수 있는 몇 가지 업로드 메서드가 있습니다. 예를 들어 메모리 스트림이 있는 경우 UploadFromFileAsync 대신 UploadFromStreamAsync 메서드를 사용할 수 있습니다. 

블록 blob 크기는 4.7TB까지 가능하며, Excel 스프레드시트에서 큰 비디오 파일까지 다양할 수 있습니다. 페이지 blob은 IaaS VM을 백업하는 데 사용되는 VHD 파일에 주로 사용됩니다. 추가 Blob은 파일에 쓰고 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 사용됩니다. Blob Storage에 저장된 대부분의 개체는 블록 Blob입니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync)를 사용하여 컨테이너의 파일 목록을 가져옵니다. 다음 코드는 blob 목록을 검색하고, 이 과정을 반복하여 발견된 Blob의 URI를 표시합니다. 명령 창에서 URI를 복사한 후 브라우저에 붙여 넣어 파일을 봅니다.

컨테이너에 있는 blob이 5,000개 이하인 경우 모든 blob 이름이 ListBlobsSegmentedAsync의 단일 호출에서 검색됩니다. 컨테이너에 있는 blob이 5,000개보다 많을 경우 서비스는 모든 blob 이름이 검색될 때까지 5,000개씩 목록을 검색합니다. 이 API가 처음 호출되면 처음 5,000개 blob 이름과 연속 토큰이 반환됩니다. 두 번째로 호출될 때는 이 토큰을 제공해야 합니다. 그러면 서비스는 다음 blob 이름 집합을 검색합니다. 이와 같이 진행하다가 연속 토큰이 null이 되어 모든 blob 이름이 검색되었음을 나타내면 중단됩니다. 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

## <a name="download-blobs"></a>Blob 다운로드

[CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync)를 사용하여 blob을 로컬 디스크에 다운로드합니다.

다음 코드는 이전 섹션에서 업로드된 blob을 다운로드하고, blob 이름에 "_DOWNLOADED" 접미사를 추가하여 로컬 디스크에서 두 파일을 모두 볼 수 있도록 합니다. 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 가이드에서는 업로드된 blob이 더 이상 필요하지 않으므로 [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync)를 사용하여 전체 컨테이너를 삭제해도 됩니다. 만든 파일도 더 이상 필요하지 않으면 삭제합니다.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 .NET을 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 계속해서 Blob Storage 방법을 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](storage-dotnet-how-to-use-blobs.md)

Storage 탐색기 및 Blob에 대한 자세한 내용은 [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

