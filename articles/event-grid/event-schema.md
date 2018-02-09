---
title: "Azure Event Grid 이벤트 스키마"
description: "Azure Event Grid를 사용하여 이벤트에 제공되는 속성을 설명합니다."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: babanisa
ms.openlocfilehash: 2b0039c7b90ef6f003641e096521f84885171c26
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid 이벤트 스키마

이 문서에서는 모든 이벤트에 존재하는 속성과 스키마를 설명합니다. 이벤트는 5개의 필수 문자열 속성 집합과 필수 데이터 개체로 구성됩니다. 속성은 모든 게시자에서 모든 이벤트에 공통입니다. 데이터 개체에는 각 게시자에만 적용되는 속성이 포함됩니다. 시스템 항목에 대한 이러한 속성은 Azure Storage 또는 Event Hubs와 같은 리소스 공급자에 따라 다릅니다.

이벤트는 여러 이벤트 개체를 포함할 수 있는 배열의 Azure Event Grid에 전송됩니다. 단일 이벤트가 하나뿐이면 배열의 길이는 1입니다. 배열의 총 크기는 최대 1MB일 수 있습니다. 배열의 각 이벤트는 64KB로 제한됩니다.

[이벤트 스키마 저장소](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)에서 Event Grid 이벤트에 대한 JSON 스키마 및 각 Azure 게시자의 데이터 페이로드를 찾을 수 있습니다.

## <a name="event-schema"></a>이벤트 스키마

다음 예제는 모든 이벤트 게시자가 사용하는 속성을 보여줍니다.

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

예를 들어 Azure Blob 저장소 이벤트용으로 게시된 스키마는 다음과 같습니다.

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
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```
 
## <a name="event-properties"></a>이벤트 속성

모든 이벤트에는 다음과 같은 동일한 최상위 수준 데이터가 포함됩니다.

| 자산 | 형식 | 설명 |
| -------- | ---- | ----------- |
| 토픽 | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid가 이 값을 제공합니다. |
| subject | string | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 특정 리소스 공급자에 대한 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid가 이 값을 제공합니다. |

데이터 개체의 속성에 대해 자세히 알아보려면 이벤트 원본을 참조하십시오.

* [Azure 구독(관리 작업)](event-schema-subscriptions.md)
* [Blob storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [리소스 그룹(관리 작업)](event-schema-resource-groups.md)

사용자 지정 항목의 경우 이벤트 게시자가 데이터 개체를 결정합니다. 최상위 수준 데이터에는 표준 리소스 정의 이벤트와 동일한 필드가 있어야 합니다. 사용자 지정 항목에 이벤트를 게시할 때 라우팅 및 필터링을 지원하도록 이벤트의 주체를 모델링하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
