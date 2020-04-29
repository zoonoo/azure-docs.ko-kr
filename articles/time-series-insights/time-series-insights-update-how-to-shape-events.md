---
title: 셰이프 이벤트-Azure Time Series Insights | Microsoft Docs
description: 모범 사례 및 Azure Time Insights 미리 보기에서 쿼리에 대 한 이벤트를 셰이핑 하는 방법에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77650926"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기를 사용하여 이벤트 셰이핑

이 문서에서는 Azure Time Series Insights에서 수집 하기 위해 JSON 페이로드를 셰이핑 하 고 미리 보기 쿼리의 효율성을 최대화 하는 모범 사례를 정의 합니다.

## <a name="best-practices"></a>모범 사례

Time Series Insights 미리 보기 환경으로 이벤트를 전송 하는 방법을 신중 하 게 고려 하는 것이 가장 좋습니다. 

일반적인 모범 사례는 다음과 같습니다.

* 네트워크를 통해 최대한 효율적으로 데이터를 보냅니다.
* 시나리오에 보다 적합한 집계에 도움이 되는 방식으로 데이터를 저장합니다.

최상의 쿼리 성능을 위해 다음과 같은 thumb 규칙을 따릅니다.

* 불필요한 속성을 보내지 않습니다. 사용량 별로 청구서 미리 보기를 Time Series Insights 합니다. 쿼리할 데이터만 저장 하 고 처리 하는 것이 가장 좋습니다.
* 정적 데이터에 인스턴스 필드를 사용합니다. 이 방법을 사용 하면 네트워크를 통해 정적 데이터를 전송 하지 않아도 됩니다. 시계열 모델의 구성 요소인 인스턴스 필드는 일반 공급 되는 Time Series Insights 서비스의 참조 데이터와 같은 방식으로 작동 합니다. 인스턴스 필드에 대 한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조 하세요.
* 둘 이상의 이벤트 간에 차원 속성을 공유합니다. 이 방법은 네트워크를 통해 보다 효율적으로 데이터를 보내는 데 도움이 됩니다.
* 여러 배열 중첩을 사용하지 않습니다. Time Series Insights 미리 보기는 개체를 포함 하는 중첩 배열의 최대 두 수준을 지원 합니다. Time Series Insights 미리 보기에서는 메시지의 배열을 속성 값 쌍이 있는 여러 이벤트로 평면화합니다.
* 대부분 또는 모든 이벤트에 몇 가지 측정값이 존재하는 경우 이러한 측정값을 동일한 개체 내에서 별도 속성으로 전송하는 것이 좋습니다. 이러한 이벤트를 개별적으로 전송 하면 이벤트 수가 감소 하 고 처리 해야 하는 이벤트 수가 줄어들기 때문에 쿼리 성능이 향상 될 수 있습니다.

## <a name="column-flattening"></a>열 평면화

수집 하는 동안 중첩 된 개체를 포함 하는 페이로드는 열 이름이 delineator의 단일 값이 되도록 평면화 됩니다.

* 예를 들어 다음과 같은 중첩 된 JSON이 있습니다.

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   는 평면화 `data_flow` 될 때가 됩니다.

> [!IMPORTANT]
> * Azure Time Series Insights 미리 보기는 경계`_`열에 밑줄 ()을 사용 합니다.
> * 대신 마침표 (`.`)를 사용 하는 일반 가용성의 차이점을 확인 합니다.

더 복잡 한 시나리오는 다음과 같습니다.

#### <a name="example-1"></a>예제 1:

다음 시나리오에는 측정값 (신호)을 전송 하는 두 개 이상의 장치 ( *흐름 요금*, *엔진 오일 압력*, *온도*및 *습도*)가 있습니다.

외부 배열에 공통 차원 값의 공유 섹션이 포함 된 단일 Azure IoT Hub 메시지가 전송 됩니다 (메시지에 포함 된 두 개의 장치 항목 참조).

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**내용**

