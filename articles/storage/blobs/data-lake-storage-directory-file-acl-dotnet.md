---
title: ACL을 & 파일용 Azure 데이터 레이크 스토리지 Gen2 .NET SDK
description: Azure Storage 클라이언트 라이브러리를 사용하여 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 디렉터리 및 파일 및 디렉터리 액세스 제어 목록(ACL)을 관리합니다.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061576"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>.NET을 사용하여 Azure 데이터 레이크 저장소 Gen2에서 디렉터리, 파일 및 ACL관리

이 문서에서는 HNS(계층적 네임스페이스)가 활성화된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리하는 데 .NET을 사용하는 방법을 보여 주며 있습니다. 

[패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API 참조](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1에서 Gen2 매핑으로](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임스페이스(HNS)가 활성화된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 만들 수 있습니다.

## <a name="set-up-your-project"></a>프로젝트 설정

시작하려면 [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 패키지를 설치합니다.

NuGet 패키지를 설치하는 방법에 대한 자세한 내용은 [NuGet 패키지 관리자를 사용하여 Visual Studio에서 패키지 설치 및 관리를](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)참조하십시오.

그런 다음 코드 파일의 맨 위에 문을 사용하여 이러한 문을 추가합니다.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 스니펫을 사용하려면 저장소 계정을 나타내는 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용하여 연결

이렇게 하면 계정에 연결하는 가장 쉬운 방법이 있습니다. 

이 예제는 계정 키를 사용하여 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 인스턴스를 만듭니다.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active 디렉터리(AD)를 사용하여 연결

[.NET에 대한 Azure ID 클라이언트 라이브러리를](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 사용하여 Azure AD를 사용하여 응용 프로그램을 인증할 수 있습니다.

이 예제에서는 클라이언트 ID, 클라이언트 암호 및 테넌트 ID를 사용하여 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 인스턴스를 만듭니다.  이러한 값을 가져오려면 [클라이언트 응용 프로그램에서 요청을 승인하기 위해 Azure AD에서 토큰 가져오기를](../common/storage-auth-aad-app.md)참조하십시오.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> 자세한 예는 .NET 설명서에 [대한 Azure ID 클라이언트 라이브러리를](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 참조하십시오.

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. [DataLakeServiceClient.CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) 메서드를 호출하여 하나를 만들 수 있습니다.

이 예제는 .라는 `my-file-system`파일 시스템을 만듭니다. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>디렉터리 만들기

[DataLakeFileSystemClient.CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) 메서드를 호출 하여 디렉터리 참조를 만듭니다.

이 예제에서는 파일 `my-directory` 시스템에 명명된 디렉터리를 추가한 다음 `my-subdirectory`. 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

[DataLakeDirectoryClient.RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) 메서드를 호출하여 디렉터리 이름을 바꾸거나 이동합니다. 원하는 디렉터리 매개 변수의 경로를 전달합니다. 

이 예제는 하위 디렉터리 이름을 `my-subdirectory-renamed`이름으로 바꿉니다.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

이 예제는 `my-subdirectory-renamed` `my-directory-2`명명된 디렉터리를 .라는 디렉터리라는 디렉터리로 이동합니다. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>디렉터리 삭제

[DataLakeDirectoryClient.Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) 메서드를 호출하여 디렉터리를 삭제합니다.

이 예제는 .라는 `my-directory`디렉터리를 삭제합니다.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>디렉터리 ACL 관리

[DataLakeDirectoryClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 메서드를 호출 하 여 디렉터리 (ACL)를 가져옵니다 및 [DataLakeDirectoryClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 메서드를 호출 하 여 ACL을 설정 합니다.

> [!NOTE]
> 응용 프로그램이 Azure Active Directory(Azure AD)를 사용하여 액세스 권한을 부여한 경우 응용 프로그램에서 액세스를 승인하는 데 사용하는 보안 주체가 [저장소 Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오. 

이 예제는 . `my-directory` 문자열은 `user::rwx,group::r-x,other::rw-` 소유 사용자에게 읽기, 쓰기 및 실행 권한을 제공하고, 소유 그룹에 읽기 및 실행 권한만 제공하고, 다른 모든 읽기 및 쓰기 권한을 부여합니다.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>디렉토리에 파일 업로드

먼저 [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) 클래스의 인스턴스를 만들어 대상 디렉터리에서 파일 참조를 만듭니다. [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 메서드를 호출하여 파일을 업로드합니다. [DataLakeFileClient.FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) 메서드를 호출하여 업로드를 완료해야 합니다.

이 예제는 `my-directory`.라는 디렉터리에 텍스트 파일을 업로드합니다.    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> 파일 크기가 큰 경우 코드에서 [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)을 여러 번 호출해야 합니다. 대신 [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 메서드를 사용하는 것이 좋습니다. 이렇게 하면 전체 파일을 단일 호출로 업로드할 수 있습니다. 
>
> 예제는 다음 섹션을 참조하세요.

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 대용량 파일 업로드

[DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 메서드를 사용하여 [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 메서드를 여러 번 호출하지 않고도 대용량 파일을 업로드할 수 있습니다.

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>파일 ACL 관리

[DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) 메서드를 호출 하 여 파일의 액세스 제어 목록 (ACL)를 가져옵니다 및 [DataLakeFileClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) 메서드를 호출 하 여 ACL을 설정 합니다.

> [!NOTE]
> 응용 프로그램이 Azure Active Directory(Azure AD)를 사용하여 액세스 권한을 부여한 경우 응용 프로그램에서 액세스를 승인하는 데 사용하는 보안 주체가 [저장소 Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오. 

이 예제는 . `my-file.txt` 문자열은 `user::rwx,group::r-x,other::rw-` 소유 사용자에게 읽기, 쓰기 및 실행 권한을 제공하고, 소유 그룹에 읽기 및 실행 권한만 제공하고, 다른 모든 읽기 및 쓰기 권한을 부여합니다.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>디렉토리에서 다운로드 

먼저 다운로드할 파일을 나타내는 [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) 인스턴스를 만듭니다. [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) 메서드를 사용 하 여 반환 값을 구문 분석 하 여 [스트림](https://docs.microsoft.com/dotnet/api/system.io.stream) 개체를 가져옵니다. 모든 .NET 파일 처리 API를 사용하여 스트림에서 파일로 바이트를 저장합니다. 

이 예제에서는 [바이너리리더와](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) [FileStream을](https://docs.microsoft.com/dotnet/api/system.io.filestream) 사용하여 바이트를 파일에 저장합니다. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

[FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) 메서드를 호출 한 다음 결과를 통해 나열 하여 디렉터리 내용을 나열 합니다.

이 예제에서는 라는 `my-directory`디렉터리에 있는 각 파일의 이름을 인쇄합니다.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>참조

* [API 참조 설명서](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen 1 ~ Gen2 매핑](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [피드백 제공](https://github.com/Azure/azure-sdk-for-net/issues)

