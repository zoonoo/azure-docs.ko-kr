---
title: Acl & 파일에 대 한 Java SDK Azure Data Lake Storage Gen2 (미리 보기)
description: Java 용 Azure Storage 라이브러리를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정의 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 0f9489cd702eab6038689f6ac710c32427665093
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031121"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Java를 사용 하 여 Azure Data Lake Storage Gen2에서 디렉터리, 파일 및 Acl 관리 (미리 보기)

이 문서에서는 Java를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리 하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> 이 문서에서 설명 하는 Java 라이브러리는 현재 공개 미리 보기로 제공 됩니다.

[Package (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html) | [Gen1 to Gen2 mapping](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임 스페이스 (HNS)를 사용 하도록 설정 된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 새로 만듭니다.

## <a name="set-up-your-project"></a>프로젝트 설정

시작 하려면 [이 페이지](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) 를 열고 최신 버전의 Java 라이브러리를 찾습니다. 그런 다음 텍스트 편집기에서 *pom .xml* 파일을 엽니다. 해당 버전을 참조 하는 종속성 요소를 추가 합니다.

그런 다음 이러한 imports 문을 코드 파일에 추가 합니다.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>계정에 연결 

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 하나를 가져오는 가장 쉬운 방법은 계정 키를 사용 하는 것입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 의 인스턴스를 만듭니다.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      

```
## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. **DataLakeServiceClient** 메서드를 호출 하 여 만들 수 있습니다.

이 예에서는 `my-file-system`이라는 파일 시스템을 만듭니다. 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>디렉터리 만들기

**DataLakeFileSystemClient** 메서드를 호출 하 여 디렉터리 참조를 만듭니다.

이 예제에서는 `my-directory` 라는 디렉터리를 파일 시스템에 추가한 다음 `my-subdirectory`라는 하위 디렉터리를 추가 합니다. 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리 이름을 바꾸거나 이동 합니다. 원하는 디렉터리의 경로를 매개 변수로 전달 합니다. 

이 예에서는 `my-subdirectory-renamed`이름으로 하위 디렉터리의 이름을 바꿉니다.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename("my-directory/my-subdirectory-renamed");
}
```

이 예제에서는 이름이 `my-subdirectory-renamed` 인 디렉터리를 `my-directory-2`디렉터리의 하위 디렉터리로 이동 합니다. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>디렉터리 삭제

**DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리를 삭제 합니다.

이 예에서는 `my-directory`라는 디렉터리를 삭제 합니다.   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>디렉터리 ACL 관리

이 예제에서는 `my-directory`된 디렉터리의 ACL을 가져온 다음 설정 합니다. 이 예제에서는 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 권한을 부여 합니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조 하세요.

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    PathPermissions permissions = new PathPermissions()

      .group(new RolePermissions().execute(true).read(true))
      .owner(new RolePermissions().execute(true).read(true).write(true))
      .other(new RolePermissions().read(true));

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 **DataLakeFileClient** 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. **DataLakeFileClient** 메서드를 호출 하 여 파일을 업로드 합니다. **DataLakeFileClient FlushAsync** 메서드를 호출 하 여 업로드를 완료 해야 합니다.

이 예제에서는 `my-directory`라는 디렉터리에 텍스트 파일을 업로드 합니다.

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

## <a name="manage-a-file-acl"></a>파일 ACL 관리

이 예제에서는 `upload-file.txt`이라는 파일의 ACL을 가져온 다음 설정 합니다. 이 예제에서는 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 권한을 부여 합니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조 하세요.

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    PathPermissions permissions = new PathPermissions()

        .group(new RolePermissions().execute(true).read(true))
        .owner(new RolePermissions().execute(true).read(true).write(true))
        .other(new RolePermissions().read(false));

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

먼저 다운로드 하려는 파일을 나타내는 **DataLakeFileClient** 인스턴스를 만듭니다. **DataLakeFileClient** 메서드를 사용 하 여 파일을 읽습니다. 모든 .NET 파일 처리 API를 사용 하 여 스트림에서 바이트를 파일에 저장 합니다. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

이 예에서는 `my-directory`라는 디렉터리에 있는 각 파일의 이름을 인쇄 합니다.

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>참고 항목

* [API 참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html)
* [패키지 (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake/12.0.0-preview.6/jar)
* [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 to Gen2 mapping](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-java/issues)