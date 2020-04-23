---
title: Azure 스택 허브에서 검사점 저장소로 Blob 저장소를 사용 하 여 (미리 보기)
description: 이 문서에서는 Azure 스택 허브의 이벤트 허브(미리 보기)에서 Blob 저장소를 검사점 저장소로 사용하는 방법을 설명합니다.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 2938099383c32eac493e4b4bb620f03c76ca5c44
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023662"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Blob 저장소를 검사점 저장소로 사용 - Azure 스택 허브의 이벤트 허브(미리 보기)
Azure Blob Storage를 Azure에서 일반적으로 사용할 수 있는 버전보다 다른 버전의 저장소 Blob SDK를 지원하는 환경에서 검사점 저장소로 사용하는 경우 코드를 사용하여 저장소 서비스 API 버전을 해당 환경에서 지원하는 특정 버전으로 변경해야 합니다. 예를 들어 Azure 스택 [허브 버전 2002에서 이벤트 허브를](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)실행 하는 경우 저장소 서비스에 대 한 사용 가능한 가장 높은 버전은 버전 2017-11-09입니다. 이 경우 코드를 사용하여 저장소 서비스 API 버전을 2017-11-09로 지정해야 합니다. 특정 저장소 API 버전을 대상으로 지정하는 방법에 대한 예제는 GitHub에서 다음 샘플을 참조하십시오. 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [자바](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [자바 스크립트](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) 또는 [타이프 스크립트](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- 파이썬 - [동기,](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) [비동기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Azure 스택 허브의 이벤트 허브는 현재 [미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 중이며 무료입니다. 

Azure Stack Hub가 지원하는 버전을 대상으로 하지 않고 Blob Storage를 검사점 저장소로 사용하는 이벤트 허브 수신기를 실행하면 다음과 같은 오류 메시지가 나타납니다.

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>파이썬의 샘플 오류 메시지
파이썬의 `azure.core.exceptions.HttpResponseError` 경우 의 오류 `on_error(partition_context, error)` 처리기의 `EventHubConsumerClient.receive()`오류 처리기에 오류가 전달됩니다. 그러나 메서드는 `receive()` 예외를 발생 하지 않습니다. `print(error)`다음 예외 정보를 인쇄합니다.

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

로거는 다음과 같은 두 가지 경고를 기록합니다.

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>다음 단계

분할 및 검사점에 대한 다음 문서 보기: [응용 프로그램의 여러 인스턴스에서 파티션 로드 균형](event-processor-balance-partition-load.md) 조정
