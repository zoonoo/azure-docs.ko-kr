---
title: Azure Blob 저장소를 이벤트 그리드 원본으로
description: Azure Event Grid를 사용하여 Blob Storage 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: cfc6e4790b67137b423cc90d93874d4914f81251
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393384"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Azure Blob 저장소를 이벤트 그리드 원본으로

이 문서에서는 Blob Storage 이벤트에 대한 속성 및 스키마를 제공합니다.이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 또한 Azure Blob Storage를 이벤트 소스로 사용할 수 있는 빠른 시작 및 자습서 목록도 제공합니다.


>[!NOTE]
> 저장소 계정만 **StorageV2 (범용 v2)**, **BlockBlobStorage**및 **BlobStorage** 지원 이벤트 통합. **스토리지(범용 v1)** 는 Event Grid와의 통합을 지원하지 *않습니다*.

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

### <a name="list-of-events-for-blob-rest-apis"></a>Blob REST API의 이벤트 목록

이러한 이벤트는 클라이언트가 Blob REST API를 호출하여 Blob을 만들거나 바꾸거나 삭제할 때 트리거됩니다.

 |이벤트 이름 |Description|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Blob을 만들거나 대체할 때 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Blob `PutBlob` `PutBlockList`REST `CopyBlob` API에서 사용할 수 있는 " 또는 작업을 사용할 때 트리거됩니다.   |
 |**Microsoft.Storage.BlobDeleted** |Blob이 삭제될 때 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Blob `DeleteBlob` REST API에서 사용할 수 있는 작업을 호출할 때 트리거됩니다. |

> [!NOTE]
> **Microsoft.Storage.BlobCreated** 이벤트가 블록 Blob이 완전히 커밋된 경우에만 트리거되도록 하려면 `CopyBlob`에 `PutBlob`대한 이벤트를 필터링합니다. `PutBlockList` 이러한 API 호출은 데이터가 블록 Blob에 완전히 커밋된 후에만 **Microsoft.Storage.BlobCreated** 이벤트를 트리거합니다. 필터를 만드는 방법에 대해 알아보려면 [이벤트 그리드에 대한 이벤트 필터](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)를 참조하십시오.

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure 데이터 레이크 스토리지 세대 2 REST API에 대한 이벤트 목록

이러한 이벤트는 저장소 계정에서 계층적 네임스페이스를 사용하도록 설정하고 클라이언트가 Azure Data Lake Storage Gen2 REST API를 호출하는 경우 트리거됩니다. 자세한 내용은 Azure Data Lake Storage [Introduction to Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)Gen2를 참조하세요.

|이벤트 이름|Description|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Blob을 만들거나 대체할 때 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Azure `CreateFile` Data `FlushWithClose` Lake Storage Gen2 REST API에서 사용할 수 있는 및 작업을 사용할 때 트리거됩니다. |
|**Microsoft.Storage.BlobDeleted** |Blob이 삭제될 때 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Azure Data `DeleteFile` Lake Storage Gen2 REST API에서 사용할 수 있는 작업을 호출할 때도 트리거됩니다. |
|**마이크로소프트.스토리지.BlobRenamed**|Blob의 이름이 바뀌면 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Azure `RenameFile` Data Lake Storage Gen2 REST API에서 사용할 수 있는 작업을 사용할 때 트리거됩니다.|
|**마이크로소프트.스토리지.디렉토리 생성**|디렉터리를 만들 때 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Azure `CreateDirectory` Data Lake Storage Gen2 REST API에서 사용할 수 있는 작업을 사용할 때 트리거됩니다.|
|**마이크로소프트.스토리지.디렉토리 이름**|디렉터리 이름이 바뀌면 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Azure `RenameDirectory` Data Lake Storage Gen2 REST API에서 사용할 수 있는 작업을 사용할 때 트리거됩니다.|
|**마이크로소프트.스토리지.디렉토리 삭제됨**|디렉터리가 삭제될 때 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Azure `DeleteDirectory` Data Lake Storage Gen2 REST API에서 사용할 수 있는 작업을 사용할 때 트리거됩니다.|

