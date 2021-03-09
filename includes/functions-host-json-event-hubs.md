---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: fe80a71125d43220e408eab7b07aeedcafa0a526
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473827"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|속성  |기본값 | 설명 |
|---------|---------|---------|
|batchCheckpointFrequency|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.|
|eventProcessorOptions/maxBatchSize|10|수신 루프 당 받은 최대 이벤트 수입니다.|
|eventProcessorOptions/prefetchCount|300|기본 `EventProcessorHost`에서 사용하는 기본 프리페치 횟수입니다. 허용되는 최솟값은 10초입니다.|
|initialOffsetOptions/type<sup>1</sup>|fromStart|스토리지에 체크포인트가 없을 때 처리를 시작할 이벤트 스트림의 위치입니다. 옵션은 `fromStart`, `fromEnd` 또는 `fromEnqueuedTime`입니다. `fromEnd`는 함수 앱 실행이 시작된 후 큐에 추가된 새 이벤트를 처리합니다. 모든 파티션에 적용됩니다.  자세한 내용은 [EventProcessorOptions 설명서](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider)를 참조하세요.|
|initialOffsetOptions/enqueuedTimeUtc<sup>1</sup>|해당 없음| 처리를 시작할 스트림의 이벤트 큐에 넣은 시간을 지정합니다. `initialOffsetOptions/type`가 `fromEnqueuedTime`로 구성된 경우 이 설정은 필수입니다. [DateTime.Parse()](/dotnet/standard/base-types/parsing-datetime)에서 지원하는 모든 형식(예: `2020-10-26T20:31Z`)의 시간을 지원합니다. 명확하게 하기 위해 표준 시간대도 지정해야 합니다. 시간대가 지정되지 않은 경우 함수는 Azure에서 실행될 때 UTC인 함수 앱을 실행하는 머신의 로컬 시간대를 가정합니다. 자세한 내용은 [EventProcessorOptions 설명서](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider)를 참조하세요.|

<sup>1</sup> `intitialOffsetOptions`에 대한 지원은 [EventHubs v4.2.0](https://github.com/Azure/azure-functions-eventhubs-extension/releases/tag/v4.2.0)부터 시작됩니다.

> [!NOTE]
> Azure Functions 2.x 이상에서 host.json의 참조는 [Azure Functions에 대한 host.json 참조](../articles/azure-functions/functions-host-json.md)를 확인하세요.

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|maxBatchSize|64|수신 루프 당 받은 최대 이벤트 수입니다.|
|prefetchCount|해당 없음|기본 `EventProcessorHost`에서 사용할 기본 프리페치입니다.| 
|batchCheckpointFrequency|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.| 

> [!NOTE]
> Azure Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](../articles/azure-functions/functions-host-json-v1.md)를 확인하세요.
