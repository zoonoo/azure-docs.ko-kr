---
title: Azure Event Grid 배달 및 다시 시도
description: Azure Event Grid에서 이벤트를 배달하는 방법 및 배달되지 않은 메시지를 처리하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 924abaa1e5c12c4477bddf888541e7414b7bdbec
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324096"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 메시지 배달 및 다시 시도

이 문서에서는 배달이 승인되지 않는 경우 Azure Event Grid에서 이벤트를 처리하는 방법을 설명합니다.

Event Grid는 지속성이 있는 배달을 제공합니다. 각 메시지를 각 구독에 대해 최소 한 번 배달합니다. 이벤트는 각 구독에 등록된 엔드포인트로 즉시 전송됩니다. 엔드포인트가 이벤트 수신을 확인하지 않는 경우 Event Grid는 이벤트 전달을 재시도합니다.

## <a name="batched-event-delivery"></a>일괄 처리 된 이벤트 배달

Event Grid는 기본적으로 각 이벤트를 구독자에 게 개별적으로 보냅니다. 구독자는 단일 이벤트로 배열을 받습니다. 처리량이 높은 시나리오에서 향상 된 HTTP 성능을 제공 하기 위해 일괄 처리 이벤트에 대 한 Event Grid를 구성할 수 있습니다.

일괄 배달에는 두 가지 설정이 있습니다.

* **일괄 처리당 최대 이벤트** 수-일괄 처리당 제공 되 Event Grid 최대 이벤트 수입니다. 이 수는 절대로 초과할 수 없지만 게시 시 다른 이벤트를 사용할 수 없는 경우에는 더 작은 이벤트를 전달할 수 있습니다. 사용할 수 있는 이벤트의 수를 줄이면 일괄 처리를 만들기 위해 이벤트를 지연 하지 않습니다 Event Grid 합니다. 1에서 5000 사이 여야 합니다.
* **기본 배치 크기 (kb)** 입니다. 일괄 처리 크기의 대상 상한 (kb)입니다. 최대 이벤트와 마찬가지로 게시할 때 더 많은 이벤트를 사용할 수 없는 경우 일괄 처리 크기가 더 작아질 수 있습니다. 단일 이벤트가 기본 설정 된 크기 보다 큰 *경우* 일괄 처리가 기본 일괄 처리 크기 보다 클 수 있습니다. 예를 들어 기본 설정 된 크기가 4kb이 고 Event Grid 10gb 이벤트가 푸시되는 경우에도 10mbps 이벤트는 삭제 되지 않고 자체 일괄 처리로 배달 됩니다.

포털, CLI, PowerShell 또는 Sdk를 통해 이벤트 구독 별로 구성 된의 일괄 처리 배달

