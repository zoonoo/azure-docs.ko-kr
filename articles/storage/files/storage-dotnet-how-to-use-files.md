---
title: .NET을 사용하여 Azure Files 개발 | Microsoft Docs
description: Azure Files를 사용하여 파일 데이터를 저장하는 .NET 애플리케이션 및 서비스를 개발하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4d8be13a75e276d5be6ec71141a13f95601869f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301440"
---
# <a name="develop-for-azure-files-with-net"></a>.NET을 사용하여 Azure Files 개발

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

이 자습서에서는 [Azure Files](storage-files-introduction.md)를 사용하여 파일 데이터를 저장하는 애플리케이션을 .NET을 사용하여 개발하는 기본 사항을 보여줍니다. 이 자습서는 .NET 및 Azure 파일을 사용하여 기본 작업을 수행하는 간단한 콘솔 응용 프로그램을 만듭니다.

* 파일의 내용을 가져옵니다.
* 파일 공유의 최대 크기 또는 *할당량을* 설정합니다.
* 공유에 정의된 저장된 액세스 정책을 사용하는 파일에 대한 공유 액세스 서명(SAS 키)을 만듭니다.
* 동일한 스토리지 계정의 다른 파일로 파일 복사
* 동일한 스토리지 계정의 blob으로 파일 복사
* 문제 해결을 위해 Azure 저장소 메트릭을 사용합니다.

