---
title: .NET을 사용 하 여 Azure Data Lake Storage Gen2에서 데이터 관리
description: .NET 용 Azure Storage 클라이언트 라이브러리를 사용 하 여 계층적 네임 스페이스를 사용 하는 저장소 계정의 디렉터리와 파일을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650323"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>.NET을 사용 하 여 Azure Data Lake Storage Gen2에서 디렉터리 및 파일 관리

이 문서에서는 .NET을 사용 하 여 계층적 네임 스페이스를 포함 하는 저장소 계정의 디렉터리와 파일을 만들고 관리 하는 방법을 보여 줍니다.

디렉터리 및 파일의 ACL (액세스 제어 목록)을 가져오고, 설정 하 고, 업데이트 하는 방법에 대 한 자세한 내용은 [.net을 사용 하 여 Azure Data Lake Storage Gen2에서 acl 관리](data-lake-storage-acl-dotnet.md)를 참조 하세요.

[패키지 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API 참조](/dotnet/api/azure.storage.files.datalake)  |  [Gen1 To Gen2 mapping](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- 계층적 네임 스페이스를 사용 하는 저장소 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

시작 하려면 [DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 패키지를 설치 합니다.

NuGet 패키지를 설치 하는 방법에 대 한 자세한 내용은 [Nuget 패키지 관리자를 사용 하 여 Visual Studio에서 패키지 설치 및 관리](/nuget/consume-packages/install-use-packages-visual-studio)를 참조 하세요.

그런 다음 이러한 using 문을 코드 파일의 맨 위에 추가 합니다.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만듭니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory를 사용 하 여 연결 (Azure AD)

[.Net 용 azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 획득](../common/storage-auth-aad-app.md)을 참조 하세요.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 더 많은 예제는 [.net 용 Azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 설명서를 참조 하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일에 대 한 파일 시스템 역할을 합니다. [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) 메서드를 호출 하 여 만들 수 있습니다.

이 예제에서는 라는 컨테이너를 만듭니다 `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>디렉터리 만들기

[DataLakeFileSystemClient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) 메서드를 호출 하 여 디렉터리 참조를 만듭니다.

이 예제에서는 라는 디렉터리를 `my-directory` 컨테이너에 추가한 다음 라는 하위 디렉터리를 추가 `my-subdirectory` 합니다. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

[DataLakeDirectoryClient RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) 메서드를 호출 하 여 디렉터리 이름을 바꾸거나 이동 합니다. 원하는 디렉터리의 경로를 매개 변수로 전달 합니다. 

이 예에서는 하위 디렉터리의 이름을로 바꿉니다 `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

이 예에서는 라는 디렉터리를 `my-subdirectory-renamed` 라는 디렉터리의 하위 디렉터리로 이동 `my-directory-2` 합니다. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>디렉터리 삭제

[DataLakeDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) 메서드를 호출 하 여 디렉터리를 삭제 합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. [DataLakeFileClient async](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 메서드를 호출 하 여 파일을 업로드 합니다. [DataLakeFileClient FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) 메서드를 호출 하 여 업로드를 완료 해야 합니다.

이 예제에서는 라는 디렉터리에 텍스트 파일을 업로드 `my-directory` 합니다. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> 파일 크기가 큰 경우 코드는 [DataLakeFileClient async](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)를 여러 번 호출 해야 합니다. 대신 [DataLakeFileClient UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 메서드를 사용 하는 것이 좋습니다. 이렇게 하면 단일 호출에서 전체 파일을 업로드할 수 있습니다.
>
> 예제는 다음 섹션을 참조하세요.

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 용량이 많은 파일 업로드

[DataLakeFileClient async](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 메서드를 여러 번 호출 하지 않고도 [DataLakeFileClient UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 메서드를 사용 하 여 많은 파일을 업로드할 수 있습니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드 

먼저 다운로드 하려는 파일을 나타내는 [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) 인스턴스를 만듭니다. [DataLakeFileClient ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) 메서드를 사용 하 고 반환 값을 구문 분석 하 여 [스트림](/dotnet/api/system.io.stream) 개체를 가져옵니다. 모든 .NET 파일 처리 API를 사용 하 여 스트림에서 바이트를 파일에 저장 합니다. 

이 예에서는 [BinaryReader](/dotnet/api/system.io.binaryreader) 및 [FileStream](/dotnet/api/system.io.filestream) 을 사용 하 여 바이트를 파일에 저장 합니다. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

[FileSystemClient GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) 메서드를 호출 하 고 결과를 열거 하 여 디렉터리 내용을 나열 합니다.

이 예에서는 라는 디렉터리에 있는 각 파일의 이름을 인쇄 `my-directory` 합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>참고 항목

- [API 참조 설명서](/dotnet/api/azure.storage.files.datalake)
- [패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [피드백 보내기](https://github.com/Azure/azure-sdk-for-net/issues)