### <a name="azure-portal"></a>Azure Portal: 
![일괄 처리 배달 설정](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
이벤트 구독을 만들 때 다음 매개 변수를 사용 합니다. 

- **일괄 처리당 최대 이벤트** 수-일괄 처리의 최대 이벤트 수입니다. 1에서 5000 사이의 숫자 여야 합니다.
- **기본 설정-일괄 처리 크기** (kb)입니다. 1에서 1024 사이의 숫자 여야 합니다.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Event Grid에서 Azure CLI를 사용 하는 방법에 대 한 자세한 내용은 Azure CLI를 사용 하 여 [웹 끝점으로 저장소 이벤트 라우팅](../storage/blobs/storage-blob-event-quickstart.md)을 참조 하세요.

## <a name="retry-schedule-and-duration"></a>예약 및 기간 재시도

Event Grid는 메시지를 전달한 후 30 초 동안 응답을 기다립니다. 30 초 후 끝점이 응답 하지 않으면 메시지를 다시 시도 하도록 큐에 대기 합니다. Event Grid는 이벤트 배달에 대해 지수 백오프 재시도 정책을 사용합니다. Event Grid는 다음 일정에 따라 가장 적합 한 방식으로 배달을 다시 시도 합니다.

- 10초
- 30초
- 1분
- 5분
- 10분
- 30분
- 1시간
- 최대 24 시간 동안의 시간

끝점이 3 분 이내에 응답 하는 경우, Event Grid는 최상의 노력으로 재시도 큐에서 이벤트를 제거 하려고 시도 하지만 중복은 계속 받을 수 있습니다.

Event Grid은 모든 재시도 단계에 작은 임의를 추가 하 고, 끝점이 일관 되 게 비정상 상태 이거나, 오랜 기간 동안 중단 되거나, 과부하가 발생 한 경우 특정 재시도를 대해 선택적으로 수 있습니다.

결정적 동작의 경우 [구독 다시 시도 정책](manage-event-delivery.md)에서 이벤트 시간을 실시간 및 최대 배달 시도로 설정 합니다.

기본적으로 Event Grid는 24시간 이내에 배달되지 않는 모든 이벤트를 만료합니다. 이벤트 구독을 만들 때 [재시도 정책을 사용자 지정](manage-event-delivery.md)할 수 있습니다. 최대 배달 시도 횟수(기본값: 30)와 이벤트 TTL(Time to Live, 기본값: 1440분)을 제공합니다.

## <a name="delayed-delivery"></a>지연 된 배달

배달에 실패 하는 끝점은 Event Grid에서 배달 지연 및 해당 끝점에 대 한 이벤트 다시 시도를 시작 합니다. 예를 들어 끝점에 게시 된 처음 10 개의 이벤트가 실패할 경우 Event Grid는 끝점에 문제가 발생 한 것으로 가정 하 고 일부 경우에는 몇 시간 동안 모든 후속 재시도 *와 새* 배달을 지연 시킵니다.

지연 배달의 기능 목적은 비정상 끝점과 Event Grid 시스템을 보호 하는 것입니다. 백오프 및 비정상 끝점에 대 한 배달 지연 없이 Event Grid의 재시도 정책 및 볼륨 기능을 사용 하면 시스템에 쉽게 과부하가 발생할 수 있습니다.

## <a name="dead-letter-events"></a>배달 못한 편지 이벤트
Event Grid 특정 기간 내에 이벤트를 전달할 수 없거나 특정 횟수 만큼 이벤트를 배달 하려고 시도한 후에는 배달 되지 않은 이벤트를 저장소 계정으로 보낼 수 있습니다. 이 프로세스를 **배달 못 한 문자**라고 합니다. **다음 조건 중 하나가** 충족 되는 경우 배달 못한 편지를 Event Grid 합니다. 

- 이벤트는 ttl (time to live) 기간 내에 배달 되지 않습니다.
- 이벤트 전달 시도 횟수가 제한을 초과 했습니다.

조건 중 하나가 충족 되 면 이벤트가 삭제 되거나 배달 되지 않습니다.  기본적으로 Event Grid에서는 배달 못한 편지를 켜지 않습니다. 이 기능을 사용하려면 이벤트 구독을 만들 때 전송되지 않은 이벤트를 보유할 스토리지 계정을 지정해야 합니다. 이 스토리지 계정에서 이벤트를 끌어와 전송을 해결합니다.

Event Grid가 모든 재시도 시도 횟수를 시도한 경우 이벤트를 배달 못한 편지로 보냅니다. Event Grid가 400(잘못된 요청) 또는 413(요청 엔터티가 너무 큼) 응답 코드를 수신하는 경우, 즉시 이벤트를 배달 못한 편지 엔드포인트로 보냅니다. 이 응답 코드는 이벤트 전달이 실패하지 않음을 나타냅니다.

이벤트를 배달하기 위한 마지막 시도 및 배달 못한 편지 위치로 이벤트를 배달하는 경우, 그 사이에 5분의 지연이 발생합니다. 이 지연은 Blob Storage 작업 수를 줄이기 위함입니다. 배달 못한 편지 위치를 4시간 동안 사용할 수 없는 경우 이벤트가 삭제됩니다.

배달 못한 편지 위치를 설정하기 전에 컨테이너를 포함하는 스토리지 계정이 있어야 합니다. 이벤트 구독을 만들 때 이 컨테이너에 대한 엔드포인트를 제공합니다. 엔드포인트는 다음과 같은 형식입니다. `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

이벤트가 배달 못한 편지 위치로 전송된 경우 알림을 받을 수 있습니다. Event Grid를 사용하여 전송되지 않은 이벤트에 응답하려면 배달 못한 편지 Blob Storage에 대한 [이벤트 구독을 만듭니다](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 배달 못한 편지 Blob Storage가 전송되지 않은 이벤트를 수신할 때마다 Event Grid에서 처리기에 알립니다. 처리기는 전송되지 않은 이벤트를 조정하기 위해 수행할 작업으로 응답합니다. 배달 못한 편지 위치를 설정 하 고 정책을 다시 시도 하는 예는 [배달 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조 하세요.

## <a name="delivery-event-formats"></a>배달 이벤트 형식
이 섹션에서는 다양 한 배달 스키마 형식 (Event Grid 스키마, CloudEvents 1.0 스키마 및 사용자 지정 스키마)의 이벤트 및 배달 못 한 이벤트 예제를 제공 합니다. 이러한 형식에 대 한 자세한 내용은 [Event Grid 스키마](event-schema.md) 및 [클라우드 이벤트 1.0 스키마](cloud-event-schema.md) 문서를 참조 하세요. 

### <a name="event-grid-schema"></a>Event Grid 스키마

#### <a name="event"></a>이벤트 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>배달 못한 편지 이벤트

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1.0 스키마

#### <a name="event"></a>이벤트

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>배달 못한 편지 이벤트

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>사용자 지정 스키마

#### <a name="event"></a>이벤트

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>배달 못한 편지 이벤트
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>메시지 배달 상태

Event Grid는 HTTP 응답 코드를 사용하여 이벤트의 수신을 승인합니다. 

### <a name="success-codes"></a>성공 코드

Event Grid는 다음 HTTP **응답 코드만 성공한 배달으로 간주 합니다** . 다른 모든 상태 코드는 실패 한 배달으로 간주 되며 적절 하 게 다시 시도 하거나 deadlettered 됩니다. 성공적인 상태 코드를 수신 하면 배달이 완료 된 것으로 간주 Event Grid.

- 200 정상
- 201 생성됨
- 202 수락됨
- 203 신뢰할 수 없는 정보
- 204 콘텐츠 없음

### <a name="failure-codes"></a>실패 코드

위의 집합 (200-204)에 없는 다른 모든 코드는 실패로 간주 되며 다시 시도 됩니다. 일부는 아래에 설명 된 특정 다시 시도 정책을 포함 하 고 나머지는 표준 지 수 백오프 모델을 따릅니다. Event Grid 아키텍처의 병렬 특성으로 인해 재시도 동작은 비결 정적 이기 때문에 다시 시도 해야 합니다. 

| 상태 코드 | 다시 시도 동작 |
| ------------|----------------|
| 400 잘못된 요청 | 5 분 이상 후 다시 시도 (배달 못한 편지를 설치 하면 즉시 배달 못한 편지) |
| 401 권한 없음 | 5 분 이상 후 다시 시도 |
| 403 사용할 수 없음 | 5 분 이상 후 다시 시도 |
| 404 찾을 수 없음 | 5 분 이상 후 다시 시도 |
| 408 요청 시간 초과 | 2 분 이상 후 다시 시도 |
| 413 요청 엔터티가 너무 큼 | 10 초 이상 후 다시 시도 (배달 못한 편지를 설치 하면 즉시 배달 못한 편지) |
| 503 서비스를 사용할 수 없음 | 30 초 이상 후 다시 시도 |
| 나머지 | 10 초 이상 후 다시 시도 |


## <a name="next-steps"></a>다음 단계

* 이벤트 배달 상태를 보려면 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 이벤트 전달 옵션을 사용자 지정하려면 [전송 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.
