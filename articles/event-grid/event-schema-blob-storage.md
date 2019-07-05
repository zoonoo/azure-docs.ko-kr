---
title: Azure Event Grid Blob Storage 이벤트 스키마
description: Azure Event Grid를 사용하여 Blob Storage 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445777"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid Blob Storage에 대한 이벤트 스키마

이 문서에서는 Blob Storage 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

샘플 스크립트 및 자습서 목록은 [저장소 이벤트 원본](event-sources.md#storage)을 참조하세요.

## <a name="list-of-events-for-blob-rest-apis"></a>Blob REST Api에 대 한 이벤트 목록

이러한 이벤트는 클라이언트를 대체 만들거나 Blob REST Api를 호출 하 여 blob을 삭제 하는 경우에 트리거됩니다.

 |이벤트 이름 |설명|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Blob를 만들거나 대체 하는 경우에 트리거됩니다. <br>클라이언트를 사용 하는 경우이 이벤트는 발생 하는 예는 `PutBlob`, `PutBlockList`, 또는 `CopyBlob` Blob REST API에서 사용할 수 있는 작업입니다.   |
 |**Microsoft.Storage.BlobDeleted** |Blob을 삭제할 때 트리거됩니다. <br>클라이언트를 호출할 때이 이벤트는 발생 하는 구체적으로 `DeleteBlob` Blob REST API에서 사용할 수 있는 작업입니다. |

> [!NOTE]
> 확인 하려는 경우를 **Microsoft.Storage.BlobCreated** 블록 Blob을 완전히 커밋될 때에 이벤트가 트리거되면에 대 한 이벤트를 필터링 합니다 `CopyBlob`, `PutBlob`, 및 `PutBlockList` REST API를 호출 합니다. 이러한 API 호출 트리거를 **Microsoft.Storage.BlobCreated** 이벤트 데이터를 완벽 하 게 블록 Blob에 커밋된 후에 합니다. 필터를 만드는 방법에 알아보려면 참조 [Event Grid에 대 한 이벤트를 필터링](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)합니다.

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake 저장소 Gen 2 REST Api에 대 한 이벤트의 목록

이러한 이벤트는 저장소 계정에 대해 계층적 네임 스페이스를 활성화 하 고 클라이언트에서 Azure Data Lake 저장소 Gen2 REST Api를 호출할 경우에 트리거됩니다.

> [!NOTE]
> 이러한 이벤트는 공개 미리 보기로 제공 하 고만 사용할 수는 **미국 서 부 2** 하 고 **미국 중서부** 지역입니다.

 |이벤트 이름|설명|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Blob를 만들거나 대체 하는 경우에 트리거됩니다. <br>클라이언트를 사용 하는 경우이 이벤트는 발생 하는 예는 `CreateFile` 및 `FlushWithClose` Azure Data Lake 저장소 Gen2 REST API에서 사용할 수 있는 작업입니다. |
 |**Microsoft.Storage.BlobDeleted** |Blob을 삭제할 때 트리거됩니다. <br>특히이 이벤트는 경우에 트리거됩니다 클라이언트 호출을 `DeleteFile` Azure Data Lake 저장소 Gen2 REST API에서 사용할 수 있는 작업입니다. |
 |**Microsoft.Storage.BlobRenamed**|Blob 이름이 바뀔 때 트리거됩니다. <br>클라이언트를 사용 하는 경우이 이벤트는 발생 하는 구체적으로 `RenameFile` Azure Data Lake 저장소 Gen2 REST API에서 사용할 수 있는 작업입니다.|
 |**Microsoft.Storage.DirectoryCreated**|디렉터리를 만들 때 트리거됩니다. <br>클라이언트를 사용 하는 경우이 이벤트는 발생 하는 구체적으로 `CreateDirectory` Azure Data Lake 저장소 Gen2 REST API에서 사용할 수 있는 작업입니다.|
 |**Microsoft.Storage.DirectoryRenamed**|디렉터리 이름이 바뀔 때 트리거됩니다. <br>클라이언트를 사용 하는 경우이 이벤트는 발생 하는 구체적으로 `RenameDirectory` Azure Data Lake 저장소 Gen2 REST API에서 사용할 수 있는 작업입니다.|
 |**Microsoft.Storage.DirectoryDeleted**|디렉터리가 삭제 되는 경우에 트리거됩니다. <br>클라이언트를 사용 하는 경우이 이벤트는 발생 하는 구체적으로 `DeleteDirectory` Azure Data Lake 저장소 Gen2 REST API에서 사용할 수 있는 작업입니다.|

> [!NOTE]
> 확인 하려는 경우는 **Microsoft.Storage.BlobCreated** 블록 Blob을 완전히 커밋될 때에 이벤트가 트리거되면에 대 한 이벤트를 필터링 합니다 `FlushWithClose` REST API 호출 합니다. 이 API는 트리거를 호출 합니다 **Microsoft.Storage.BlobCreated** 이벤트 데이터를 완벽 하 게 블록 Blob에 커밋된 후에 합니다. 필터를 만드는 방법에 알아보려면 참조 [Event Grid에 대 한 이벤트를 필터링](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)합니다.

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>이벤트 응답의 콘텐츠

이벤트가 트리거될 때 Event Grid 서비스 구독 끝점으로 해당 이벤트에 대 한 데이터를 보냅니다.

이 섹션에서는 각 blob 저장소 이벤트에 대 한 표시는 해당 데이터의 예가 포함 되어 있습니다.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated 이벤트 (Data Lake 저장소 Gen2)

데이터를 제외 하면 앞의 예제와 유사한 경우 blob storage 계정에는 계층 구조 네임 스페이스를 변경 합니다.

* 합니다 `dataVersion` 키 값으로 설정 되어 `2`입니다.

* 합니다 `data.api` 문자열에 키가 설정 `CreateFile` 또는 `FlushWithClose`합니다.

* `contentOffset` 키가 데이터 집합에 포함 합니다.

> [!NOTE]
> 응용 프로그램을 사용 하는 경우는 `PutBlockList` 계정 데이터에 새 blob을 업로드 하는 작업 이러한 변경 내용을 포함 하지 않습니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted 이벤트 (Data Lake 저장소 Gen2)

데이터를 제외 하면 앞의 예제와 유사한 경우 blob storage 계정에는 계층 구조 네임 스페이스를 변경 합니다.

* 합니다 `dataVersion` 키 값으로 설정 되어 `2`입니다.

* 합니다 `data.api` 문자열에 키가 설정 `DeleteFile`합니다.

* 합니다 `url` 경로 포함 하는 키 `dfs.core.windows.net`합니다.

> [!NOTE]
> 응용 프로그램을 사용 하는 경우는 `DeleteBlob` 데이터 계정에서 blob을 삭제 하는 작업이 이러한 변경 내용을 포함 하지 않습니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| topic | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| data | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| api | 문자열 | 이벤트를 트리거하는 작업입니다. |
| clientRequestId | 문자열 | 저장소 API 작업에 대 한 클라이언트에서 제공 된 요청 id입니다. 이 id는 로그에서 "클라이언트 요청 id" 필드를 사용 하 여 Azure Storage 진단 로그에 상관 관계를 사용할 수 있으며 "x-ms-클라이언트 요청 id" 헤더를 사용 하 여 클라이언트 요청에 제공할 수 있습니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| requestId | 문자열 | 저장소 API 작업에 대한 서비스에서 생성된 요청 ID입니다. 로그의 "request-id-header" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, 'x-ms-request-id' 헤더에서 API 호출을 시작하여 반환됩니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| eTag | 문자열 | 조건부로 작업을 수행하는 데 사용할 수 있는 값입니다. |
| contentType | 문자열 | Blob에 대해 지정된 콘텐츠 형식입니다. |
| contentLength | 정수 | Blob의 크기(바이트)입니다. |
| blobType | 문자열 | Blob의 형식입니다. 유효한 값은 "BlockBlob" 또는 "PageBlob"입니다. |
| contentOffset | number | 이벤트 트리거 응용 프로그램 파일에 쓰기를 완료 하는 되는 지점에서 수행 하는 쓰기 작업의 바이트 오프셋입니다. <br>계층 구조 네임 스페이스에 있는 blob 저장소 계정에서 트리거되는 이벤트에만 나타납니다.|
| destinationUrl |문자열 | 작업이 완료 된 후 존재 하는 파일의 url입니다. 예를 들어, 파일의 이름을 바꾸면는 `destinationUrl` 속성 새 파일 이름에 url을 포함 합니다. <br>계층 구조 네임 스페이스에 있는 blob 저장소 계정에서 트리거되는 이벤트에만 나타납니다.|
| sourceUrl |문자열 | 작업 전에 존재 하는 파일의 url입니다. 예를 들어, 파일의 이름을 바꾸면는 `sourceUrl` 이름 바꾸기 작업 전에 원래 파일 이름이의 url을 포함 합니다. <br>계층 구조 네임 스페이스에 있는 blob 저장소 계정에서 트리거되는 이벤트에만 나타납니다. |
| url | 문자열 | Blob에 대한 경로입니다. <br>클라이언트가 Blob REST API를 사용 하 여 경우 url이이 구조를 갖습니다:  *\<저장소 계정 이름\>.blob.core.windows.net/\<컨테이너 이름\>/\<파일 이름 \>* . <br>클라이언트는 Data Lake 저장소 REST API를 사용 하 여 경우 url이이 구조를 갖습니다:  *\<저장소 계정 이름\>.dfs.core.windows.net/\<파일 시스템 이름\> / \<파일 이름\>* 합니다.
|
| recursive| 문자열| `True` 모든 자식 디렉터리에 대 한 작업을 수행 하려면 그렇지 않으면 `False`합니다. <br>계층 구조 네임 스페이스에 있는 blob 저장소 계정에서 트리거되는 이벤트에만 나타납니다. |
| sequencer | 문자열 | 특정 Blob 이름에 대한 이벤트의 논리적 순서를 나타내는 불투명 문자열 값입니다.  사용자는 표준 문자열 비교를 사용하여 동일한 Blob 이름에 대한 두 이벤트의 상대적 순서를 이해할 수 있습니다. |
| storageDiagnostics | object | 경우에 따라 Azure Storage 서비스에 의해 포함되는 진단 데이터입니다. 포함될 경우, 이벤트 소비자는 무시해야 합니다. |

|자산|Type|설명|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* Blob Storage 이벤트를 사용하는 방법에 대한 소개는 [Blob Storage 이벤트 라우팅 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)를 참조하세요. 
