---
title: "Azure Event Grid 이벤트 스키마"
description: "Azure Event Grid가 있는 이벤트에 제공되는 속성을 설명합니다."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/15/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 6736c6a60021b51db612f0a596086a9e988d7aef
ms.contentlocale: ko-kr
ms.lasthandoff: 08/17/2017

---

# <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

이 문서에서는 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트는 다섯 개의 필수 문자열 속성 집합과 필수 **데이터** 개체로 구성되어 있습니다. 속성은 모든 게시자에서 모든 이벤트에 공통입니다. **데이터** 개체는 각 게시자에 특정한 속성을 포함합니다. 시스템 토픽의 경우 이러한 속성은 Storage 또는 Event Hubs와 같은 리소스 공급자에 특정적입니다.

이벤트는 여러 이벤트 개체를 포함할 수 있는 배열의 Azure Event Grid에 전송됩니다. 단일 이벤트가 하나뿐이면 배열의 길이는 1입니다. 
 
## <a name="event-properties"></a>이벤트 속성

모든 이벤트에는 다음과 같은 최상위 수준 데이터가 포함됩니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| 토픽 | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. |
| subject | string | 이벤트 주체에 대한 경로가 정의된 게시자입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 특정 리소스 공급자에 대한 이벤트 데이터입니다. |

## <a name="available-event-sources"></a>사용할 수 있는 이벤트 원본

다음 이벤트 원본은 Event Grid를 통해 소비에 대한 이벤트를 게시합니다.

* 리소스 그룹(관리 작업)
* Azure 구독(관리 작업)
* Event Hubs
* 사용자 지정 토픽

## <a name="azure-subscriptions"></a>Azure 구독

Azure 구독에서 이제 VM이 생성되거나 저장소 계정이 삭제될 때와 같이 Azure Resource Manager에서 관리 이벤트를 내보낼 수 있습니다.

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

- **Microsoft.Resources.ResourceWriteSuccess**: 리소스 만들기 또는 업데이트 작업이 성공할 때 발생합니다.  
- **Microsoft.Resources.ResourceWriteFailure**: 리소스 만들기 또는 업데이트 작업이 실패할 때 발생합니다.  
- **Microsoft.Resources.ResourceWriteCancel**: 리소스 만들기 또는 업데이트 작업이 취소될 때 발생합니다.  
- **Microsoft.Resources.ResourceDeleteSuccess**: 리소스 삭제 작업이 성공할 때 발생합니다.  
- **Microsoft.Resources.ResourceDeleteFailure**: 리소스 삭제 작업이 실패할 때 발생합니다.  
- **Microsoft.Resources.ResourceDeleteCancel**: 리소스 삭제가 취소될 때 발생합니다. 템플릿 배포가 취소되면 발생합니다.

### <a name="example-event-schema"></a>예제 이벤트 스키마

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>리소스 그룹

Resource Manager에서 이제 VM이 생성되거나 저장소 계정이 삭제될 때와 같이 Azure Resource Manager에서 관리 이벤트를 내보낼 수 있습니다.

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

- **Microsoft.Resources.ResourceWriteSuccess**: 리소스 만들기 또는 업데이트 작업이 성공할 때 발생합니다.  
- **Microsoft.Resources.ResourceWriteFailure**: 리소스 만들기 또는 업데이트 작업이 실패할 때 발생합니다.  
- **Microsoft.Resources.ResourceWriteCancel**: 리소스 만들기 또는 업데이트 작업이 취소될 때 발생합니다.  
- **Microsoft.Resources.ResourceDeleteSuccess**: 리소스 삭제 작업이 성공할 때 발생합니다.  
- **Microsoft.Resources.ResourceDeleteFailure**: 리소스 삭제 작업이 실패할 때 발생합니다.  
- **Microsoft.Resources.ResourceDeleteCancel**: 리소스 삭제가 취소될 때 발생합니다. 템플릿 배포가 취소되면 발생합니다.

### <a name="example-event"></a>예제 이벤트

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>Event Hubs

Event Hubs 이벤트는 현재 파일이 캡처 기능을 사용하여 저장소에 자동으로 전송되는 경우에만 내보내집니다.

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

- **Microsoft.EventHub.CaptureFileCreated**: 캡처 파일이 생성될 때 발생합니다.

### <a name="example-event"></a>예제 이벤트

이 샘플 이벤트는 캡처가 파일을 저장하는 경우 발생하는 Event Hubs 이벤트의 스키마를 보여 줍니다. 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```



## <a name="azure-blob-storage"></a>Azure Blob Storage

Event Grid와의 통합에 등록한 비공개 미리 보기의 Azure Blob Storage입니다.

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

- **Microsoft.Storage.BlobCreated**: Blob이 생성될 때 발생합니다.
- **Microsoft.Storage.BlobDeleted**: Blob이 삭제될 때 발생합니다.

### <a name="example-event"></a>예제 이벤트

이 샘플 이벤트는 Blob이 생성될 때 발생하는 저장소 이벤트의 스키마를 보여 줍니다. 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```




## <a name="custom-topics"></a>사용자 지정 토픽

사용자 지정 이벤트의 데이터 페이로드는 사용자에 의해 정의되고 포맷이 올바른 모든 JSON이 될 수 있습니다. 최상위 수준 데이터는 표준 리소스 정의 이벤트와 동일한 필드를 포함해야 합니다. 사용자 지정 토픽에 이벤트를 게시할 때 라우팅 및 필터링을 지원하도록 이벤트의 주체를 모델링하는 것을 고려해야 합니다.

### <a name="example-event"></a>예제 이벤트

다음 예제에서는 사용자 지정 토픽에 대한 이벤트를 보여 줍니다.
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Event Grid 구독을 만드는 방법을 알아보려면 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.

