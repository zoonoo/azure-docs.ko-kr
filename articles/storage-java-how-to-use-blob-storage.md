<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

Java에서 Blob 저장소를 사용하는 방법
====================================

이 가이드에서는 Azure Blob 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure SDK](http://www.windowsazure.com/en-us/develop/java/)(영문)를 사용합니다. Blob **업로드**, **나열**, **다운로드** 및 **삭제** 시나리오를 다룹니다. Blob에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하십시오.

목차
----

-   [Blob 저장소 정의](#what-is)
-   [개념](#Concepts)
-   [Azure 저장소 계정 만들기](#CreateAccount)
-   [Java 응용 프로그램 만들기](#CreateApplication)
-   [Blob 저장소에 액세스하도록 응용 프로그램 구성](#ConfigureStorage)
-   [Azure 저장소 연결 문자열 설정](#ConnectionString)
-   [방법: 컨테이너 만들기](#CreateContainer)
-   [방법: 컨테이너에 Blob 업로드](#UploadBlob)
-   [방법: 컨테이너의 Blob 나열](#ListBlobs)
-   [방법: Blob 다운로드](#DownloadBlob)
-   [방법: Blob 삭제](#DeleteBlob)
-   [방법: Blob 컨테이너 삭제](#DeleteContainer)
-   [다음 단계](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Azure 저장소 계정 만들기
------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Java 응용 프로그램 만들기
-------------------------

이 가이드에서는 Java 응용 프로그램 내에서 로컬로 또는 Azure의 웹 역할 또는 작업자 역할 내에서 실행되는 코드에서 실행할 수 있는 저장소 기능을 사용합니다. JDK(Java 개발 키트)를 다운로드하여 설치했으며 [Java용 Azure SDK 다운로드](http://www.windowsazure.com/en-us/develop/java/)(영문)의 지침에 따라 Java용 Azure 라이브러리 및 Azure SDK를 설치하고 Azure 구독에서 Azure 저장소 계정을 만들었다고 가정합니다.

응용 프로그램을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다. 또한 Java 프로젝트를 컴파일하고 Java용 Azure 라이브러리를 참조할 수 있어야 합니다.

Blob 저장소에 액세스하도록 응용 프로그램 구성
---------------------------------------------

Azure 저장소 API를 사용하여 Blob에 액세스하려는 Java 파일의 맨 위에 다음 import 문을 추가합니다.

    // Include the following imports to use blob APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

Azure 저장소 연결 문자열 설정
-----------------------------

Azure 저장소 클라이언트는 저장소 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하는 데 사용할 끝점 및 자격 증명을 저장합니다. 클라이언트 응용 프로그램에서 실행 중인 경우 관리 포털에 나열된 저장소 계정의 이름 및 저장소 계정의 기본 액세스 키를 *AccountName* 및 *AccountKey* 값에 사용하여 저장소 연결 문자열을 다음 형식으로 지정해야 합니다. 이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Azure의 역할 내에서 실행되는 응용 프로그램에서, 이 문자열은 서비스 구성 파일인 ServiceConfiguration.cscfg에 저장할 수 있으며 **RoleEnvironment.getConfigurationSettings** 메서드에 대한 호출을 통해 액세스될 수 있습니다. 다음은 서비스 구성 파일에서 이름이 *StorageConnectionString*인 **설정** 요소에서 연결 문자열을 가져오는 예제입니다.

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

방법: 컨테이너 만들기
---------------------

CloudBlobClient 개체를 사용하면 컨테이너 및 Blob에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudBlobClient** 개체를 만듭니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

모든 Blob은 컨테이너에 있습니다. **CloudBlobClient** 개체를 사용하여 사용할 컨테이너에 대한 참조를 가져올 수 있습니다. 컨테이너가 없는 경우 **createIfNotExist** 메서드로 만들 수 있습니다. 기존 컨테이너가 있으면 해당 컨테이너가 반환됩니다. 기본적으로 새 컨테이너는 전용이므로 이 컨테이너에서 Blob을 다운로드하려면 저장소 액세스 키(위에서 한 것과 같이)를 지정해야 합니다.

    // Get a reference to a container
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist
    container.createIfNotExist();

파일을 공용으로 설정하려는 경우 컨테이너의 사용 권한을 설정할 수 있습니다.

    // Create a permissions object
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container
    container.uploadPermissions(containerPermissions);

인터넷의 모든 사용자가 공용 컨테이너의 Blob을 볼 수 있지만 이러한 공용 액세스는 읽기 전용으로 제한됩니다.

방법: 컨테이너에 Blob 업로드
----------------------------

Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 후 이 참조를 사용하여 Blob 참조를 가져옵니다. Blob 참조가 있으면 Blob 참조의 upload를 호출하여 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 다음 코드 샘플은 이를 보여 주며, 컨테이너가 이미 만들어졌다고 가정합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create or overwrite the "myimage.jpg" blob with contents from a local file
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\myimages\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

방법: 컨테이너의 Blob 나열
--------------------------

컨테이너의 Blob을 나열하려면 Blob을 업로드할 때 수행한 것과 마찬가지로 컨테이너 참조를 가져옵니다. for 루프와 함께 컨테이너의 **listBlobs** 메서드를 사용할 수 있습니다. 다음 코드는 컨테이너에 있는 각 Blob의 Uri를 콘솔에 출력합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }

Blob 서비스에는 컨테이너 내 디렉터리의 개념도 있습니다. 따라서 폴더와 유사한 구조로 Blob을 구성할 수 있습니다.

예를 들어 이름이 "photos"인 컨테이너가 있는 경우 이 컨테이너에서 이름이 "rootphoto1", "2010/photo1", "2010/photo2" 및 "2011/photo1"인 Blob을 업로드할 수 있습니다. 이 작업은 "photos" 컨테이너 내에 "2010" 및 "2011" 가상 디렉터리를 만듭니다. "photos" 컨테이너에서 **listBlobs**를 호출하면 최상위 수준에서 포함된 디렉터리 및 Blob을 나타내는 **CloudBlobDirectory** 및 **CloudBlob** 개체가 반환된 컬렉션에 포함됩니다. 이 경우에는 디렉터리 "2010", "2011" 및 사진 "rootphoto1"이 반환됩니다. **instanceof** 연산자를 사용하여 이러한 개체를 구분할 수 있습니다.

선택적으로 **useFlatBlobListing** 매개 변수의 값을 True로 설정하여 **listBlobs** 메서드에 전달할 수도 있습니다. 그러면 모든 Blob이 디렉터리에 상관없이 반환됩니다. 자세한 내용은 Javadocs 설명서의 CloudBlobContainer.listBlobs를 참조하십시오.

방법: Blob 다운로드
-------------------

Blob을 다운로드하려면 Blob 참조를 가져오기 위해 Blob을 업로드할 때 수행한 것과 동일한 단계를 따릅니다. 업로드 예제에서는 Blob 개체에 대해 upload를 호출했습니다. 다음 예제에서는 download를 호출하여 Blob을 로컬 파일에 저장하는 데 사용할 수 있는 **FileOutputStream**과 같은 스트림 개체로 Blob 콘텐츠를 전송합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // For each item in the container
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream(blob.getName()));
        }
    }

방법: Blob 삭제
---------------

Blob을 삭제하려면 Blob 참조를 가져온 후 **delete**를 호출합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg"
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob
    blob.delete();

방법: Blob 컨테이너 삭제
------------------------

마지막으로 Blob 컨테이너를 삭제하려면 Blob 컨테이너 참조를 가져온 후 delete를 호출합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container
    container.delete();

다음 단계
---------

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   MSDN 참조: [Azure에서 데이터 저장 및 액세스]
-   Azure 저장소 팀 블로그를 방문하십시오. http://blogs.msdn.com/b/windowsazurestorage/

[Azure SDK for Java]: http://www.windowsazure.com/en-us/develop/java/
[Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
