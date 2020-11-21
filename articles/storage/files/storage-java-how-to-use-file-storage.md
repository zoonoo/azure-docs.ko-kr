---
title: Java를 사용하여 Azure Files 개발 | Microsoft Docs
description: Azure Files를 사용하여 파일 데이터를 저장하는 Java 애플리케이션 및 서비스를 개발하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024112"
---
# <a name="develop-for-azure-files-with-java"></a>Java를 사용하여 Azure Files 개발

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Azure Files를 사용 하 여 데이터를 저장 하는 Java 응용 프로그램을 개발 하는 기본 사항을 알아봅니다. 콘솔 응용 프로그램을 만들고 Azure Files Api를 사용 하 여 기본 작업을 알아봅니다.

- Azure 파일 공유 만들기 및 삭제
- 디렉터리 만들기 및 삭제
- Azure 파일 공유의 파일 및 디렉터리 열거
- 파일 업로드, 다운로드 및 삭제

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기

예제를 빌드하려면 java 용 JDK (Java Development Kit) 및 [AZURE STORAGE SDK](https://github.com/azure/azure-sdk-for-java)가 필요 합니다. Azure Storage 계정도 만들었어야 합니다.

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files를 사용하도록 애플리케이션 설정

Azure Files Api를 사용 하려면 Azure Files에 액세스 하려는 Java 파일의 맨 위에 다음 코드를 추가 합니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 연결 문자열 설정

Azure Files를 사용하려면 Azure Storage 계정에 연결해야 합니다. 연결 문자열을 구성 하 고이를 사용 하 여 저장소 계정에 연결 합니다. 연결 문자열을 보유할 정적 변수를 정의 합니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

*\<storage_account_name\>* 및를 *\<storage_account_key\>* 저장소 계정에 대 한 실제 값으로 바꿉니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

*Your_storage_account_name* 및 *your_storage_account_key* 을 저장소 계정에 대 한 실제 값으로 바꿉니다.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Azure Files storage 액세스

# <a name="java-v12"></a>[Java v12](#tab/java)

Azure Files에 액세스 하려면 [Shareclient](/java/api/com.azure.storage.file.share.shareclient) 개체를 만듭니다. [Shareclientbuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) 클래스를 사용 하 여 새 **shareclient** 개체를 빌드합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

저장소 계정에 액세스 하려면 **Cloudstorageaccount** 개체를 사용 하 여 해당 **parse** 메서드에 연결 문자열을 전달 합니다.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** 는 InvalidKeyException을 발생시키므로 try/catch 블록 안에 넣어야 합니다.

---

## <a name="create-a-file-share"></a>파일 공유 만들기

Azure Files의 모든 파일 및 디렉터리는 공유 라는 컨테이너에 저장 됩니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

공유가 이미 있는 경우 [Shareclient. create](/java/api/com.azure.storage.file.share.shareclient.create) 메서드는 예외를 throw 합니다. 호출을 블록에 **넣고** 예외를 `try/catch` 처리 합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

공유 및 해당 콘텐츠에 대 한 액세스 권한을 얻으려면 Azure Files 클라이언트를 만듭니다.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Azure Files 클라이언트를 사용하면 공유에 대한 참조를 가져올 수 있습니다.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

실제로 공유를 만들려면 **cloudfileshare** 개체의 **Createifnotexists** 메서드를 사용 합니다.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

이 시점에서 **공유** 는 **sample share** 라는 공유에 대 한 참조를 보유 합니다.

---

## <a name="delete-a-file-share"></a>파일 공유 삭제

다음 샘플 코드에서는 파일 공유를 삭제 합니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

[Shareclient. delete](/java/api/com.azure.storage.file.share.shareclient.delete) 메서드를 호출 하 여 공유를 삭제 합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Cloudfilefileobject에서 **Deleteifexists** 메서드를 호출 하 **CloudFileShare** 여 공유를 삭제 합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>디렉터리 만들기

루트 디렉터리에 파일을 모두 포함 하는 대신 하위 디렉터리 내에 파일을 배치 하 여 저장소를 구성 합니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

다음 코드는 [Sharedirectoryclient. create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create)를 호출 하 여 디렉터리를 만듭니다. 예제 메서드는 `Boolean` 디렉터리를 성공적으로 만들었는지 여부를 나타내는 값을 반환 합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

다음 코드는 루트 디렉터리 아래에 **sampledir** 라는 하위 디렉터리를 만듭니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>디렉터리 삭제

디렉터리를 삭제 하는 작업은 간단 합니다. 여전히 파일 또는 하위 디렉터리가 포함 된 디렉터리는 삭제할 수 없습니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

디렉터리가 없거나 비어 있지 않은 경우 [Sharedirectoryclient. delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) 메서드는 예외를 throw 합니다. 블록에 **삭제** 에 대 한 호출을 추가 하 `try/catch` 고 예외를 처리 합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure 파일 공유의 파일 및 디렉터리 열거

# <a name="java-v12"></a>[Java v12](#tab/java)

[Sharedirectoryclient. listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories)를 호출 하 여 파일 및 디렉터리 목록을 가져옵니다. 메서드는 반복할 수 있는 [Sharefileitem](/java/api/com.azure.storage.file.share.models.sharefileitem) 개체의 목록을 반환 합니다. 다음 코드는 파일 및 디렉터리 내에서 파일 *이름* 매개 변수를 지정 합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

**Cloudfiledirectory** 참조에서 **listfilesanddirectories** 를 호출 하 여 파일 및 디렉터리 목록을 가져옵니다. 메서드는 반복할 수 있는 **Listfileitem** 개체의 목록을 반환 합니다. 다음 코드는 루트 디렉터리 내의 파일 및 디렉터리를 나열 합니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>파일 업로드

로컬 저장소에서 파일을 업로드 하는 방법을 알아봅니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

다음 코드는 [ShareFileClient uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) 메서드를 호출 하 여 Azure file storage에 로컬 파일을 업로드 합니다. 다음 예제 메서드는 지정 된 `Boolean` 파일을 성공적으로 업로드 했음을 나타내는 값을 반환 합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

공유 개체의 **Getrootdirectoryreference** 메서드를 호출 하 여 파일이 업로드 될 디렉터리에 대 한 참조를 가져옵니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

이제 공유의 루트 디렉터리에 대한 참조를 가졌으므로 다음 코드를 사용하여 파일을 업로드할 수 있습니다.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>파일 다운로드

자주 수행 되는 작업 중 하나는 Azure Files 저장소에서 파일을 다운로드 하는 것입니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

다음 예에서는 지정 된 파일을 *Destdir* 매개 변수에 지정 된 로컬 디렉터리에 다운로드 합니다. 예제 메서드는 날짜 및 시간 앞에를 사용 하 여 다운로드 한 파일 이름을 고유 하 게 만듭니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

다음 예에서는 SampleFile.txt를 다운로드 하 고 해당 내용을 표시 합니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>파일 삭제

다른 일반적인 Azure Files 작업은 파일 삭제입니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

다음 코드는 지정 된 지정 된 파일을 삭제 합니다. 먼저이 예에서는 *Diname* 매개 변수를 기반으로 [sharedirectoryclient](/java/api/com.azure.storage.file.share.sharedirectoryclient) 를 만듭니다. 그런 다음, 코드는 *fileName* 매개 변수를 기반으로 디렉터리 클라이언트에서 [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) 를 가져옵니다. 마지막으로, 예제 메서드는 [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient.delete) 를 호출 하 여 파일을 삭제 합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

다음 코드는 **sampledir** 라는 이름의 디렉터리 안에 저장된 SampleFile.txt라는 이름의 파일을 삭제합니다.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>다음 단계

다른 Azure Storage API에 대해 더 알아보려면 다음 링크를 따르십시오.

- [Java 개발자용 Azure](/azure/developer/java)
- [Java용 Azure SDK](https://github.com/azure/azure-sdk-for-java)
- [Android 용 Azure SDK](https://github.com/azure/azure-sdk-for-android)
- [Java SDK 용 Azure 파일 공유 클라이언트 라이브러리 참조](/java/api/overview/azure/storage-file-share-readme)
- [Azure Storage 서비스 REST API](/rest/api/storageservices/)
- [Azure Storage 팀 블로그](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [AzCopy 명령줄 유틸리티로 데이터 전송](../common/storage-use-azcopy-v10.md)
- [Azure Files 문제 해결 - Windows](storage-troubleshoot-windows-file-connection-problems.md)
