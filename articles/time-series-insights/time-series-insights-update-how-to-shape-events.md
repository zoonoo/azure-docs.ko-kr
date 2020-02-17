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
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: e814d9be4a0db2852bd9e21f3d3c1d54a45bd268
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368650"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기를 사용하여 이벤트 셰이핑

이 문서에서는 수집을 위해 JSON 파일의 모양을 만들고 Azure Time Series Insights 미리 보기 쿼리의 효율성을 최대화 하는 방법을 설명 합니다.

## <a name="best-practices"></a>모범 사례

Time Series Insights 미리 보기로 이벤트를 보내는 방법에 대해 생각해 보십시오. 즉, 다음을 수행해야 합니다.

* 네트워크를 통해 최대한 효율적으로 데이터를 보냅니다.
* 시나리오에 보다 적합한 집계에 도움이 되는 방식으로 데이터를 저장합니다.

최상의 쿼리 성능을 위해서는 다음을 수행 하십시오.

* 불필요한 속성을 보내지 않습니다. Time Series Insights 미리 보기에서는 사용량에 따라 요금을 청구합니다. 쿼리할 데이터를 저장하고 처리하는 것이 가장 좋습니다.
* 정적 데이터에 인스턴스 필드를 사용합니다. 이 방법은 네트워크를 통한 정적 데이터 전송을 방지하는 데 도움이 됩니다. 시계열 모델의 구성 요소인 인스턴스 필드는 일반 공급 되는 Time Series Insights 서비스의 참조 데이터와 같은 방식으로 작동 합니다. 인스턴스 필드에 대 한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조 하세요.
* 둘 이상의 이벤트 간에 차원 속성을 공유합니다. 이 방법은 네트워크를 통해 보다 효율적으로 데이터를 보내는 데 도움이 됩니다.
* 여러 배열 중첩을 사용하지 않습니다. Time Series Insights 미리 보기는 개체를 포함 하는 중첩 배열의 최대 두 수준을 지원 합니다. Time Series Insights 미리 보기에서는 메시지의 배열을 속성 값 쌍이 있는 여러 이벤트로 평면화합니다.
* 대부분 또는 모든 이벤트에 몇 가지 측정값이 존재하는 경우 이러한 측정값을 동일한 개체 내에서 별도 속성으로 전송하는 것이 좋습니다. 이러한 이벤트를 개별적으로 전송 하면 이벤트 수가 감소 하 고 처리 해야 하는 이벤트 수가 줄어들기 때문에 쿼리 성능이 향상 될 수 있습니다.

수집 하는 동안 중첩이 포함 된 페이로드가 평면화 되어 열 이름이 delineator 인 단일 값이 됩니다. Time Series Insights 미리 보기에서는 경계에 밑줄을 사용 합니다. 이는 기간을 사용 하는 제품의 GA 버전에서 변경 된 사항입니다. 미리 보기 중에는 아래 두 번째 예제에서 설명 하는 평면화에 주의 해야 합니다.

## <a name="examples"></a>예

다음 예제는 둘 이상의 디바이스가 측정값 또는 신호를 보내는 시나리오를 기반으로 합니다. 측정 또는 신호는 *흐름 요금*, *엔진 오일 압력*, *온도*및 *습도*가 될 수 있습니다.

예제에는 외부 배열에 공통 차원 값의 공유 섹션이 포함 된 단일 Azure IoT Hub 메시지가 있습니다. 외부 배열은 Time Series Instance 데이터를 사용하여 메시지의 효율성을 높입니다. 

시계열 인스턴스는 장치 메타 데이터를 포함 합니다. 이 메타 데이터는 모든 이벤트에 대해 변경 되지 않지만 데이터 분석에 대 한 유용한 속성을 제공 합니다. 네트워크를 통해 전송 되는 바이트를 절약 하 고 메시지의 효율성을 향상 시키려면 공통 차원 값을 일괄 처리 하 고 시계열 인스턴스 메타 데이터를 사용 하는 것이 좋습니다.

### <a name="example-1"></a>예제 1:

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

### <a name="time-series-instance"></a>Time Series Instance 

> [!NOTE]
> 시계열 ID는 *deviceId*입니다.

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

Time Series Instance 미리 보기에서는 쿼리 시간 중에 테이블을 조인합니다(평면화 후). 테이블에는 **유형** 등의 추가 열이 포함됩니다. 다음 예제에서는 원격 분석 데이터의 [모양을](./time-series-insights-send-events.md#supported-json-shapes) 지정할 수 있는 방법을 보여 줍니다.

| deviceId  | Type | L1 | L2 | timestamp | series_Flow Rate ft3/s | 석유 압력 프 series_Engine |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | 시뮬레이터 | 배터리 시스템 | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | 시뮬레이터 |   배터리 시스템 |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | 시뮬레이터 |    배터리 시스템 |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

앞의 예제에서 다음 사항에 유의하세요.

* 정적 속성은 네트워크를 통해 전송되는 데이터를 최적화하기 위해 Time Series Insights 미리 보기에 저장됩니다.
* Time Series Insights 미리 보기 데이터는 인스턴스에 정의 된 시계열 ID를 통해 쿼리 시간에 조인 됩니다.
* 두 개의 중첩 계층이 사용 됩니다. 이 번호는 Time Series Insights 미리 보기에서 지 원하는 가장 많은 값입니다. 여러 중첩된 배열을 방지해야 합니다.
* 측정값이 거의 없으므로 동일한 개체 내의 개별 속성으로 전송됩니다. 이 예에서는 **Series_Flow 요금 프**에 **series_Engine 석유 압력 Psi**와 **series_Flow Rate ft3/s** 가 고유 열입니다.

>[!IMPORTANT]
> 인스턴스 필드는 원격 분석과 함께 저장 되지 않습니다. 이러한 데이터는 시계열 모델에서 메타 데이터와 함께 저장 됩니다.
> 앞의 표는 쿼리 보기를 나타냅니다.

### <a name="example-2"></a>예제 2:

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
위의 예제에서 평면화 된 `data_flow` 속성은 `data_flow` 속성과 명명 충돌을 표시 합니다. 이 경우 *최신* 속성 값이 이전 속성 값을 덮어씁니다. 이 동작으로 인해 비즈니스 시나리오에 문제가 발생 하는 경우에는 TSI 팀에 문의 하세요.

> [!WARNING] 
> 평면화 나 다른 메커니즘으로 인해 동일한 이벤트 페이로드에 중복 속성이 있는 경우 최신 속성 값이 저장 overwritting 이전 값이 저장 됩니다.


## <a name="next-steps"></a>다음 단계

이러한 지침을 적용 하려면 [쿼리 구문 미리 보기 Azure Time Series Insights](./time-series-insights-query-data-csharp.md)를 참조 하세요. 데이터 액세스에 대 한 Time Series Insights 미리 보기 REST API의 쿼리 구문에 대해 자세히 알아보세요.
