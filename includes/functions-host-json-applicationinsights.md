---
title: 파일 포함
description: 포함 파일
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a925076dfccd30c73febb2aadc8692667ea01525
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279473"
---
[Application Insights에서 샘플링 기능](../articles/azure-functions/functions-monitoring.md#configure-sampling)을 제어합니다.

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------| 
|isEnabled|true|샘플링을 사용 여부를 설정합니다.| 
|maxTelemetryItemsPerSecond|5|샘플링이 시작되는 임계값입니다.| 
