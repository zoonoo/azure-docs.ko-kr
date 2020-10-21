---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8f3a58d3a7470867ab23249bbd645289e010ad89
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92223110"
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

|속성  |기본값 | 설명 |
|---------|---------|---------|
|maxBatchSize|10|수신 루프 당 받은 최대 이벤트 수입니다.|
|prefetchCount|300|기본 `EventProcessorHost`에서 사용하는 기본 프리페치 횟수입니다. 허용되는 최솟값은 10초입니다.|
|batchCheckpointFrequency|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.|

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
