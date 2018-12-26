---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133748"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|자산  |기본값 | 설명 |
|---------|---------|---------| 
|maxConcurrentCalls|16|메시지 펌프가 시작되어야 하는 콜백에 대한 최대 동시 호출 수입니다. 기본적으로 함수 런타임은 여러 개의 메시지를 동시에 처리합니다. 런타임이 큐 또는 토픽 메시지를 한 번에 하나만 처리하도록 하려면, `maxConcurrentCalls`를 1로 설정합니다. | 
|prefetchCount|해당 없음|기본 MessageReceiver에서 사용할 기본 PrefetchCount입니다.| 
|autoRenewTimeout|00:05:00|메시지 잠금이 자동으로 갱신되는 최대 기간입니다.| 