> [!NOTE]
> **Microsoft.Storage.BlobCreated** 이벤트가 블록 Blob이 완전히 커밋된 경우에만 트리거되도록 하려면 `FlushWithClose` REST API 호출에 대한 이벤트를 필터링합니다. 이 API 호출은 데이터가 블록 Blob에 완전히 커밋된 후에만 **Microsoft.Storage.BlobCreated** 이벤트를 트리거합니다. 필터를 만드는 방법에 대해 알아보려면 [이벤트 그리드에 대한 이벤트 필터](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)를 참조하십시오.

<a id="example-event" />

### <a name="the-contents-of-an-event-response"></a>이벤트 응답의 내용

이벤트가 트리거되면 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 끝점으로 보냅니다.

이 섹션에는 각 Blob 저장소 이벤트에 대해 해당 데이터가 어떻게 보이는지에 대한 예제가 포함되어 있습니다.

### <a name="microsoftstorageblobcreated-event"></a>마이크로소프트.스토리지.Blob생성 이벤트

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.Blob생성 이벤트(데이터 레이크 스토리지 Gen2)

Blob 저장소 계정에 계층적 네임스페이스가 있는 경우 데이터는 이러한 변경 사항을 제외하고 이전 예제와 유사합니다.

* `dataVersion` 키는 `2`의 값으로 설정됩니다.

* `data.api` 키가 문자열 `CreateFile` 또는 `FlushWithClose`로 설정됩니다.

* `contentOffset` 키는 데이터 집합에 포함됩니다.

> [!NOTE]
> 응용 프로그램에서 `PutBlockList` 작업을 사용하여 계정에 새 Blob을 업로드하는 경우 데이터에 이러한 변경 내용이 포함되지 않습니다.

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

### <a name="microsoftstorageblobdeleted-event"></a>마이크로소프트.스토리지.Blob삭제 된 이벤트

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.Blob삭제 이벤트(데이터 레이크 스토리지 Gen2)

Blob 저장소 계정에 계층적 네임스페이스가 있는 경우 데이터는 이러한 변경 사항을 제외하고 이전 예제와 유사합니다.

* `dataVersion` 키는 `2`의 값으로 설정됩니다.

* `data.api` 키가 문자열로 `DeleteFile`설정됩니다.

* 키에는 `url` 경로가 `dfs.core.windows.net`포함되어 있습니다.

> [!NOTE]
> 응용 프로그램이 `DeleteBlob` 작업을 사용하여 계정에서 Blob을 삭제하는 경우 데이터에 이러한 변경 내용이 포함되지 않습니다.

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

### <a name="microsoftstorageblobrenamed-event"></a>마이크로소프트.스토리지.BlobRenamed 이벤트

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

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.Directory생성된 이벤트

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>마이크로소프트.스토리지.디렉터리이름이 지정된 이벤트

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.Directory삭제된 이벤트

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

