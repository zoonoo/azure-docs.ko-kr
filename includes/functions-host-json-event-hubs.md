---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81791650"
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
            }
        }
    }
}  
```

|속성  |기본값 | Description |
|---------|---------|---------|
|maxBatchSize|10|수신 루프 당 받은 최대 이벤트 수입니다.|
|prefetchCount|300|내부에서 사용 하는 기본 프리페치 수 `EventProcessorHost` 입니다.|
|batchCheckpointFrequency|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.|

> [!NOTE]
> Azure Functions 2.x 이상에서 host.js에 대 한 참조는 [Azure Functions에 대 한 참조host.js](../articles/azure-functions/functions-host-json.md)를 참조 하세요.

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

|속성  |기본값 | Description |
|---------|---------|---------| 
|maxBatchSize|64|수신 루프 당 받은 최대 이벤트 수입니다.|
|prefetchCount|해당 없음|내부에서 사용 되는 기본 사전 인출입니다 `EventProcessorHost` .| 
|batchCheckpointFrequency|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.| 

> [!NOTE]
> Azure Functions 1.x의에서 host.js에 대 한 참조는 [Azure Functions 1.x에 대 한host.js참조](../articles/azure-functions/functions-host-json-v1.md)를 참조 하세요.