* 예제 JSON에는 [시계열 인스턴스](./time-series-insights-update-tsm.md#time-series-model-instances) 데이터를 사용 하 여 메시지의 효율성을 높이는 외부 배열이 있습니다. 시계열 인스턴스가 장치 메타 데이터를 변경할 가능성이 없더라도 데이터 분석에 유용한 속성을 제공 하는 경우가 많습니다.

* JSON은 두 개 이상의 메시지 (각 장치에서 하나씩)를 시간에 따른 대역폭을 절약 하는 단일 페이로드로 결합 합니다.

* 각 장치에 대 한 개별 계열 데이터 요소가 단일 **계열** 특성으로 결합 되어 각 장치에 대해 지속적으로 업데이트를 스트리밍할 필요성이 줄어듭니다.

> [!TIP]
> 데이터를 전송 하 고 원격 분석을 보다 효율적으로 만드는 데 필요한 메시지 수를 줄이려면 공통 차원 값과 시계열 인스턴스 메타 데이터를 단일 JSON 페이로드로 일괄 처리 하는 것이 좋습니다.

#### <a name="time-series-instance"></a>Time Series Instance 

[시계열 인스턴스](./time-series-insights-update-tsm.md#time-series-model-instances) 를 사용 하 여 JSON을 보다 최적으로 만드는 방법에 대해 자세히 살펴보겠습니다. 

> [!NOTE]
> 다음의 [시계열 id](./time-series-insights-update-how-to-id.md) 는 *deviceIds*입니다.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Time Series Instance 미리 보기에서는 쿼리 시간 중에 테이블을 조인합니다(평면화 후). 테이블에는 **유형** 등의 추가 열이 포함됩니다.

| deviceId  | Type | L1 | L2 | timestamp | series_Flow Rate ft3/s | 석유 압력 프 series_Engine |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | 시뮬레이터 | 배터리 시스템 | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | 시뮬레이터 |   배터리 시스템 |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | 시뮬레이터 |    배터리 시스템 |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  위의 표는 시계열 [미리 보기 탐색기](./time-series-insights-update-explorer.md)의 쿼리 뷰를 나타냅니다.

**내용**

* 위의 예제에서 정적 속성은 네트워크를 통해 전송 되는 데이터를 최적화 하기 위해 Time Series Insights 미리 보기에 저장 됩니다.
* Time Series Insights 미리 보기 데이터는 인스턴스에 정의 된 시계열 ID를 통해 쿼리 시간에 조인 됩니다.
* 두 개의 중첩 계층이 사용 됩니다. 이 번호는 Time Series Insights 미리 보기에서 지 원하는 가장 많은 값입니다. 여러 중첩된 배열을 방지해야 합니다.
* 측정값이 거의 없으므로 동일한 개체 내의 개별 속성으로 전송됩니다. 이 예에서는 **Series_Flow 요금 프**에 **series_Engine 석유 압력 Psi**와 **series_Flow Rate ft3/s** 가 고유 열입니다.

>[!IMPORTANT]
> 인스턴스 필드는 원격 분석과 함께 저장 되지 않습니다. 이러한 데이터는 시계열 모델에서 메타 데이터와 함께 저장 됩니다.

#### <a name="example-2"></a>예제 2:

다음 JSON을 고려 하십시오.

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

위의 예제에서 평면화 `data["flow"]` 된 속성은 속성과의 `data_flow` 이름 충돌을 표시 합니다.

이 경우 *최신* 속성 값이 이전 속성 값을 덮어씁니다. 

> [!TIP]
> Time Series Insights 팀에 도움을 요청 하세요.

> [!WARNING] 
> * 평면화 나 다른 메커니즘으로 인해 동일한 (단일) 이벤트 페이로드에 중복 속성이 있는 경우 최신 > 속성 값이 저장 되 고 이전 값을 통해 기록 됩니다.
> * 결합 된 일련의 이벤트는 서로를 재정의 하지 않습니다.

## <a name="next-steps"></a>다음 단계

* 이러한 지침을 적용 하려면 [쿼리 구문 미리 보기 Azure Time Series Insights](./time-series-insights-query-data-csharp.md)를 참조 하세요. 데이터 액세스에 대 한 Time Series Insights [미리 보기 REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview) 의 쿼리 구문에 대해 자세히 알아보세요.

* JSON 모범 사례 [를 시계열 모델 방법](./time-series-insights-update-how-to-tsm.md)으로 결합 합니다.
