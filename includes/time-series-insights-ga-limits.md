---
title: 포함 파일
description: 포함 파일
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263349"
---
다음은 일반 공급의 주요 한도를 요약한 것입니다.

### <a name="sku-ingress-rates-and-capacities"></a>SKU 의 환율 및 용량

S1 및 SKU 는 새로운 타임시리즈 인사이트 환경을 구성할 때 유연성을 제공합니다. SKU 용량은 저장된 이벤트 또는 바이트 수(중 먼저 제공되는 이벤트 또는 바이트)를 기준으로 일일 침투 속도를 나타냅니다. 침투는 *분당*측정되며 **제한은** 토큰 버킷 알고리즘을 사용하여 적용됩니다. 인그레스는 1-KB 블록으로 측정됩니다. 예를 들어 0.8KB 실제 이벤트는 하나의 이벤트로 측정되고 2.6KB 이벤트는 세 개의 이벤트로 계산됩니다.

| S1 SKU 용량 | 침투 율 | 최대 저장 용량
| --- | --- | --- |
| 1 | 하루에 1GB(100만 이벤트) | 매달 30GB(3천만 이벤트) |
| 10 | 하루에 10GB(1,000만 개의 이벤트) | 매달 300GB(3억 이벤트) |

| S2 SKU 용량 | 침투 율 | 최대 저장 용량
| --- | --- | --- |
| 1 | 하루에 10GB(1,000만 개의 이벤트) | 매달 300GB(3억 이벤트) |
| 10 | 하루에 100GB(1억 이벤트) | 매달 3TB(30억 이벤트) |

> [!NOTE]
> 용량은 연속해서 크기가 조정되므로 용량 2의 S1 SKU는 일일 2GB(2백만) 이벤트 수신 속도 및 매달 60GB(6천만 이벤트)를 지원합니다.

S2 SKU 환경은 매월 훨씬 더 많은 이벤트를 지원하고 훨씬 더 높은 침투 용량을 갖습니다.

| SKU  | 월별 이벤트 수  | 분당 이벤트 수 | 분당 이벤트 크기  |
|---------|---------|---------|---------|---------|
| S1     |   3천만   |  720    |  720KB   |
 |S2     |   3억   | 7,200   | 7,200KB  |

### <a name="property-limits"></a>속성 제한

GA 속성 제한은 선택한 SKU 환경에 따라 다릅니다. 제공된 이벤트 속성에는 [타임시리즈 인사이트 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)내에서 볼 수 있는 해당 JSON, CSV 및 차트 열이 있습니다.

| SKU | 최대 속성 |
| --- | --- |
| S1 | 600개의 속성(열) |
| S2 | 800개 속성(열) |

### <a name="event-sources"></a>이벤트 원본

인스턴스당 최대 2개의 이벤트 소스가 지원됩니다. 

* [이벤트 허브 소스를 추가하는](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)방법에 대해 알아봅니다.
* [IoT 허브 소스를 구성합니다.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)

### <a name="api-limits"></a>API 한도

TIME Series Insights 일반 가용성에 대한 REST API 제한은 [REST API 참조 설명서에](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)지정되어 있습니다.