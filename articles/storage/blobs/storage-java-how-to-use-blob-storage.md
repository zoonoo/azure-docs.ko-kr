---
title: "Java에서 Azure Blob Storage(개체 저장소)를 사용하는 방법 | Microsoft Docs"
description: "Azure Blob 저장소(개체 저장소)를 사용하여 클라우드에 구조화되지 않은 데이터를 저장합니다."
services: storage
documentationcenter: java
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 2e223b38-92de-4c2f-9254-346374545d32
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e4de1bc57adf668f383d1fbaf4a721a61576d2a0
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="how-to-use-blob-storage-from-java"></a>Java에서 Blob 저장소를 사용하는 방법
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>개요
Azure Blob 저장소는 클라우드에 구조화되지 않은 데이터를 개체/Blob로 저장하는 서비스입니다. Blob 저장소는 문서, 미디어 파일 또는 응용 프로그램 설치 프로그램과 같은 모든 종류의 텍스트 또는 이진 데이터를 저장할 수 있습니다. 또한 Blob 저장소를 개체 저장소라고 합니다.

이 문서에서는 Microsoft Azure Blob 저장소를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure Storage SDK][Azure Storage SDK for Java](영문)를 사용합니다. 여기서 다루는 시나리오에는 Blob **업로드**, **나열**, **다운로드** 및 **삭제**가 포함됩니다. Blob에 대한 자세한 내용은 [다음 단계](#Next-Steps) 섹션을 참조하십시오.

> [!NOTE]
> SDK는 Android 장치에서 Azure 저장소를 사용하는 개발자에게 제공됩니다. 자세한 내용은 [Android용 Azure Storage SDK][Azure Storage SDK for Android]를 참조하세요.
>
>

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java 응용 프로그램 만들기
이 문서에서는 Java 응용 프로그램 내에서 로컬로 또는 Azure의 웹 역할 또는 작업자 역할 내에서 실행되는 코드에서 실행할 수 있는 저장소 기능을 사용합니다.

그러려면 JDK(Java Development Kit)를 설치하고 Azure 구독에서 Azure 저장소 계정을 만들어야 합니다. 그러고 나면 개발 시스템에서 GitHub의 [Java용 Azure Storage SDK][Azure Storage SDK for Java] 리포지토리에 있는 최소 요구 사항과 종속성을 충족하는지 확인해야 합니다. 시스템에서 해당 요구 사항을 충족하는 경우에는 리포지토리에서 시스템의 Java용 Azure Storage Library를 다운로드 및 설치하기 위한 지침을 따를 수 있습니다. 작업을 완료하고 나면 이 문서의 예를 사용하는 Java 응용 프로그램을 만들 수 있습니다.

## <a name="configure-your-application-to-access-blob-storage"></a>Blob 저장소에 액세스하도록 응용 프로그램 구성
Azure 저장소 API를 사용하여 Blob에 액세스하려는 Java 파일의 맨 위에 다음 import 문을 추가합니다.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure 저장소 연결 문자열 설정
Azure 저장소 클라이언트는 저장소 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 끝점 및 자격 증명을 저장합니다. 클라이언트 응용 프로그램에서 실행할 경우 *AccountName*과 *AccountKey* 값에 대해 [Azure Portal](https://portal.azure.com)에 나열된 저장소 계정의 이름과 기본 선택키를 사용하여 다음 형식의 저장소 연결 문자열을 제공해야 합니다. 다음 예제에서는 연결 문자열을 포함할 정적 필드를 선언하는 방법을 보여 줍니다.

```java
// Define the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Microsoft Azure의 역할 내에서 실행되는 응용 프로그램에서는 이 문자열이 서비스 구성 파일 *ServiceConfiguration.cscfg*에 저장될 수 있고, **RoleEnvironment.getConfigurationSettings** 메서드 호출을 통해 이 문자열에 액세스할 수 있습니다. 다음 예제에서는 서비스 구성 파일에서 이름이 *StorageConnectionString* 인 **설정** 요소의 연결 문자열을 가져옵니다.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

다음 샘플에서는 저장소 연결 문자열을 가져오기 위해 위의 두 메서드 중 하나를 사용한 것으로 가정합니다.

## <a name="create-a-container"></a>컨테이너 만들기
**CloudBlobClient** 개체를 사용하면 컨테이너 및 Blob에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudBlobClient** 개체를 만듭니다.

> [!NOTE]
> **CloudStorageAccount** 개체를 만들 수 있는 방법이 더 있습니다. 자세한 내용은 [Azure Storage Client SDK Reference]에서 **CloudStorageAccout**를 참조하세요.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

**CloudBlobClient** 개체를 사용하여 사용할 컨테이너에 대한 참조를 가져올 수 있습니다. 컨테이너가 없다면 **createIfNotExists** 메서드로 만들 수 있습니다. 기존 컨테이너가 있으면 해당 컨테이너가 반환됩니다. 기본적으로 새 컨테이너는 전용이므로 이 컨테이너에서 Blob을 다운로드하려면 앞에서 한 것처럼 저장소 액세스 키를 지정해야 합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Get a reference to a container.
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist.
    container.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

### <a name="optional-configure-a-container-for-public-access"></a>옵션: 공용 액세스에 대한 컨테이너 구성
컨테이너 권한은 기본적으로 개인 액세스용으로 구성되지만 인터넷에 있는 모든 사용자가 공용으로 읽기 전용 액세스를 사용할 수 있도록 컨테이너의 권한을 간단하게 구성할 수 있습니다.

```java
// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-a-blob-into-a-container"></a>컨테이너에 Blob 업로드
Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 후 이 참조를 사용하여 Blob 참조를 가져옵니다. Blob 참조가 있으면 Blob 참조의 upload를 호출하여 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 다음 코드 샘플은 이 작업을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Define the path to a local file.
    final String filePath = "C:\\myimages\\myimage.jpg";

    // Create or overwrite the "myimage.jpg" blob with contents from a local file.
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File(filePath);
    blob.upload(new FileInputStream(source), source.length());
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열
컨테이너의 Blob을 나열하려면 Blob을 업로드할 때 수행한 것과 마찬가지로 컨테이너 참조를 가져옵니다. **for** 루프와 함께 컨테이너의 **listBlobs** 메서드를 사용할 수 있습니다. 다음 코드는 컨테이너에 있는 각 Blob의 Uri를 콘솔에 출력합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them.
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

이름에 대한 경로 정보를 사용하여 Blob 이름을 지정할 수 있습니다. 이렇게 하면 기존 파일 시스템과 같이 구성 및 트래버스할 수 있는 가상 디렉터리 구조를 만듭니다. 디렉터리 구조는 가상만 해당됩니다. Blob 저장소에서 사용할 수 있는 리소스만 컨테이너 및 Blob입니다. 하지만 클라이언트 라이브러리는 가상 디렉터리를 참조하도록 **CloudBlobDirectory** 개체를 제공하며 이러한 방식으로 구성되는 Blob을 사용한 작업 과정을 단순화합니다.

예를 들어 이름이 "photos"인 컨테이너가 있는 경우 이 컨테이너에서 이름이 "rootphoto1", "2010/photo1", "2010/photo2" 및 "2011/photo1"인 Blob을 업로드할 수 있습니다. 이 작업은 "photos" 컨테이너 내에 "2010" 및 "2011" 가상 디렉터리를 만듭니다. "photos" 컨테이너에서 **listBlobs**를 호출하면 최상위 수준에서 포함된 디렉터리 및 Blob을 나타내는 **CloudBlobDirectory** 및 **CloudBlob** 개체가 반환된 컬렉션에 포함됩니다. 이 경우에는 디렉터리 "2010", "2011" 및 사진 "rootphoto1"이 반환됩니다. **instanceof** 연산자를 사용하여 이러한 개체를 구분할 수 있습니다.

선택적으로 **useFlatBlobListing** 매개 변수의 값을 True로 설정하여 **listBlobs** 메서드에 전달할 수도 있습니다. 그러면 모든 Blob이 디렉터리에 상관없이 반환됩니다. 자세한 내용은 **Azure 저장소 클라이언트 SDK 참조** 에서 [Azure Storage Client SDK Reference]를 참조하세요.

## <a name="download-a-blob"></a>Blob 다운로드
Blob을 다운로드하려면 Blob 참조를 가져오기 위해 Blob을 업로드할 때 수행한 것과 동일한 단계를 따릅니다. 업로드 예제에서는 Blob 개체에 대해 upload를 호출했습니다. 다음 예제에서는 download를 호출하여 Blob을 로컬 파일에 저장하는 데 사용할 수 있는 **FileOutputStream** 과 같은 스트림 개체로 Blob 콘텐츠를 전송합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop through each blob item in the container.
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory.
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name.
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob"></a>Blob 삭제
Blob을 삭제하려면 Blob 참조를 가져온 후 **deleteIfExists**를 호출합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg".
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob.
    blob.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob-container"></a>Blob 컨테이너 삭제
마지막으로 Blob 컨테이너를 삭제하려면 Blob 컨테이너 참조를 가져온 후 **deleteIfExists**를 호출합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container.
    container.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>다음 단계
이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀더 복잡한 저장소 작업에 대해 알아보세요.

* [Java용 Azure Storage SDK][Azure Storage SDK for Java]
* [Azure Storage Client SDK Reference][Azure Storage Client SDK Reference]
* [Azure Storage REST API][Azure Storage REST API]
* [Azure Storage 팀 블로그][Azure Storage Team Blog]

자세한 내용은 [Java 개발자용 Azure](/java/azure)도 참조하세요.

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK Reference]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

