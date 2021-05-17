---
title: 수집 개요 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2로 데이터를 수집하는 방법에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: b688bbf454b3df9f6ae368c66a62657a5522d720
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505453"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Azure Time Series Insights Gen2 데이터 수집 개요

Azure Time Series Insights Gen2 환경에는 스트리밍 시계열 데이터를 수집, 처리 및 저장하는 *수집 엔진* 이 포함되어 있습니다. 데이터가 이벤트 원본에 도착하면 Azure Time Series Insights Gen2는 거의 실시간으로 데이터를 사용하고 저장합니다.

[![수집 개요](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>수집 항목

다음 문서에서는 따라야 할 모범 사례를 포함하여 데이터 처리에 대해 자세히 설명합니다.

* [이벤트 원본](./concepts-streaming-ingestion-event-sources.md) 및 이벤트 원본 타임스탬프 선택에 대한 지침을 읽습니다.

* 지원되는 [데이터 형식](./concepts-supported-data-types.md) 검토

* 수집 엔진이 JSON 속성에 [규칙](./concepts-json-flattening-escaping-rules.md) 세트를 적용하여 스토리지 계정 열을 만드는 방법을 파악합니다.

* 환경 [처리량 제한 사항](./concepts-streaming-ingress-throughput-limits.md)을 검토하여 규모 요구 사항을 계획합니다.

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights Gen2 환경의 [이벤트 원본](./concepts-streaming-ingestion-event-sources.md)에 대해 자세히 알아보려면 계속 진행하세요.
