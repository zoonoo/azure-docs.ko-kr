---
title: Azure Stack 허브 (미리 보기)에서 Blob Storage를 검사점 저장소로 사용
description: 이 문서에서는 Azure Stack 허브 (미리 보기)의 Event Hubs에서 Blob Storage을 검사점 저장소로 사용 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 0990941191827c66cd51d70216c75e106d0448fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322357"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Blob Storage를 검사점 저장소로 사용-Azure Stack 허브 (미리 보기)에서 Event Hubs
Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지 원하는 환경에서 검사점 저장소로 Azure Blob Storage을 사용 하는 경우, 코드를 사용 하 여 저장소 서비스 API 버전을 해당 환경에서 지 원하는 특정 버전으로 변경 해야 합니다. 예를 들어 [Azure Stack 허브 버전 2002에서 Event Hubs](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)를 실행 하는 경우 저장소 서비스에 사용할 수 있는 가장 높은 버전은 2017-11-09입니다. 이 경우에는 코드를 사용 하 여 저장소 서비스 API 버전을 2017-11-09로 대상으로 해야 합니다. 특정 Storage API 버전을 대상으로 지정 하는 방법에 대 한 예제는 GitHub의 다음 샘플을 참조 하세요. 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) 또는 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python- [동기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [비동기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Azure Stack Hub의 Event Hubs는 현재 [미리 보기로](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 제공 되며 무료입니다. 

Azure Stack 허브가 지 원하는 버전을 대상으로 하지 않고 Blob Storage를 검사점 저장소로 사용 하는 Event Hubs 수신자를 실행 하는 경우 다음 오류 메시지가 표시 됩니다.

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python의 샘플 오류 메시지
Python의 경우 오류는 `azure.core.exceptions.HttpResponseError` 의 오류 처리기에 전달 됩니다 `on_error(partition_context, error)` `EventHubConsumerClient.receive()` . 그러나 메서드는 `receive()` 예외를 발생 시 키 지 않습니다. `print(error)`는 다음과 같은 예외 정보를 인쇄 합니다.

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

로 거는 다음과 같은 두 가지 경고를 기록 합니다.

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>다음 단계

분할 및 검사점에 대 한 자세한 내용은 다음 문서를 참조 하세요. [응용 프로그램의 여러 인스턴스에서 파티션 로드 균형 조정](event-processor-balance-partition-load.md)
