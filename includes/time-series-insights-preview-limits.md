---
title: 포함 파일
description: 포함 파일
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98109268"
---
### <a name="property-limits"></a>속성 제한

Azure Time Series Insights 속성 제한은 웜 스토리지의 경우 1000으로 증가되었으며 콜드 스토리지의 경우 속성 제한이 없습니다. 제공된 이벤트 속성에는 [Azure Time Series Insights Gen2 Explorer](../articles/time-series-insights/quickstart-explore-tsi.md) 내에서 볼 수 있는 해당 JSON, CSV 및 차트 열이 있습니다.

| SKU | 최대 속성 |
| --- | --- |
| Gen2(L1) | 웜 스토리지의 경우 1000개의 속성(열) 및 콜드 스토리지의 경우 무제한|
| Gen1(S1) | 600개 속성(열) |
| Gen1(S2) | 800개 속성(열) |

### <a name="streaming-ingestion"></a>스트리밍 수집

* 환경당 최대 두 개의 [이벤트 원본](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md)이 있습니다.

* 이벤트 원본에 대한 모범 사례 및 일반 지침은 [여기](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)에서 찾을 수 있습니다.

* 기본적으로 Azure Time Series Insights Gen2는 **Azure Time Series Insights Gen2 환경당 최대 1MBps(초당 메가바이트)의 속도** 로 들어오는 데이터를 수집할 수 있습니다. [허브 파티션](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)마다 추가 제한이 있습니다. Azure Portal을 통해 지원 티켓을 제출하면 최대 2MBps의 속도를 제공할 수 있습니다. 자세히 알아보려면 [스트리밍 수집 처리량 제한](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)을 참조하세요.

### <a name="api-limits"></a>API 제한

Azure Time Series Insights Gen2에 대한 REST API 제한은 [REST API 참조 설명서](/rest/api/time-series-insights/preview#limits-1)에 지정되어 있습니다.