---
title: 파일 포함
description: 포함 파일
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: cd6ea6d4967e024ddf88fb9572d5efae8b7a7815
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495342"
---
### <a name="property-limits"></a>속성 제한

Azure Time Series Insights 속성 제한은 Gen1에서 800의 최대 캡에서 1000로 증가 했습니다. 제공 된 이벤트 속성에는 해당 JSON, CSV 및 [Azure Time Series Insights Gen2 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)내에서 볼 수 있는 차트 열이 있습니다.

| SKU | 최대 속성 |
| --- | --- |
| Gen2 (L1) | 1000 속성 (열) |
| Gen1 (S1) | 600 속성 (열) |
| Gen1 (S2) | 800 속성 (열) |

### <a name="event-sources"></a>이벤트 원본

인스턴스당 최대 2 개의 이벤트 원본이 지원 됩니다.

* [이벤트 허브 원본을 추가](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)하는 방법에 대해 알아봅니다.
* [IoT hub 원본을](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)구성 합니다.

기본적으로 Gen2 환경은 **환경 당 초당**최대 1mb의 [수신 속도를 지원](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-throughput-limitations) 합니다. 고객은 필요한 경우 최대 **16mb/s** 처리량까지 환경을 확장할 수 있습니다. 또한 **0.5 m b/s**의 파티션당 제한이 있습니다.

### <a name="api-limits"></a>API 제한

Azure Time Series Insights Gen2에 대 한 REST API 제한은 [REST API 참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)에 지정 되어 있습니다.
