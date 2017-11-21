---
title: "Azure 빠른 시작 - Node.js를 사용하여 Azure Blob Storage에서 개체 전송 | Microsoft Docs"
description: "Node.js를 사용하여 Azure Blob Storage에서 개체를 전송하는 방법을 간단히 알아봅니다."
services: storage
documentationcenter: storage
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: gwallace
ms.openlocfilehash: 9ea7f77d3bbe45de49c798fe3d51151e1a5a6658
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Node.js를 사용하여 Azure Blob Storage에서 개체 전송

이 빠른 시작에서 Node.js를 사용하여 Azure Blob Storage에서 컨테이너에 블록 blob을 업로드, 다운로드 및 나열하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* [Node.js](https://nodejs.org/en/)

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드

이 빠른 시작 가이드에서 사용되는 [샘플 응용 프로그램](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)은 기본적인 콘솔 응용 프로그램입니다. 

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. 응용 프로그램을 열려면 storage-blobs-node-quickstart 폴더를 찾아 그것을 열고 index.js를 두 번 클릭합니다.

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성

응용 프로그램에서 저장소 계정에 대한 연결 문자열을 제공해야 합니다. `index.js` 파일을 열고 `connectionString` 변수를 찾습니다. 값을 Azure Portal에서 저장한 값으로 연결 문자열의 전체 값과 바꿉니다. 저장소 연결 문자열은 다음과 유사하게 나타납니다.

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

응용 프로그램 디렉터리에서 `package.json` 파일에 나열된 필수 패키지를 설치하려면 `npm install`을 실행합니다.

```javascript
npm install
```

## <a name="run-the-sample"></a>샘플 실행

이 샘플은 내 문서에 테스트 파일을 만들고, Blob Storage에 업로드한 후, 컨테이너의 blob을 나열하고, 해당 파일을 새 이름으로 다운로드하여 이전 파일과 새 파일을 비교할 수 있도록 합니다.

`node index.js`을 입력하여 샘플을 실행합니다. 다음 출력은 Windows 시스템에서 옵니다.  Linux를 사용하는 경우 적절한 파일 경로를 가진 유사한 출력을 예상할 수 있습니다.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

계속하기 전에 MyDocuments에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다.

[Azure Storage 탐색기](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 저장소 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다.

파일을 확인한 후에 아무 키나 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 index.js 파일을 열고 코드를 확인합니다. 

## <a name="get-references-to-the-storage-objects"></a>저장소 개체에 대한 참조 가져오기

가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 `BlobService`에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* 저장소 계정의 Blob service를 가리키는 **[BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor)** 개체의 인스턴스를 만듭니다.

* 새 컨테이너를 만든 다음, 컨테이너에 대해 사용 권한을 설정하여 blob을 공용 blob으로 유지하고 URL을 통해서만 액세스할 수 있게 합니다. 컨테이너는 **quickstartcontainer-**로 시작합니다.

이 예제에서는 샘플이 실행될 때마다 새 컨테이너를 만들려고 하기 때문에 [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists)를 사용합니다. 응용 프로그램 전체에서 동일한 컨테이너를 사용하는 프로덕션 환경에서는 CreateIfNotExists를 한 번만 호출하는 것이 더 좋은 방법입니다. 또는 코드에서 만들 필요가 없도록 컨테이너를 미리 만들 수도 있습니다.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

## <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 Blob은 가장 일반적으로 사용됩니다. 텍스트 및 바이너리 데이터를 저장하는데 이상적이며, 그 때문에 이 빠른 시작에 사용됩니다.

파일을 BLOB에 업로드하려면 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) 메서드를 사용합니다. 이 작업은 Blob이 없는 경우 만들고, Blob이 이미 있는 경우 덮어씁니다.

샘플 코드는 업로드 및 다운로드에 사용할 로컬 파일을 만들고 해당 파일이 **localFileToUpload**에 **localPath** 및 blob의 이름으로 업로드될 수 있게 저장합니다. 다음 예제에서는 **quickstartcontainer-**로 시작하는 저장소에 이 파일을 업로드합니다.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Blob 저장소에서 사용할 수 있는 몇 가지 업로드 메서드가 있습니다. 예를 들어 메모리 스트림이 있는 경우 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) 대신 [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) 메서드를 사용할 수 있습니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

다음으로 응용 프로그램은 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented)를 사용하여 컨테이너의 파일 목록을 가져옵니다. 다음 코드는 blob 목록을 검색하고, 이 과정을 반복하여 발견된 Blob의 URI를 표시합니다. 명령 창에서 URI를 복사한 후 브라우저에 붙여 넣어 파일을 봅니다.

컨테이너에 있는 blob이 5,000개 이하인 경우 모든 blob 이름이 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented)의 단일 호출에서 검색됩니다. 컨테이너에 있는 blob이 5,000개보다 많을 경우 서비스는 모든 blob 이름이 검색될 때까지 5,000개씩 목록을 검색합니다. 이 API가 처음 호출되면 처음 5,000개 blob 이름과 연속 토큰이 반환됩니다. 두 번째로 호출될 때는 이 토큰을 제공해야 합니다. 그러면 서비스는 다음 blob 이름 집합을 검색합니다. 이와 같이 진행하다가 연속 토큰이 null이 되어 모든 blob 이름이 검색되었음을 나타내면 중단됩니다.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

## <a name="download-blobs"></a>Blob 다운로드

[getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile)을 사용하여 Blob을 로컬 디스크에 다운로드합니다.

다음 코드는 이전 섹션에서 업로드된 blob을 다운로드하고, blob 이름에 "_DOWNLOADED" 접미사를 추가하여 로컬 디스크에서 두 파일을 모두 볼 수 있도록 합니다. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 가이드에서는 업로드된 blob이 더 이상 필요하지 않으므로 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) 및 [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists)를 사용하여 전체 컨테이너를 삭제해도 됩니다. 만든 파일도 더 이상 필요하지 않으면 삭제합니다. 응용 프로그램을 종료하기 위해 Enter 키를 누르면 응용 프로그램에서 이것이 처리됩니다.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 Node.js를 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 계속해서 Blob Storage 방법을 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](storage-nodejs-how-to-use-blob-storage.md)

Storage 탐색기 및 Blob에 대한 자세한 내용은 [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
