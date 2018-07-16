---
title: Azure 빠른 시작 - Java Storage SDK V10을 사용하여 개체 저장소에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 저장소에서 컨테이너를 만들고 파일을 업로드하고 개체를 나열하고 Java Storage SDK를 사용하여 다운로드합니다.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: a789269e73e1817f6a45e1e5948dbfaa21efd283
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704473"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-java-sdk-v10-preview"></a>빠른 시작: Java SDK V10(미리 보기)을 사용하여 Blob 업로드, 다운로드 및 나열

이 빠른 시작에서 새 Java Storage SDK를 사용하여 Azure Blob Storage의 컨테이너에 블록 Blob을 업로드하고, 다운로드하고, 나열하는 방법을 알아봅니다. 새 Java SDK는 RxJava를 통해 사후 프로그래밍 모델을 사용하여 비동기 연산을 제공합니다. [여기에서](https://github.com/ReactiveX/RxJava) RxJava에 대해 자세히 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* 명령줄에서 작동할 수 있게 [Maven](http://maven.apache.org/download.cgi) 또는 선호하는 모든 Java IDE 설치 및 구성
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 설치 및 구성

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드

이 빠른 시작 가이드에서 사용되는 [샘플 응용 프로그램](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart)은 기본적인 콘솔 응용 프로그램입니다. 

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다.

프로젝트가 가져오기를 완료하면 **Quickstart.java**(**src/main/java/quickstart**에 있음)를 엽니다.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성
이 솔루션에서는 샘플을 실행하는 컴퓨터에 로컬인 환경 변수에 저장소 계정 이름과 키를 안전하게 저장해야 합니다. 운영 체제에 따라 아래 예제 중 하나를 따라 환경 변수를 만듭니다.

### <a name="for-linux"></a>Linux의 경우

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="for-windows"></a>Windows의 경우

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>샘플 실행

이 샘플에서는 기본 디렉터리(Windows 사용자의 경우 AppData\Local\Temp)에 테스트 파일을 만든 다음, Blob Storage에 테스트 파일을 업로드하는 명령을 입력하라는 메시지가 나타나고 컨테이너의 Blob을 나열하고 업로드된 파일을 새 이름으로 다운로드하여 이전 파일과 새 파일을 비교할 수 있습니다. 

명령줄에서 Maven을 사용하여 샘플을 실행하려는 경우 셸을 열고 복제한 디렉터리 내의 **storage-blobs-java-v10-quickstart**로 이동합니다. 그런 후 `mvn compile exec:java`를 입력합니다.

다음은 Windows에서 응용 프로그램을 실행하는 경우의 출력 예제입니다.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

샘플을 컨트롤할 수 있으므로 코드를 실행하게 하려면 명령을 입력합니다. 입력은 대/소문자를 구분한다는 것을 알아야 합니다.

[Azure Storage 탐색기](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 저장소 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 

파일을 확인한 후에 **E** 및 Enter 키를 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도를 파악했으므로 **Quickstart.java** 파일을 열고 코드를 확인합니다. 

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해할 수 있도록 샘플 코드를 연습해 보겠습니다.

### <a name="get-references-to-the-storage-objects"></a>저장소 개체에 대한 참조 가져오기

가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* 저장소 계정을 가리키는 StorageURL 개체의 인스턴스를 만듭니다.

    [**StorageURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) 개체는 저장소 계정의 표현이며 새 파이프라인을 생성할 수 있습니다. 파이프라인([HTTP 파이프라인](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline))은 권한 부여, 로깅 및 메커니즘 다시 시도를 사용하여 요청 및 응답을 조작하는 데 사용되는 정책의 집합입니다. 파이프라인을 사용하여 Blob 컨테이너에서 작업을 실행하는 데 필요한 [**ContainerURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview) 인스턴스를 만들도록 허용하는 [**ServiceURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview) 개체의 인스턴스를 만들 수 있습니다.

* 액세스하는 컨테이너를 나타내는 ContainerURL 개체의 인스턴스를 만듭니다. 컨테이너는 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 blob을 구성하는 데 사용됩니다.

    **ContainerURL**은 컨테이너 서비스에 대한 액세스 지점을 제공합니다. **ContainerURL**을 사용하여 Blob을 만드는 데 필요한 [**BlobURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview) 개체의 인스턴스를 만들 수 있습니다.

* 관심이 있는 특정 Blob을 가리키는 [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview) 개체의 인스턴스를 만듭니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

### <a name="create-a-container"></a>컨테이너 만들기 

이 섹션에서는 ContainerURL의 인스턴스를 만들고 이를 사용하여 새 컨테이너를 만듭니다. 이 샘플의 컨테이너를 **빠른 시작**으로 지칭합니다. 

이 예제에서는 샘플이 실행될 때마다 새 컨테이너를 만들려고 하기 때문에 [containerURL.create](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview)를 사용합니다. 또는 코드에서 만들 필요가 없도록 컨테이너를 미리 만들 수도 있습니다.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 blob이 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다. 

Blob에 파일을 업로드하려면 대상 컨테이너에 blob에 대한 참조를 가져옵니다. Blob 참조가 있으면 BlockBlobURL 인스턴스의 [Upload](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview) -a.k.a PutBlob 및 [StageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_) -a.k.a PutBLock- 같은 하위 수준 API 또는 [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview) 메서드 같은 [TransferManager](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview) 클래스에 제공되는 고급 수준 API를 사용하여 파일을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 만들고, Blob이 이미 있는 경우 덮어씁니다.

샘플 코드는 업로드 및 다운로드에 사용할 로컬 파일을 만듭니다. **sourceFile**로 업로드될 파일 및 Blob의 URL을 **Blob**에 저장합니다. 다음 예제에서는 **빠른 시작**이라는 컨테이너에 이 파일을 업로드합니다.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

블록 Blob은 모든 유형의 텍스트 또는 이진 파일이 될 수 있습니다. 페이지 blob은 IaaS VM을 백업하는 데 사용되는 VHD 파일에 주로 사용됩니다. Blob 추가는 엔드에 데이터를 추가하기 위함이며 로깅에 자주 사용됩니다. Blob Storage에 저장된 대부분의 개체는 블록 Blob입니다.

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[containerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview)를 사용하여 컨테이너의 개체 목록을 가져올 수 있습니다. 컨테이너에 나열할 것이 더 있는 경우 이 메서드는 연속 표식(다음 표식)과 함께 한 번에 최대 5000개의 개체를 반환합니다. 이를 수행하려면 이전의 listBlobsFlatSegment 응답에 다음 표식이 있는 한 반복해서 호출하는 도우미 함수를 만듭니다.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Blob 다운로드

[blobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview)를 사용하여 Blob을 로컬 디스크에 다운로드합니다.

다음 코드는 이전 섹션에서 업로드된 blob을 다운로드하고, blob 이름에 "_DOWNLOADED" 접미사를 추가하여 로컬 디스크에서 두 파일을 모두 볼 수 있도록 합니다. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에 업로드된 Blob이 더 이상 필요하지 않은 경우 [containerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview)을 사용하여 전체 컨테이너를 삭제해도 됩니다. 이 메서드는 컨테이너의 파일도 삭제합니다.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 Java를 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. 

> [!div class="nextstepaction"]
> [Java 소스 코드에 대한 Storage SDK V10](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [API 참조](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-preview)
> [RxJava에 대해 자세히 알아보기](https://github.com/ReactiveX/RxJava)