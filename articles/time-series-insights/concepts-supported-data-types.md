---
title: 지원되는 데이터 형식 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2에서 지원되는 데이터 형식에 대해 알아봅니다.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: eee8a3167c5c7b9be2ccf0c5f14895846bfd3d3a
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796240"
---
# <a name="supported-data-types"></a>지원되는 데이터 형식

다음 표에는 Azure Time Series Insights Gen2에서 지원하는 데이터 형식이 나열되어 있습니다.

| 데이터 형식 | Description | 예제 | [시계열 식 구문](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet의 속성 열 이름
|---|---|---|---|---|
| **bool** | `true` 또는 `false`의 두 가지 상태 중 하나인 데이터 형식입니다. | `"isQuestionable" : true` | `$event.isQuestionable.Bool` 또는 `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | 일반적으로 날짜와 시간으로 표현된 시간의 한 순간을 나타냅니다. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 형식으로 표현됩니다. 날짜/시간 속성은 항상 UTC 형식으로 저장됩니다. 형식이 올바르게 지정된 경우 표준 시간대 오프셋이 적용된 다음, UTC에 값이 저장됩니다. 환경 타임스탬프 속성 및 날짜/시간 오프셋에 대한 자세한 내용은 [이](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) 섹션을 참조하세요. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  "eventProcessedLocalTime"이 이벤트 원본 타임스탬프인 경우: `$event.$ts`. 다른 JSON 속성인 경우: `$event.eventProcessedLocalTime.DateTime` 또는 `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | 배정밀도 64 비트 숫자  | `"value": 31.0482941` | `$event.value.Double` 또는 `$event['value'].Double` |  `value_double`
| **long** | 부호 있는 64비트 정수  | `"value" : 31` | `$event.value.Long` 또는 `$event['value'].Long` |  `value_long`
| **string** | 텍스트 값은 유효한 UTF-8로 구성되어야 합니다. Null 및 빈 문자열은 동일하게 처리됩니다. |  `"site": "DIM_MLGGG"`| `$event.site.String` 또는 `$event['site'].String`| `site_string`
| **dynamic** | 배열 또는 속성 모음(사전)으로 구성된 복합(기본이 아닌) 형식입니다. 현재는 기본 문자열로 지정된 JSON 배열이나 TS ID 또는 타임스탬프 속성을 포함하지 않는 개체의 배열만 동적으로 저장됩니다. 개체를 평면화하고 배열을 언롤할 수 있는 방법을 이해하려면 이 [문서](./concepts-json-flattening-escaping-rules.md)를 참조하세요. 이 유형으로 저장된 페이로드 속성은 `Explore Events` 원시 이벤트를 보기 위해 Time Series Insights Explorer에서 선택하거나 클라이언트 쪽 구문 분석을 위한 [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) 쿼리 API를 통해서만 액세스할 수 있습니다. |  `"values": "[197, 194, 189, 188]"` | 시계열 식의 동적 형식 참조는 아직 지원되지 않습니다. | `values_dynamic`

> [!NOTE]
> 64비트 정수 값이 지원되지만 Azure Time Series Insights Explorer에서 안전하게 표현할 수 있는 가장 큰 숫자는 JavaScript 제한으로 인해 9,007,199,254,740,991(2^53-1)입니다. 위의 데이터 모델에서 숫자를 사용하는 경우 [시계열 모델 변수](./concepts-variables.md#numeric-variables)를 만들고 값을 [변환](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions)하여 크기를 줄일 수 있습니다.

> [!NOTE]
> **문자열** 형식은 null을 허용하지 않습니다.
>
> * 빈 문자열( **''** )의 값을 **NULL** 과 비교하는 [시계열 쿼리](/rest/api/time-series-insights/reference-query-apis)로 표현된 [TSX(시계열 식)](/rest/api/time-series-insights/reference-time-series-expression-syntax)는 동일한 방식으로 작동합니다. `$event.siteid.String = NULL`은 `$event.siteid.String = ''`과 동일합니다.
> * 원래 이벤트에 빈 문자열이 포함된 경우에도 API에서 **NULL** 값을 반환할 수 있습니다.
> * 비교 또는 평가를 수행하기 위해 **문자열** 열의 **NULL** 값에 대한 종속성을 사용하지 말고 빈 문자열과 동일한 방식으로 처리합니다.

## <a name="sending-mixed-data-types"></a>혼합 데이터 형식 보내기

Azure Time Series Insights Gen2 환경은 강력한 형식입니다. 디바이스 또는 태그가 디바이스 속성에 대해 서로 다른 유형의 데이터를 전송하는 경우 값은 두 개의 분리된 열에 저장되며 API 호출에서 시계열 모델 변수 식을 정의할 때 [coalesce() 함수](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)를 사용해야 합니다.

Azure Time Series Insights 탐색기는 동일한 디바이스 속성의 개별 열을 자동으로 병합하는 방법을 제공합니다. 아래 예제에서 센서는 Long 또는 Double일 수 있는 `PresentValue` 속성을 보냅니다. 데이터 유형의 상관없이 `PresentValue` 속성의 모든 저장된 값에 대해 쿼리하려면 `PresentValue (Double | Long)`를 선택합니다. 그러면 열이 병합됩니다.

[![탐색기 자동 병합](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>개체 및 배열

이벤트 페이로드의 일부로 개체 및 배열과 같은 복잡한 유형을 보낼 수 있습니다. 중첩된 개체는 평면화되고 배열은 `dynamic`으로 저장되거나 평면화되어 환경 구성 및 JSON 셰이프에 따라 여러 이벤트를 생성합니다. [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md)에 대해 자세히 알아보려면 다음을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 이벤트를 저장하는 방법을 알아보려면 [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md) 을 참조하세요.

* 사용자 환경의 [처리량 제한 사항](./concepts-streaming-ingress-throughput-limits.md) 이해

* 스트리밍 데이터를 수집하는 [이벤트 원본](concepts-streaming-ingestion-event-sources.md)에 대해 알아봅니다.
