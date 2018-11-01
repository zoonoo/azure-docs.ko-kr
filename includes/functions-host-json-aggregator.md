---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: da0cbab59d9c801419ac4b3704fee3275f337fd9
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251267"
---
[Application Insights에 대한 메트릭을 계산](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator)할 때 집계되는 함수 호출 수를 지정합니다. 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|자산 |기본값  | 설명 |
|---------|---------|---------| 
|batchSize|1000|집계할 최대 요청 수입니다.| 
|flushTimeout|00:00:30|집계할 최대 기간입니다.| 

함수 호출은 두 개의 한도 중 첫 번째 한에 도달할 때 집계됩니다.