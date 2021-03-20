---
title: Java를 사용 하 여 Azure Data Lake Storage Gen2의 데이터 관리
description: Java 용 Azure Storage 라이브러리를 사용 하 여 계층적 네임 스페이스를 사용 하는 저장소 계정의 디렉터리와 파일을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 10debe7bb870ddd9f8711e73ccb4b690d7011b62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650190"
---
# <a name="use-java-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Java를 사용 하 여 Azure Data Lake Storage Gen2에서 디렉터리 및 파일 관리

이 문서에서는 Java를 사용 하 여 계층적 네임 스페이스를 포함 하는 저장소 계정의 디렉터리와 파일을 만들고 관리 하는 방법을 보여 줍니다.

디렉터리 및 파일의 ACL (액세스 제어 목록)을 가져오거나 설정 하 고 업데이트 하는 방법에 대 한 자세한 내용은 사용을 참조 하세요 [. Azure Data Lake Storage Gen2에서 Acl을 관리 하는 Java](data-lake-storage-acl-java.md)

[패키지 (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API 참조](/java/api/overview/azure/storage-file-datalake-readme)  |  [Gen1 To Gen2 mapping](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- 계층적 네임 스페이스를 사용 하는 저장소 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

시작 하려면 [이 페이지](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) 를 열고 최신 버전의 Java 라이브러리를 찾습니다. 그런 다음 텍스트 편집기에서 *pom.xml* 파일을 엽니다. 해당 버전을 참조 하는 종속성 요소를 추가 합니다.

Azure Active Directory (Azure AD)를 사용 하 여 클라이언트 응용 프로그램을 인증할 계획인 경우 Azure Secret Client 라이브러리에 종속성을 추가 합니다. [프로젝트에 비밀 클라이언트 라이브러리 패키지 추가를](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)참조 하세요.

그런 다음 이러한 imports 문을 코드 파일에 추가 합니다.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory를 사용 하 여 연결 (Azure AD)

[Java 용 azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 획득](../common/storage-auth-aad-app.md)을 참조 하세요.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> 더 많은 예제는 [Java 용 Azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) 설명서를 참조 하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일에 대 한 파일 시스템 역할을 합니다. **DataLakeServiceClient** 메서드를 호출 하 여 만들 수 있습니다.

이 예제에서는 라는 컨테이너를 만듭니다 `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>디렉터리 만들기

**DataLakeFileSystemClient** 메서드를 호출 하 여 디렉터리 참조를 만듭니다.

이 예제에서는 라는 디렉터리를 `my-directory` 컨테이너에 추가한 다음 라는 하위 디렉터리를 추가 `my-subdirectory` 합니다. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리 이름을 바꾸거나 이동 합니다. 원하는 디렉터리의 경로를 매개 변수로 전달 합니다. 

이 예에서는 하위 디렉터리의 이름을로 바꿉니다 `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

이 예에서는 라는 디렉터리를 `my-subdirectory-renamed` 라는 디렉터리의 하위 디렉터리로 이동 `my-directory-2` 합니다. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>디렉터리 삭제

**DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리를 삭제 합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 **DataLakeFileClient** 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. **DataLakeFileClient** 메서드를 호출 하 여 파일을 업로드 합니다. **DataLakeFileClient FlushAsync** 메서드를 호출 하 여 업로드를 완료 해야 합니다.

이 예제에서는 라는 디렉터리에 텍스트 파일을 업로드 `my-directory` 합니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> 파일 크기가 큰 경우 코드에서 **DataLakeFileClient** 메서드를 여러 번 호출 해야 합니다. 대신 **DataLakeFileClient uploadFromFile** 메서드를 사용 하는 것이 좋습니다. 이렇게 하면 단일 호출에서 전체 파일을 업로드할 수 있습니다. 
>
> 예제는 다음 섹션을 참조하세요.

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 용량이 많은 파일 업로드

**DataLakeFileClient** 메서드를 여러 번 호출 하지 않고도 **DataLakeFileClient uploadFromFile** 메서드를 사용 하 여 많은 파일을 업로드할 수 있습니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

먼저 다운로드 하려는 파일을 나타내는 **DataLakeFileClient** 인스턴스를 만듭니다. **DataLakeFileClient** 메서드를 사용 하 여 파일을 읽습니다. 모든 .NET 파일 처리 API를 사용 하 여 스트림에서 바이트를 파일에 저장 합니다. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

이 예에서는 라는 디렉터리에 있는 각 파일의 이름을 인쇄 `my-directory` 합니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>참고 항목

* [API 참조 설명서](/java/api/overview/azure/storage-file-datalake-readme)
* [패키지(Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [피드백 보내기](https://github.com/Azure/azure-sdk-for-java/issues)