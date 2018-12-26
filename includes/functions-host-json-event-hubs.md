---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134145"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|자산  |기본값 | 설명 |
|---------|---------|---------| 
|maxBatchSize|64|수신 루프 당 받은 최대 이벤트 수입니다.|
|prefetchCount|해당 없음|기본 EventProcessorHost에서 사용할 기본 PrefetchCount입니다.| 
|batchCheckpointFrequency|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.| 