Azure 파일에 대해 자세히 알아보려면 [Azure 파일이란 무엇입니까?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>.NET API 이해

Azure Files는 클라이언트 애플리케이션에 SMB(서버 메시지 블록) 및 REST라는 광범위한 두 가지 방법을 제공합니다. .NET 내에서 `System.IO` API `WindowsAzure.Storage` 및 API는 이러한 접근 방식을 추상화합니다.

API | 사용 시기 | 메모
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | 사용자 애플리케이션의 경우: <ul><li>SMB를 사용하여 파일을 읽고 작성해야 합니다.</li><li>포트 445를 통해 Azure Files 계정에 대한 액세스 권한이 있는 디바이스에서 실행됩니다.</li><li>파일 공유의 관리 설정을 관리할 필요가 없습니다.</li></ul> | SMB를 통해 Azure 파일로 구현된 파일 I/O는 일반적으로 네트워크 파일 공유 또는 로컬 저장 장치의 I/O와 동일합니다. 파일 I/O를 비롯한 .NET의 여러 기능에 대한 자세한 내용은 [콘솔 응용 프로그램](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) 자습서를 참조하십시오.
[마이크로소프트.Azure.스토리지.파일](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | 사용자 애플리케이션의 경우: <ul><li>방화벽 또는 ISP 제약 조건으로 인해 포트 445에서 SMB를 사용하여 Azure 파일에 액세스할 수 없습니다.</li><li>파일 공유 할당량을 설정하거나 공유 액세스 서명을 만들 수 있는 기능 등 관리 기능이 필요합니다.</li></ul> | 이 문서에서는 SMB `Microsoft.Azure.Storage.File` 대신 REST를 사용하고 파일 공유를 관리하여 파일 I/O에 사용하는 것을 보여 줍니다.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>콘솔 애플리케이션 만들기 및 어셈블리 가져오기

Visual Studio에서 새로운 Windows 콘솔 애플리케이션을 만듭니다. 다음 단계에서는 Visual Studio 2019에서 콘솔 애플리케이션을 만드는 방법을 보여 줍니다. 이러한 단계는 다른 버전의 Visual Studio에서도 비슷합니다.

1. Visual Studio를 시작하고, **새 프로젝트 만들기**를 선택합니다.
1. **새 프로젝트 만들기에서**C#에 대한 **콘솔 앱(.NET 프레임워크)을** 선택한 다음 **다음을**선택합니다.
1. 새 프로젝트 구성에서 앱의 이름을 입력하고 **에서 만들기를** **선택합니다.**

이 자습서의 모든 코드 예제를 콘솔 `Main()` 응용 프로그램 `Program.cs` 파일의 메서드에 추가할 수 있습니다.

모든 유형의 .NET 응용 프로그램에서 Azure Storage 클라이언트 라이브러리를 사용할 수 있습니다. 이러한 유형에는 Azure 클라우드 서비스 또는 웹 앱, 데스크톱 및 모바일 응용 프로그램이 포함됩니다. 이 가이드에서는 편의상 콘솔 애플리케이션을 사용합니다.

## <a name="use-nuget-to-install-the-required-packages"></a>NuGet을 사용하여 필요한 패키지 설치

이 자습서를 완료하려면 프로젝트에서 다음 패키지를 참조하십시오.

* [.NET에 대 한 마이크로소프트 Azure 스토리지 공용 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  이 패키지는 저장소 계정의 공통 리소스에 대한 프로그래밍 방식으로 액세스를 제공합니다.
* [.NET에 대 한 마이크로소프트 Azure 스토리지 Blob 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  이 패키지는 저장소 계정의 Blob 리소스에 대한 프로그래밍 방식으로 액세스를 제공합니다.
* [.NET에 대 한 마이크로소프트 Azure 스토리지 파일 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  이 패키지는 저장소 계정의 파일 리소스에 대한 프로그래밍 방식으로 액세스를 제공합니다.
* [.NET에 대 한 마이크로소프트 Azure 구성 관리자 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  이 패키지는 응용 프로그램이 실행되는 모든 위치에 구성 파일에서 연결 문자열을 구문 분석하는 클래스를 제공합니다.

NuGet을 사용하여 패키지를 모두 가져올 수 있습니다. 다음 단계를 수행하세요.

1. **솔루션 탐색기에서**프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리를 선택합니다.**
1. **NuGet 패키지 관리자에서** **찾아보기를 선택합니다.** 그런 다음 **Microsoft.Azure.Storage.Blob을**검색하고 선택한 다음 **설치를**선택합니다.

   이 단계에서는 패키지와 해당 종속성을 설치합니다.
1. 다음 패키지를 검색하고 설치합니다.

   * **마이크로소프트.Azure.스토리지.공통**
   * **마이크로소프트.Azure.스토리지.파일**
   * **마이크로소프트.Azure.구성 관리자**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>저장소 계정 자격 증명을 App.config 파일에 저장합니다.

다음으로 프로젝트 파일에 자격 증명을 `App.config` 저장합니다. **솔루션 탐색기에서**다음 `App.config` 예제와 유사하도록 파일을 두 번 클릭하고 편집합니다. 저장소 `myaccount` 계정 이름과 저장소 `mykey` 계정 키로 바꿉니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> 최신 버전의 Azure Storage 에뮬레이터는 Azure Files를 지원하지 않습니다. Azure Files를 사용하려면 연결 문자열이 클라우드에 있는 Azure Storage 계정을 대상으로 해야 합니다.

## <a name="add-using-directives"></a>지시문을 사용하여 추가

**솔루션 탐색기에서** `Program.cs` 파일을 열고 다음 지시문을 사용하여 파일 의 맨 위에 추가합니다.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>프로그래밍 방식으로 파일 공유 액세스

다음으로 위에 표시된 코드 `Main()` 다음에 메서드에 다음 내용을 추가하여 연결 문자열을 검색합니다. 이 코드는 이전에 만든 파일에 대한 참조를 얻고 해당 내용을 출력합니다.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

콘솔 애플리케이션을 실행하여 출력을 확인합니다.

## <a name="set-the-maximum-size-for-a-file-share"></a>파일 공유에 대한 최대 크기 설정

Azure 저장소 클라이언트 라이브러리의 버전 5.x부터 파일 공유에 대한 할당량(최대 크기)을 설정할 수 있습니다. 또한 공유에 현재 저장된 데이터의 양도 확인할 수 있습니다.

공유에 대한 할당량을 설정하면 공유에 저장된 파일의 총 크기가 제한됩니다. 공유에 있는 파일의 총 크기가 공유에 설정된 할당량을 초과하는 경우 클라이언트는 기존 파일의 크기를 늘릴 수 없습니다. 클라이언트는 해당 파일이 비어 있지 않으면 새 파일을 만들 수 없습니다.

아래 예제에서는 공유에 대한 현재 사용량을 확인하고 공유에 대해 할당량을 설정하는 방법을 보여 줍니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>파일 또는 파일 공유에 대한 공유 액세스 서명 생성

Azure Storage 클라이언트 라이브러리 버전 5.x부터 파일 공유 또는 개별 파일에 대해 SAS(공유 액세스 서명)를 생성할 수 있습니다. 파일 공유에 저장된 액세스 정책을 만들어 공유 액세스 서명을 관리할 수도 있습니다. SAS가 손상되면 취소할 수 있으므로 저장된 액세스 정책을 만드는 것이 좋습니다.

다음 예제는 공유에 저장된 액세스 정책을 만듭니다. 이 예제에서는 해당 정책을 사용하여 공유의 파일에 대한 SAS에 대한 제약 조건을 제공합니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

공유 액세스 서명을 만들고 사용하는 방법에 대한 자세한 내용은 [공유 액세스 서명의 작동 방식을](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)참조하십시오.

## <a name="copy-files"></a>파일 복사

Azure Storage 클라이언트 라이브러리 버전 5.x부터 파일을 다른 파일로, 파일을 blob으로 또는 blob을 파일로 복사할 수 있습니다. 다음 섹션에서는 프로그래밍 방식으로 이러한 복사 작업을 수행하는 방법을 보여 줍니다.

AzCopy를 사용하여 한 파일을 다른 파일로 복사하거나 Blob을 파일 또는 다른 방법으로 복사할 수도 있습니다. [AzCopy 시작](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

> [!NOTE]
> BLOB을 파일에 복사하거나 파일을 BLOB에 복사하는 경우 두 항목이 동일한 스토리지 계정 내에 있더라도 SAS(공유 액세스 서명)를 사용하여 원본 개체에 대한 액세스를 인증해야 합니다.
>

### <a name="copy-a-file-to-another-file"></a>파일을 다른 파일에 복사

다음 예제에서는 파일을 동일한 공유의 다른 파일에 복사합니다. 이 복사 작업은 동일한 저장소 계정의 파일 간에 복사되므로 공유 키 인증을 사용하여 복사본을 수행할 수 있습니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>파일을 Blob에 복사

다음 예제에서는 파일을 만들고 동일한 스토리지 계정 내의 blob에 복사합니다. 이 예제에서 서비스는 복사 작업 동안 원본 파일에 대한 액세스를 인증하는 데 사용하는 소스 파일용 SAS를 만듭니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

동일한 방식으로 blob을 파일에 복사할 수 있습니다. 원본 개체가 BLOB인 경우 복사 작업 동안 해당 BLOB에 대한 액세스를 인증하는 SAS를 만듭니다.

## <a name="share-snapshots"></a>공유 스냅샷

Azure Storage 클라이언트 라이브러리의 버전 8.5부터는 공유 스냅샷을 만들 수 있습니다. 또한 공유 스냅샷을 나열하거나 찾고 삭제할 수도 있습니다. 공유 스냅샷은 읽기 전용이므로 공유 스냅샷에 쓰기 작업이 허용되지 않습니다.

### <a name="create-share-snapshots"></a>공유 스냅샷 만들기

다음 예제에서는 파일 공유 스냅샷을 만듭니다.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>공유 스냅샷 나열

다음 예제에서는 공유에 공유 스냅샷을 나열합니다.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>공유 스냅샷 내에서 파일 및 디렉터리 찾아보기

다음 예제에서는 공유 스냅샷 내에서 파일 및 디렉터리를 찾습니다.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>공유 및 공유 스냅샷 나열 및 공유 스냅샷의 파일 공유 또는 파일 복원

파일 공유의 스냅샷을 만들면 나중에 개별 파일 또는 전체 파일 공유를 복구할 수 있습니다.

파일 공유의 공유 스냅샷을 쿼리하여 파일 공유 스냅샷의 파일을 복원할 수 있습니다. 그런 다음 특정 공유 스냅숏에 속한 파일을 검색할 수 있습니다. 직접 읽고 비교하거나 복원하려면 해당 버전을 사용합니다.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>공유 스냅샷 삭제

다음 예제에서는 파일 공유 스냅샷을 삭제합니다.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>메트릭을 사용하여 Azure 파일 문제 해결<a name="troubleshooting-azure-files-using-metrics"></a>

이제 Azure Storage 분석은 Azure Files에 대한 메트릭을 지원합니다. 메트릭 데이터를 사용하여 요청을 추적하고 문제를 진단할 수 있습니다.

[Azure 포털](https://portal.azure.com)에서 Azure 파일에 대한 메트릭을 활성화할 수 있습니다. 또한 REST API 또는 저장소 클라이언트 라이브러리의 유사체 중 하나를 사용하여 파일 서비스 속성 설정 작업을 호출하여 프로그래밍 방식으로 메트릭을 활성화할 수도 있습니다.

다음 코드 예제에서는 .NET용 스토리지 클라이언트 라이브러리를 사용하여 Azure Files에 대한 메트릭을 사용하도록 설정하는 방법을 보여 줍니다.

먼저 위에 추가한 지침과 함께 파일에 다음 `using` 지시문을 추가합니다. `Program.cs`

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Azure Blob, Azure 테이블 및 Azure 큐는 `ServiceProperties` `Microsoft.Azure.Storage.Shared.Protocol` 네임스페이스에서 공유 형식을 사용하지만 Azure `FileServiceProperties` Files는 `Microsoft.Azure.Storage.File.Protocol` 네임스페이스의 형식인 자체 형식을 사용합니다. 그러나 다음 코드를 컴파일하려면 코드에서 두 네임스페이스를 모두 참조해야 합니다.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

문제가 발생하면 [Windows에서 Azure 파일 문제 해결을](storage-troubleshoot-windows-file-connection-problems.md)참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 파일에 대한 자세한 내용은 다음 리소스를 참조하십시오.

### <a name="conceptual-articles-and-videos"></a>개념 문서 및 비디오

* [Azure Files: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)(Azure Files: Windows 및 Linux를 위한 원활한 클라우드 SMB 파일 시스템)
* [Linux에서 Azure Files 사용](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage용 도구 지원

* [AzCopy 시작](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Windows에서 Azure Files 문제 해결](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>참고

* [.NET용 Azure Storage API](/dotnet/api/overview/azure/storage)
* [파일 서비스 REST API](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>블로그 게시물

* [Azure 파일 저장소, 이제 일반적으로 사용할 수 있습니다.](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure 파일 저장소 내부](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [마이크로소프트 Azure 파일 서비스 소개](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Microsoft Azure 파일에 대한 연결 유지](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
