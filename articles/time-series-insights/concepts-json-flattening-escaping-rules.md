---
title: JSON 평면화 및 이스케이프 규칙 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 JSON 평면화, 이스케이프 및 배열 처리에 대해 알아봅니다.
author: esung22
ms.author: elsung
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 4816d506db5631398932ad602c9ac9e0c5e87b1c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797480"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON 평면화, 이스케이핑 및 배열 처리

Azure Time Series Insights Gen2 환경에서는 특정 명명 규칙 집합에 따라 웜 및 콜드 저장소의 열을 동적으로 만듭니다. 이벤트가 수집되면 규칙 집합이 JSON 페이로드 및 속성 이름에 적용됩니다. 여기에는 특정 특수 문자를 이스케이프하고 중첩 JSON 개체를 평면화하는 작업이 포함됩니다. JSON의 셰이프를 통해 이벤트를 저장하고 쿼리하는 방법에 어떻게 영향을 미치는지 이해하려면 이러한 규칙을 알고 있어야 합니다. 전체 규칙 목록은 아래 표를 참조하세요. 예제 A 및 B에서는 배열에서 여러 시계열을 효율적으로 일괄 처리할 수 있는 방법을 보여 줍니다.

> [!IMPORTANT]
>
> * [시계열 ID 속성](./how-to-select-tsid.md) 및/또는 이벤트 원본 [타임스탬프 속성](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)을 선택하기 전에 아래 규칙을 검토합니다. TS ID 또는 타임스탬프가 중첩된 개체 내에 있거나 아래에 하나 이상의 특수 문자가 있는 경우에는 수집 규칙이 적용된 *후* 에 제공하는 속성 이름이 열 이름과 일치하는지 확인하는 것이 중요합니다. 아래 예제 [B](concepts-json-flattening-escaping-rules.md#example-b)를 참조하세요.

| 규칙 | 예제 JSON | [시계열 식 구문](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet의 속성 열 이름
|---|---|---|---|
| Azure Time Series Insights Gen2 데이터 형식이 열 이름 끝에 "_\<dataType\>"로 추가됩니다. | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| 이벤트 원본 [타임스탬프 속성](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)은 스토리지에서 "timestamp"로 Azure Time Series Insights Gen2에 저장되고 값은 UTC로 저장됩니다. 솔루션 요구 사항에 맞게 이벤트 원본 타임스탬프 속성을 사용자 지정할 수 있지만 웜 및 콜드 스토리지의 열 이름은 "timestamp"입니다. 이벤트 원본 타임스탬프가 아닌 다른 datetime JSON 속성은 위의 규칙에 설명된 대로 열 이름에 "_datetime"으로 저장됩니다.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| 특수 문자를 포함하는 JSON 속성 이름입니다. [ \ 및 '는 [' 및 ']를 사용하여 이스케이프됩니다.  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| [' 및 '] 내에서 작은따옴표와 백슬래시를 추가로 이스케이프합니다. 작은따옴표는 \'로 작성되고 백슬래시는 \\\로 작성됩니다. | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| 중첩된 JSON 개체는 마침표를 사용하여 구분 기호로 평면화됩니다. 최대 10개 수준의 중첩이 지원됩니다. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`, `$event['series']['value'].Long` 또는 `$event.series['value'].Long` |  `series.value_long` |
| 기본 형식의 배열은 동적 형식으로 저장됩니다. |  ```"values": [154, 149, 147]``` | 동적 형식은 [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API를 통해서만 검색할 수 있습니다. | `values_dynamic` |
| 개체를 포함하는 배열에는 개체 내용에 따라 두 가지 동작이 있습니다. TS ID 또는 timestamp 속성이 배열의 개체 내에 있는 경우에는 초기 JSON 페이로드가 여러 이벤트를 생성하도록 배열이 언롤됩니다. 이렇게 하면 여러 이벤트를 하나의 JSON 구조로 일괄 처리할 수 있습니다. 배열의 피어인 최상위 속성은 각 unrolled 개체와 함께 저장됩니다. TS ID 및 타임스탬프가 배열 내에 *없으면* 동적 형식으로 전체 저장됩니다. | 아래 예제 [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b) 및 [C](concepts-json-flattening-escaping-rules.md#example-c) 참조
| 혼합된 요소가 포함된 배열은 평면화되지 않습니다. |  ```"values": ["foo", {"bar" : 149}, 147]``` | 동적 형식은 [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API를 통해서만 검색할 수 있습니다. | `values_dynamic` |
| 512 문자는 JSON 속성 이름 한도입니다. 이름이 512자를 초과하는 경우 512로 잘리고 '_<'해시 코드'>'가 추가됩니다. **참고** 이는 중첩된 개체 경로를 나타내는 평면화된 개체에서 연결된 속성 이름에도 적용됩니다. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>배열의 이중 동작 이해

개체의 배열은 데이터를 모델링하는 방법에 따라 전체적으로 저장되거나 여러 이벤트로 분할됩니다. 이렇게 하면 배열을 사용하여 이벤트를 일괄 처리하고 루트 개체 수준에서 정의된 원격 분석 속성을 반복하지 않을 수 있습니다. 일괄 처리는 전송되는 Event Hubs 또는 IoT Hub 메시지의 수를 줄일 수 있기 때문에 유용할 수 있습니다.

그러나 경우에 따라 개체를 포함하는 배열은 다른 값의 컨텍스트에서만 의미가 있습니다. 여러 이벤트를 만들면 데이터가 무의미하게 렌더링됩니다. 개체의 배열이 동적 형식으로 있는 그대로 저장되도록 하려면 아래의 데이터 모델링 지침을 따르고 [예제 C](concepts-json-flattening-escaping-rules.md#example-c)를 살펴봅니다.

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>개체 배열이 여러 이벤트를 생성하는지 확인하는 방법

하나 이상의 시계열 ID 속성이 배열의 개체 내에 중첩되어 *있거나* 이벤트 원본 타임스탬프 속성이 중첩된 경우 수집 엔진이 여러 이벤트를 만들도록 분할합니다. TS ID 및/또는 타임스탬프에 대해 제공한 속성 이름은 위의 평면화 규칙을 따라야 하므로 JSON의 셰이프를 표시합니다. 아래 예제를 참조하고 [시계열 ID 속성을 선택](./how-to-select-tsid.md)하는 방법에 대한 가이드를 확인합니다.

### <a name="example-a"></a>예 1

개체 루트의 시계열 ID 및 중첩된 타임스탬프\
**환경 시계열 ID:** `"id"`\
**이벤트 원본 타임스탬프:** `"values.time"`\
**JSON 페이로드:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Parquet 파일의 결과:** \
위의 구성 및 페이로드는 세 개의 열과 네 개의 이벤트를 생성합니다.

| timestamp  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>예 2

하나의 속성이 중첩된 복합 시계열 ID\
**환경 시계열 ID:** `"plantId"` 및 `"telemetry.tagId"`\
**이벤트 원본 타임스탬프:** `"timestamp"`\
**JSON 페이로드:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Parquet 파일의 결과:** \
위의 구성 및 페이로드는 네 개의 열과 여섯 개의 이벤트를 생성합니다.

| timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30.9918 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19.960796 |

### <a name="example-c"></a>예 3

시계열 ID 및 타임스탬프는 개체 루트에 있음\
**환경 시계열 ID:** `"id"`\
**이벤트 원본 타임스탬프:** `"timestamp"`\
**JSON 페이로드:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Parquet 파일의 결과:** \
위의 구성 및 페이로드는 세 개의 열과 하나의 이벤트를 생성합니다.

| timestamp  | id_string | datapoints_dynamic
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>다음 단계

* 사용자 환경의 [처리량 제한 사항](./concepts-streaming-ingress-throughput-limits.md) 이해