### <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| api | 문자열 | 이벤트를 트리거하는 작업입니다. |
| clientRequestId | 문자열 | 저장소 API 작업에 대한 클라이언트 제공 요청 ID입니다. 이 ID는 로그의 "클라이언트-요청-id" 필드를 사용하여 Azure Storage 진단 로그와 상관 관계를 지정하는 데 사용할 수 있으며 "x-ms-client-request-id" 헤더를 사용하여 클라이언트 요청에 제공할 수 있습니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| requestId | 문자열 | 스토리지 API 작업에 대한 서비스에서 생성된 요청 ID입니다. 로그의 "request-id-header" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, 'x-ms-request-id' 헤더에서 API 호출을 시작하여 반환됩니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| eTag | 문자열 | 조건부로 작업을 수행하는 데 사용할 수 있는 값입니다. |
| contentType | 문자열 | Blob에 대해 지정된 콘텐츠 형식입니다. |
| contentLength | integer | Blob의 크기(바이트)입니다. |
| blobType | 문자열 | Blob의 형식입니다. 유효한 값은 "BlockBlob" 또는 "PageBlob"입니다. |
| contentOffset | number | 이벤트 트리거 응용 프로그램이 파일에 쓰기를 완료한 지점에서 수행된 쓰기 작업의 바이트별 오프셋입니다. <br>계층적 네임스페이스가 있는 Blob 저장소 계정에서 트리거된 이벤트에 대해서만 나타납니다.|
| 대상Url |문자열 | 작업이 완료된 후 존재할 파일의 URL입니다. 예를 들어 파일이름이 변경되면 속성에 `destinationUrl` 새 파일 이름의 URL이 포함됩니다. <br>계층적 네임스페이스가 있는 Blob 저장소 계정에서 트리거된 이벤트에 대해서만 나타납니다.|
| 소스Url |문자열 | 작업 전에 있는 파일의 URL입니다. 예를 들어 파일의 이름이 바뀌면 이름 바꾸기 전에 원래 파일 이름의 URL이 `sourceUrl` 포함됩니다. <br>계층적 네임스페이스가 있는 Blob 저장소 계정에서 트리거된 이벤트에 대해서만 나타납니다. |
| url | 문자열 | Blob에 대한 경로입니다. <br>클라이언트가 Blob REST API를 사용하는 경우 URL에는 * \<저장소-계정\>이름\<.blob.core.windows.net/\>/\<컨테이너 이름\>파일 이름입니다.* <br>클라이언트가 Data Lake Storage REST API를 사용하는 경우 URL에는 * \<저장소-계정\>\<이름 .dfs.core.windows.net/\>/\<파일 시스템\>이름 파일 이름입니다.* |
| recursive | 문자열 | `True`모든 자식 디렉토리에서 작업을 수행하기 위해; 그렇지 `False`않으면 . <br>계층적 네임스페이스가 있는 Blob 저장소 계정에서 트리거된 이벤트에 대해서만 나타납니다. |
| sequencer | 문자열 | 특정 Blob 이름에 대한 이벤트의 논리적 순서를 나타내는 불투명 문자열 값입니다.  사용자는 표준 문자열 비교를 사용하여 동일한 Blob 이름에 대한 두 이벤트의 상대적 순서를 이해할 수 있습니다. |
| storageDiagnostics | object | 경우에 따라 Azure Storage 서비스에 의해 포함되는 진단 데이터입니다. 포함될 경우, 이벤트 소비자는 무시해야 합니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목  |Description  |
|---------|---------|
| [빠른 시작: Azure CLI를 사용하여 Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI를 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [빠른 시작: PowerShell을 사용하여 Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure PowerShell을 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [빠른 시작: Azure Portal을 사용하여 Blob Storage 이벤트 만들기 및 라우팅](blob-event-quickstart-portal.md) | 포털를 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [Azure CLI: Blob Storage 계정에 대한 이벤트 구독](./scripts/event-grid-cli-blob.md) | Blob Storage 계정에 대한 이벤트를 구독하는 샘플 스크립트입니다. 이벤트를 WebHook로 전송합니다. |
| [PowerShell: Blob Storage 계정에 대한 이벤트 구독](./scripts/event-grid-powershell-blob.md) | Blob Storage 계정에 대한 이벤트를 구독하는 샘플 스크립트입니다. 이벤트를 WebHook로 전송합니다. |
| [Resource Manager 템플릿: Blob Storage 및 구독 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Azure Blob Storage 계정을 배포하고 해당 스토리지 계정에 대한 이벤트를 구독합니다. WebHook에 이벤트를 보냅니다. |
| [개요: Blob Storage 이벤트에 대응](../storage/blobs/storage-blob-event-overview.md) | Event Grid와 Blob Storage 통합의 개요입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* Blob Storage 이벤트를 사용하는 방법에 대한 소개는 [Blob Storage 이벤트 라우팅 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)를 참조하세요. 
