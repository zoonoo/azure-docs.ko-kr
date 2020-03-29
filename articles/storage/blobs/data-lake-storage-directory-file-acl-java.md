---
title: ACL을 & 파일에 대한 Azure 데이터 레이크 스토리지 Gen2 Java SDK
description: Java용 Azure 저장소 라이브러리를 사용하여 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 디렉터리 및 파일 및 디렉터리 액세스 제어 목록(ACL)을 관리합니다.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061566"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Java를 사용하여 Azure 데이터 레이크 저장소 Gen2에서 디렉터리, 파일 및 ACL 관리

이 문서에서는 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리하기 위해 Java를 사용하는 방법을 보여 주며, 이 문서에서는 

[패키지 (메이븐)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 에서 Gen2 매핑으로](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임스페이스(HNS)가 활성화된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 만들 수 있습니다.

## <a name="set-up-your-project"></a>프로젝트 설정

시작하려면 이 [페이지를](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) 열고 Java 라이브러리의 최신 버전을 찾습니다. 그런 다음 텍스트 편집기에서 *pom.xml* 파일을 엽니다. 해당 버전을 참조하는 종속성 요소를 추가합니다.

Azure Active Directory(AD)를 사용하여 클라이언트 응용 프로그램을 인증하려는 경우 Azure 보안 클라이언트 라이브러리에 종속성을 추가합니다. [프로젝트에 비밀 클라이언트 라이브러리 패키지 추가를](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)참조하십시오.

다음으로 코드 파일에 이러한 가져오기 문을 추가합니다.

```java
import com.azure.core.credential.TokenCredential;
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

이 문서의 스니펫을 사용하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용하여 연결

이렇게 하면 계정에 연결하는 가장 쉬운 방법이 있습니다. 

이 예제는 계정 키를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active 디렉터리(Azure AD)를 사용하여 연결

[Java용 Azure ID 클라이언트 라이브러리를](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) 사용하여 Azure AD를 사용하여 응용 프로그램을 인증할 수 있습니다.

이 예제에서는 클라이언트 ID, 클라이언트 암호 및 테넌트 ID를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 가져오려면 [클라이언트 응용 프로그램에서 요청을 승인하기 위해 Azure AD에서 토큰 가져오기를](../common/storage-auth-aad-app.md)참조하십시오.

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> 자세한 예는 Java 설명서에 [대한 Azure ID 클라이언트 라이브러리를](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) 참조하십시오.


## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. **DataLakeServiceClient.createFileSystem** 메서드를 호출하여 하나를 만들 수 있습니다.

이 예제는 .라는 `my-file-system`파일 시스템을 만듭니다. 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>디렉터리 만들기

**DataLakeFileSystemClient.createDirectory** 메서드를 호출 하여 디렉터리 참조를 만듭니다.

이 예제에서는 파일 `my-directory` 시스템에 명명된 디렉터리를 추가한 다음 `my-subdirectory`. 

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

**DataLakeDirectoryClient.rename** 메서드를 호출하여 디렉터리 이름을 바꾸거나 이동합니다. 원하는 디렉터리 매개 변수의 경로를 전달합니다. 

이 예제는 하위 디렉터리 이름을 `my-subdirectory-renamed`이름으로 바꿉니다.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

이 예제는 `my-subdirectory-renamed` `my-directory-2`명명된 디렉터리를 .라는 디렉터리라는 디렉터리로 이동합니다. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>디렉터리 삭제

**DataLakeDirectoryClient.deleteWithResponse** 메서드를 호출 하여 디렉토리를 삭제 합니다.

이 예제는 .라는 `my-directory`디렉터리를 삭제합니다.   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>디렉터리 ACL 관리

이 예제는 . `my-directory` 이 예제에서는 소유 사용자에게 읽기, 쓰기 및 실행 권한을 제공하고, 소유 그룹에 읽기 및 실행 권한만 제공하고, 다른 모든 읽기 액세스 권한을 부여합니다.

> [!NOTE]
> 응용 프로그램이 Azure Active Directory(Azure AD)를 사용하여 액세스 권한을 부여한 경우 응용 프로그램에서 액세스를 승인하는 데 사용하는 보안 주체가 [저장소 Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>디렉토리에 파일 업로드

먼저 **DataLakeFileClient** 클래스의 인스턴스를 만들어 대상 디렉터리에서 파일 참조를 만듭니다. **DataLakeFileClient.append** 메서드를 호출하여 파일을 업로드합니다. **DataLakeFileClient.FlushAsync** 메서드를 호출하여 업로드를 완료해야 합니다.

이 예제는 텍스트 파일을 라는 `my-directory`디렉터리로 업로드합니다.

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

> [!TIP]
> 파일 크기가 큰 경우 코드에서 **DataLakeFileClient.append** 메서드를 여러 번 호출해야 합니다. 대신 **DataLakeFileClient.uploadFromFile** 메서드를 사용하는 것이 좋습니다. 이렇게 하면 전체 파일을 단일 호출로 업로드할 수 있습니다. 
>
> 예제는 다음 섹션을 참조하세요.

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 대용량 파일 업로드

**DataLakeFileClient.uploadFromFile** 메서드를 사용하여 **DataLakeFileClient.append** 메서드를 여러 번 호출하지 않고도 대용량 파일을 업로드할 수 있습니다.

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>파일 ACL 관리

이 예제는 . `upload-file.txt` 이 예제에서는 소유 사용자에게 읽기, 쓰기 및 실행 권한을 제공하고, 소유 그룹에 읽기 및 실행 권한만 제공하고, 다른 모든 읽기 액세스 권한을 부여합니다.

> [!NOTE]
> 응용 프로그램이 Azure Active Directory(Azure AD)를 사용하여 액세스 권한을 부여한 경우 응용 프로그램에서 액세스를 승인하는 데 사용하는 보안 주체가 [저장소 Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

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
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>디렉토리에서 다운로드

먼저 다운로드할 파일을 나타내는 **DataLakeFileClient** 인스턴스를 만듭니다. **DataLakeFileClient.read** 메서드를 사용하여 파일을 읽습니다. 모든 .NET 파일 처리 API를 사용하여 스트림에서 파일로 바이트를 저장합니다. 

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

이 예제에서는 라는 `my-directory`디렉터리에 있는 각 파일의 이름을 인쇄합니다.

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

## <a name="see-also"></a>참조

* [API 참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [패키지 (메이븐)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen 1 ~ Gen2 매핑](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)