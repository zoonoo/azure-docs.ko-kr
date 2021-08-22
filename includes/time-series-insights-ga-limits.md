---
title: 포함 파일
description: 포함 파일
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: f2f5ef1ff77ccbae4170b84c325f97f2cb7dc390
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122261159"
---
다음은 Azure Time Series Insights Gen1의 주요 제한을 요약한 것입니다.

### <a name="sku-ingress-rates-and-capacities"></a>SKU 수신 속도 및 용량

S1 및 S2 SKU 수신 속도 및 용량은 새 Azure Time Series Insights 환경을 구성할 때 유연성을 제공합니다. SKU 용량은 이벤트의 수 또는 저장된 바이트 수 중 먼저 도래하는 것을 기준으로 일일 수신 속도를 나타냅니다. 수신은 *분당* 측정되며, **제한** 은 토큰 버킷 알고리즘을 사용하여 적용됩니다. 수신은 1KB 블록 단위로 측정됩니다. 예를 들어 0.8KB 이벤트는 하나의 이벤트로 측정되고 2.6KB 이벤트는 세 개의 이벤트로 계산됩니다.

| S1 SKU 용량 | 수신 속도 | 최대 스토리지 용량
| --- | --- | --- |
| 1 | 매일 1GB(1백만 이벤트) | 30GB(3천만 개 이벤트) |
| 10 | 매일 10GB(1천만 이벤트) | 300GB(3억 개 이벤트) |

| S2 SKU 용량 | 수신 속도 | 최대 스토리지 용량
| --- | --- | --- |
| 1 | 매일 10GB(1천만 이벤트) | 300GB(3억 개 이벤트) |
| 10 | 매일 100GB(1억 이벤트) | 3TB(30억 개 이벤트) |

> [!NOTE]
> 용량은 연속해서 크기가 조정되므로 용량 2의 S1 SKU는 일일 2GB(2백만) 이벤트 수신 속도 및 매달 60GB(6천만 이벤트)를 지원합니다.

S2 SKU 환경은 매월 훨씬 더 많은 이벤트를 지원하고 더 높은 수신 용량을 제공합니다.

| SKU  | 월별 이벤트 수  | 분당 이벤트 수 | 분당 이벤트 크기  |
|---------|---------|---------|---------|---------|
| S1     |   3천만   |  720    |  720KB   |
 |S2     |   3억   | 7,200   | 7,200KB  |

### <a name="property-limits"></a>속성 제한

Gen1 속성 제한은 선택한 SKU 환경에 따라 달라집니다. 제공된 이벤트 속성에는 [Azure Time Series Insights 탐색기](../articles/time-series-insights/time-series-quickstart.md) 내에서 볼 수 있는 해당 JSON, CSV 및 차트 열이 있습니다.

| SKU | 최대 속성 |
| --- | --- |
| S1 | 600개 속성(열) |
| S2 | 800개 속성(열) |

### <a name="event-sources"></a>이벤트 원본

인스턴스당 최대 2개의 이벤트 원본이 지원됩니다.

* [이벤트 허브 원본을 추가](../articles/time-series-insights/how-to-ingest-data-event-hub.md)하는 방법을 알아봅니다.
* [IoT 허브 원본](../articles/time-series-insights/how-to-ingest-data-iot-hub.md)을 구성합니다.

### <a name="api-limits"></a>API 제한

Azure Time Series Insights Gen1에 대한 REST API 제한은 [REST API 참조 설명서](/rest/api/time-series-insights/gen1-reference-data-api#current-limits)에 지정되어 있습니다.
