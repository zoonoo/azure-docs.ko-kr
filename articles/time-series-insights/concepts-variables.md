---
title: 모델 변수 - Azure Time Series Insights Gen2 | Microsoft Docs
description: 모델 변수
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: 01184a4eb2aac81bbcabcebf89ef10afeabddbe8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872968"
---
# <a name="time-series-model-variables"></a>시계열 모델 변수

이 문서에서는 이벤트에 대한 수식 및 계산 규칙을 지정하는 시계열 모델 변수에 대해 설명합니다.

각 변수는 *숫자*, *범주* 및 *집계* 의 세 가지 종류 중 하나일 수 있습니다.

* **숫자** 종류는 연속 숫자 값을 사용합니다.
* **범주** 종류는 정의된 불연속 값 집합을 사용합니다.
* **집계** 종류는 단일 종류의 여러 변수(모든 숫자 또는 모든 범주)를 결합합니다.

다음 표에서는 각 변수 종류와 관련된 속성을 보여줍니다.

[![시계열 모델 변수 표](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>숫자 변수

| 변수 속성 | Description |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려되는 행 수를 제한하는 선택적 조건 절입니다. |
| 변수 값 | 계산에 사용되는 원격 분석 값은 디바이스 또는 센서에서 발생하거나 시계열 식을 사용하여 변환됩니다. 숫자 종류 변수는 수신 데이터의 데이터 형식과 일치하도록 `Double` 또는 `Long`이어야 합니다.|
| 변수 보간 | 보간은 기존 데이터를 사용하여 신호를 다시 생성하는 방법을 지정합니다. 숫자 변수에는 *단계* 및 *선형* 보간 옵션을 사용할 수 있습니다. |
| 변수 집계 | [숫자 변수 종류에 대해 지원되는 집계 함수](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind)를 통해 계산을 수행합니다. |

변수는 다음 JSON 예제를 따릅니다.

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>범주 변수

| 변수 속성 | Description |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려되는 행 수를 제한하는 선택적 조건 절입니다. |
| 변수 값 | 디바이스 또는 센서에서 들어오는 계산에 사용되는 원격 분석 값입니다. 범주형 변수는 수신 데이터의 데이터 형식과 일치하도록 `Long` 또는 `String`이어야 합니다. |
| 변수 보간 | 보간은 기존 데이터를 사용하여 신호를 다시 생성하는 방법을 지정합니다. *단계* 보간 옵션은 범주 변수에 사용할 수 있습니다. |
| 변수 범주 | 범주는 디바이스 또는 센서에서 가져온 값 간의 매핑을 레이블로 만듭니다. |
| 변수 기본 범주 | 기본 범주는 "범주" 속성에서 매핑되지 않는 모든 값에 대한 것입니다. |

변수는 다음 JSON 예제를 따릅니다.

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>집계 변수

| 변수 속성 | Description |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려되는 행 수를 제한하는 선택적 조건 절입니다. |
| 변수 집계 | [집계 변수 종류에 대해 지원되는 집계 함수](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind)를 통해 계산을 수행합니다. |

변수는 다음 JSON 예제를 따릅니다.

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

변수는 시계열 모델의 형식 정의에 저장되며, API를 통해 인라인으로 제공하여 저장된 정의를 재정의하거나 보완할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [시계열 모델](./concepts-model-overview.md)에 대해 자세히 알아봅니다.

* [쿼리 API](./concepts-query-overview.md)를 사용하여 인라인으로 변수를 정의하는 방법에 대해 자세히 알아봅니다.
