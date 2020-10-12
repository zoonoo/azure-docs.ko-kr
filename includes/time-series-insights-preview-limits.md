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
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287419"
---
### <a name="property-limits"></a>속성 제한

Azure Time Series Insights 속성 제한은 Gen1에서 800의 최대 캡에서 1000로 증가 했습니다. 제공 된 이벤트 속성에는 해당 JSON, CSV 및 [Azure Time Series Insights Gen2 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)내에서 볼 수 있는 차트 열이 있습니다.

| SKU | 최대 속성 |
| --- | --- |
| Gen2 (L1) | 1000 속성 (열) |
| Gen1 (S1) | 600 속성 (열) |
| Gen1 (S2) | 800 속성 (열) |

### <a name="streaming-ingestion"></a>스트리밍 수집

* 환경 마다 최대 두 개의 [이벤트 소스가](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) 있습니다.

* 이벤트 원본에 대 한 모범 사례 및 일반적인 지침은 [여기](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) 에서 찾을 수 있습니다.

* 기본적으로 Azure Time Series Insights Gen2는 **Azure Time Series Insights Gen2 환경 당 최대 1mbps (초당 메가바이트)** 의 속도로 들어오는 데이터를 수집할 수 있습니다. [허브 파티션](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)마다 추가 제한이 있습니다. Azure Portal를 통해 지원 티켓을 제출 하 여 최대 8 MBps의 요금을 제공할 수 있습니다. 자세히 알아보려면 [스트리밍 수집 처리량 제한](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)을 참조 하세요.

### <a name="api-limits"></a>API 제한

Azure Time Series Insights Gen2에 대 한 REST API 제한은 [REST API 참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)에 지정 되어 있습니다.
