---
title: 수집 개요-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2으로 데이터를 수집 하는 방법에 대해 알아봅니다.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.custom: seodec18
ms.openlocfilehash: 57c23ba4acdbde1a5dfac39d89a09dfcef6b25a1
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460903"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Azure Time Series Insights Gen2 데이터 수집 개요

Azure Time Series Insights Gen2 환경에는 스트리밍 시계열 데이터를 수집, 처리 및 저장 하기 위한 수집 *엔진이* 포함 되어 있습니다. 데이터가 이벤트 원본에 도착 하면 Azure Time Series Insights Gen2는 거의 실시간으로 데이터를 사용 하 고 저장 합니다.

[![수집 개요](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>수집 항목

다음 문서에서는 모범 사례를 포함 하 여 데이터 처리를 자세히 다룹니다.

* [이벤트 원본 및 이벤트](./concepts-streaming-ingestion-event-sources.md) 원본 타임 스탬프 선택에 대 한 지침을 읽습니다.

* 지원 되는 [데이터 형식](./concepts-supported-data-types.md) 검토

* 수집 엔진이 JSON 속성에 [규칙](./concepts-json-flattening-escaping-rules.md) 집합을 적용 하 여 저장소 계정 열을 만드는 방법에 대해 알아봅니다.

* 규모 요구에 대 한 계획을 수립 하려면 환경 [처리량 제한](./concepts-streaming-ingress-throughput-limits.md) 사항을 검토 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights Gen2 환경의 [이벤트 원본](./concepts-streaming-ingestion-event-sources.md) 에 대 한 자세한 내용은을 계속 진행 하세요.
