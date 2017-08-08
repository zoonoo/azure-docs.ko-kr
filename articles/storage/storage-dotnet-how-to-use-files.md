---
title: ".NET을 사용하여 Azure File Storage 개발 | Microsoft Docs"
description: "Azure File Storage를 사용하여 파일 데이터를 저장하는 .NET 응용 프로그램 및 서비스를 개발하는 방법을 알아봅니다."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e26da88ef1803d47d7286c5ae836ac9c041dadd1
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="develop-for-azure-file-storage-with-net"></a>.NET을 사용하여 Azure File Storage 개발 
> [!NOTE]
> 이 문서에서는 .NET 코드를 사용하여 Azure File Storage를 관리하는 방법을 보여 줍니다. Azure File Storage에 대한 자세한 내용은 [Azure File Storage 소개](storage-files-introduction.md)를 참조하세요.
>

[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

## <a name="about-this-tutorial"></a>이 자습서 정보
이 자습서에서는 .NET을 사용하여 Azure File Storage를 사용하여 파일 데이터를 저장하는 응용 프로그램이나 서비스를 개발하는 데 필요한 기본 사항을 보여 줍니다. 즉 간단한 콘솔 응용 프로그램을 만들고, .NET 및 Azure File Storage를 통해 기본 동작을 수행하는 방법을 보여 줍니다.

* 파일 내용 가져오기
* 파일 공유에 대한 할당량(최대 크기) 설정
* 공유에 정의된 공유 액세스 정책을 사용하는 파일에 대해 공유 액세스 서명(SAS 키) 만들기
* 동일한 저장소 계정의 다른 파일로 파일 복사
* 동일한 저장소 계정의 blob으로 파일 복사
* 문제 해결을 위해 Azure 저장소 메트릭 사용

> [!Note]  
> Azure File Storage는 SMB를 통해 액세스할 수 있기 때문에 파일 I/O에 대한 표준 System.IO 클래스를 사용하여 Azure 파일 공유에 액세스하는 간단한 응용 프로그램을 작성할 수 있습니다. 이 문서에서는 [Azure File Storage REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api)를 사용하여 Azure File Storage와 통신하는 Azure Storage .NET SDK를 사용하는 응용 프로그램을 작성하는 방법에 대해 설명합니다. 


## <a name="create-the-console-application-and-obtain-the-assembly"></a>콘솔 응용 프로그램 만들기 및 어셈블리 가져오기
Visual Studio에서 새로운 Windows 콘솔 응용 프로그램을 만듭니다. 다음 단계에서는 Visual Studio 2017에서 콘솔 응용 프로그램을 만드는 방법을 보여 줍니다. 이 단계는 다른 버전의 Visual Studio에서도 유사합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
2. **설치됨** > **템플릿** > **Visual C#** > **Windows 기본 바탕 화면**을 선택합니다.
3. **콘솔 앱(.NET Framework)**를 선택합니다.
4. **이름:** 필드에서 응용 프로그램의 이름을 입력합니다.
5. **확인**을 선택합니다.

이 자습서의 모든 코드 예제는 콘솔 응용 프로그램에 있는 `Program.cs` 파일의 `Main()` 메서드에 추가될 수 있습니다.

Azure 클라우드 서비스, 웹앱, 데스크톱 및 모바일 응용 프로그램을 포함하여 .NET 응용 프로그램의 모든 형식에서 Azure Storage 클라이언트 라이브러리를 사용할 수 있습니다. 이 가이드에서는 편의상 콘솔 응용 프로그램을 사용합니다.

## <a name="use-nuget-to-install-the-required-packages"></a>NuGet을 사용하여 필요한 패키지 설치
이 자습서를 완료하기 위해 프로젝트에서 참조해야 하는 두 개의 패키지가 있습니다.

* [.NET용 Microsoft Azure 저장소 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage/): 이 패키지는 저장소 계정에서 데이터 리소스에 프로그래밍 방식의 액세스를 제공합니다.
* [.NET용 Microsoft Azure 구성 관리자 라이브러리](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): 이 패키지는 응용 프로그램을 실행하는 위치에 관계없이 구성 파일에서 연결 문자열을 구문 분석하기 위한 클래스를 제공합니다.

NuGet을 사용하여 패키지를 모두 가져올 수 있습니다. 다음 단계를 수행하세요.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. 온라인에서 "WindowsAzure.Storage"를 검색하고 **설치** 를 클릭하여 저장소 클라이언트 라이브러리와 종속성을 설치합니다.
3. 온라인에서 "WindowsAzure.ConfigurationManager"를 검색하고 **설치**를 클릭하여 Azure 구성 관리자를 설치합니다.

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>저장소 계정 자격 증명을 app.config 파일에 저장
다음에는 프로젝트의 app.config 파일에 자격 증명을 저장합니다. 다음과 비슷하게 app.config 파일을 편집합니다. 여기서는 `myaccount`을(를) 저장소 계정 이름으로 바꾸고 `mykey`을(를) 저장소 계정 키로 바꿉니다.

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
> 최신 버전의 Azure 저장소 에뮬레이터는 Azure File Storage를 지원하지 않습니다. Azure File Storage를 사용하려면 연결 문자열이 클라우드의 Azure Storage 계정을 대상으로 해야 합니다.

## <a name="add-using-directives"></a>지시문을 사용하여 추가
솔루션 탐색기에서 `Program.cs` 파일을 열고 지시문을 사용하여 파일 맨 위에 다음을 추가합니다.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.WindowsAzure.Storage.File; // Namespace for Azure File storage
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>프로그래밍 방식으로 파일 공유 액세스
다음에는 위에 표시된 코드 뒤에 나오는 `Main()` 메서드에 다음 코드를 추가하여 연결 문자열을 검색합니다. 이 코드는 이전에 만든 파일에 대한 참조를 가져오고 해당 내용을 콘솔 창에 출력합니다.

```csharp
// Create a CloudFileClient object for credentialed access to Azure File storage.
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

콘솔 응용 프로그램을 실행하여 출력을 확인합니다.

## <a name="set-the-maximum-size-for-a-file-share"></a>파일 공유에 대한 최대 크기 설정
Azure 저장소 클라이언트 라이브러리 버전 5.x부터 파일 공유에 대한 할당량(또는 최대 크기)을 기가바이트 단위로 설정할 수 있습니다. 또한 공유에 현재 저장된 데이터의 양도 확인할 수 있습니다.

공유에 대한 할당량을 설정하여 공유에 저장되는 파일의 전체 크기를 제한할 수 있습니다. 공유에 있는 파일의 총 크기가 공유에 대해 설정된 할당량을 초과하면 클라이언트는 해당 파일이 비어 있지 않는 한, 기존 파일의 크기를 늘리거나 새 파일을 만들 수 없습니다.

아래 예제에서는 공유에 대한 현재 사용량을 확인하고 공유에 대해 할당량을 설정하는 방법을 보여 줍니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
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
Azure 저장소 클라이언트 라이브러리 버전 5.x부터 파일 공유 또는 개별 파일에 대해 SAS(공유 액세스 서명)를 생성할 수 있습니다. 또한 파일 공유에 대해 공유 액세스 정책을 만들어 공유 액세스 서명을 관리할 수도 있습니다. 공유 액세스 정책을 만들면 노출된 SAS를 해지할 수 있으므로 권장됩니다.

다음 예제에서는 공유에 대해 공유 액세스 정책을 만들고 해당 정책을 사용하여 공유의 파일에 대해 SAS에 대한 제약 조건을 제공합니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
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
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

공유 액세스 서명을 만들고 사용하는 방법에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md) 및 [Azure Blob을 사용하여 SAS 만들기 및 사용](storage-dotnet-shared-access-signature-part-2.md)을 참조하세요.

## <a name="copy-files"></a>파일 복사
Azure 저장소 클라이언트 라이브러리 버전 5.x부터 파일을 다른 파일로, 파일을 blob으로 또는 blob을 파일로 복사할 수 있습니다. 다음 섹션에는 이러한 복사 작업을 프로그래밍 방식으로 수행하는 방법이 나와 있습니다.

AzCopy를 사용하여 파일을 다른 파일로 복사하거나 blob을 파일로 복사할 수도 있고 그 반대로 복사할 수도 있습니다. [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)을 참조하세요.

> [!NOTE]
> blob을 파일에 복사하거나 파일을 blob에 복사하는 경우 두 항목이 동일한 저장소 계정 내에 있더라도 SAS(공유 액세스 서명)를 사용하여 원본 개체를 인증해야 합니다.
> 
> 

**다른 파일에 파일 복사** 다음 예제에서는 동일한 공유의 다른 파일에 파일을 복사합니다. 이 복사 작업은 동일한 저장소 계정의 파일 간에 복사를 수행하므로 공유 키 인증을 사용하여 복사를 수행할 수 있습니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure File storage.
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

**Blob에 파일 복사** 다음 예제에서는 파일을 만들고 동일한 저장소 계정 내의 Blob에 복사합니다. 이 예제에서 서비스는 복사 작업 동안 원본 파일에 대한 액세스를 인증하는 데 사용하는 소스 파일용 SAS를 만듭니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure File storage.
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
// to authenticate access to the source object, even if you are copying within the same
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

동일한 방식으로 blob을 파일에 복사할 수 있습니다. 원본 개체가 blob인 경우 복사 작업 동안 해당 blob에 대한 액세스를 인증하는 SAS를 만듭니다.

## <a name="troubleshooting-azure-file-storage-using-metrics"></a>메트릭을 사용하여 Azure File Storage 문제 해결
이제 Azure Storage 분석은 Azure File Storage에 대한 메트릭을 지원합니다. 메트릭 데이터를 사용하여 요청을 추적하고 문제를 진단할 수 있습니다.


[Azure Portal](https://portal.azure.com)에서 Azure File Storage에 대한 메트릭을 사용하도록 설정할 수 있습니다. 또한 REST API 또는 저장소 클라이언트 라이브러리의 유사한 기능 중 하나를 통해 파일 서비스 설정 속성을 호출하여 프로그래밍 방식으로 메트릭을 사용하도록 설정할 수도 있습니다.


다음 코드 예제에서는 .NET용 저장소 클라이언트 라이브러리를 사용하여 Azure File Storage에 대한 메트릭을 사용하도록 설정하는 방법을 보여 줍니다.

먼저 위에서 추가한 항목 외에도 다음 `using` 지시문을 `Program.cs` 파일에 추가합니다.

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Azure Blob, Azure 테이블 및 Azure 큐는 `Microsoft.WindowsAzure.Storage.Shared.Protocol`네임스페이스에서 `ServiceProperties` 공유 형식을 사용하지만, Azure File Storage는 `Microsoft.WindowsAzure.Storage.File.Protocol` 네임스페이스에서 `FileServiceProperties` 고유 형식을 사용합니다. 그러나 두 네임스페이스는 컴파일할 다음 코드의 경우 코드에서 참조되어야 합니다.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
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

또한 종단 간 문제 해결 지침에 대해서는 [Azure File Storage 문제 해결 문서](storage-troubleshoot-file-connection-problems.md)를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure 파일 저장소에 대한 자세한 내용은 다음 링크를 참조합니다.

### <a name="conceptual-articles-and-videos"></a>개념 문서 및 비디오
* [Azure File Storage: 원활한 Windows 및 Linux용 클라우드 SMB 파일 시스템(영문)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Linux에서 Azure File Storage 사용 방법](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>파일 저장소용 도구 지원
* [Azure 저장소와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md)
* [Microsoft Azure 저장소와 함께 AzCopy를 사용하는 방법](storage-use-azcopy.md)
* [Azure 저장소에서 Azure CLI 사용](storage-azure-cli.md#create-and-manage-file-shares)
* [Azure File Storage 문제 해결](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>참조
* [Storage Client Library for .NET 참조](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [파일 서비스 REST API 참조](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>블로그 게시물
* [Azure 파일 저장소 일반적으로 사용 가능(영문)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure File Storage 내부 구조(영문)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure 파일 서비스 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Microsoft Azure File Storage에 대한 연결 유지(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
