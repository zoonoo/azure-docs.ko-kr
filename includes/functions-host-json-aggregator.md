---
title: 포함 파일
description: 포함 파일
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167658"
---
[Application Insights에 대한 메트릭을 계산](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator)할 때 집계되는 함수 호출 수를 지정합니다. 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|속성 |기본값  | Description |
|---------|---------|---------| 
|batchSize|1000|집계할 최대 요청 수입니다.| 
|flushTimeout|00:00:30|집계할 최대 기간입니다.| 

함수 호출은 두 개의 한도 중 첫 번째 한에 도달할 때 집계됩니다.
