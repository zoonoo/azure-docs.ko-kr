---
title: 지원 되는 데이터 형식-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 미리 보기에서 지원 되는 데이터 형식에 대해 알아봅니다.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049948"
---
# <a name="supported-data-types"></a>지원되는 데이터 형식

다음 표에서는에서 지 원하는 데이터 형식을 보여 줍니다 Time Series Insights

| 데이터 형식 | Description | 예제 | Parquet의 속성 열 이름
|---|---|---|---|
| **bool** | `true` 또는 `false`의 두 가지 상태 중 하나인 데이터 형식입니다. | "isQuestionable": true | isQuestionable_bool
| **datetime** | 일반적으로 날짜와 시간으로 표현된 시간의 한 순간을 나타냅니다. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 형식으로 표현됩니다. Datetime 속성은 항상 UTC 형식으로 저장 됩니다. 형식이 올바르게 지정 된 경우 표준 시간대 오프셋은 적용 되며 UTC로 저장 된 값이 적용 됩니다. 환경 타임 스탬프 속성 및 datetime 오프셋에 대 한 자세한 내용은 [이](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) 섹션을 참조 하세요. | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668 Z" | eventProcessedLocalTime_datetime 
| **double** | 배정밀도 64 비트 숫자  | "값": 31.0482941 | value_double
| **long** | 부호 있는 64 비트 정수입니다.  | "값": 31 | value_long
| **string** | 텍스트 값은 유효한 u t f-8로 구성 되어야 합니다. |  "site": "DIM_MLGGG" | site_string
| **dynamic** | 배열 또는 속성 모음 (사전)으로 구성 된 복합 (기본이 아닌) 형식입니다. 현재는 기본 형식의 JSON 배열만 문자열 형식 하거나 TS ID 또는 타임 스탬프 속성를 포함 하지 않는 개체의 배열만 동적으로 저장 됩니다. 이 [문서](./concepts-json-flattening-escaping-rules.md) 를 참조 하 여 개체를 평면화 하는 방법을 이해 하 고 배열을 롤백하지 못할 수 있습니다. |  "values": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * TSI 환경은 강력한 형식입니다. 장치나 태그가 정수 계열 및 비정 수 데이터를 모두 보내면 장치 속성 값은 두 개의 분리 된 double 및 long 열에 저장 되며, API 호출을 수행 하 고 시계열 모델 변수 식을 정의할 때 [병합 () 함수](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) 를 사용 해야 합니다.

#### <a name="objects-and-arrays"></a>개체 및 배열

개체 및 배열과 같은 복합 형식을 이벤트 페이로드의 일부로 보낼 수 있습니다. 중첩 된 개체는 평면화 되 고 배열은 `dynamic` 환경 구성 및 JSON 셰이프에 따라 여러 이벤트를 생성 하기 위해 또는 평면화 됩니다. [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md) 에 대해 자세히 알아보려면 다음을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 이벤트를 저장 하는 방법을 이해 하려면 [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md) 을 참조 하세요. 

* 사용자 환경의 [처리량 제한 사항](concepts-streaming-throughput-limitations.md) 이해

* 스트리밍 데이터를 수집 하는 [이벤트 원본](concepts-streaming-ingestion-event-sources.md) 에 대해 알아봅니다.
