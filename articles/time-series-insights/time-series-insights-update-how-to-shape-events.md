---
title: 셰이프 이벤트 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: 모범 사례및 Azure 시간 인사이트 미리 보기에서 쿼리를 위한 이벤트를 셰이핑하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650926"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기를 사용하여 이벤트 셰이핑

이 문서에서는 Azure Time Series Insights에서 수집을 위해 JSON 페이로드를 형성하고 미리 보기 쿼리의 효율성을 최대화하는 모범 사례를 정의합니다.

## <a name="best-practices"></a>모범 사례

타임시리즈 인사이트 미리보기 환경으로 이벤트를 보내는 방법을 신중하게 고려하는 것이 가장 좋습니다. 

일반적인 모범 사례는 다음과 같습니다.

* 네트워크를 통해 최대한 효율적으로 데이터를 보냅니다.
* 시나리오에 보다 적합한 집계에 도움이 되는 방식으로 데이터를 저장합니다.

최상의 쿼리 성능을 위해 다음 의 기본 규칙을 준수하십시오.

* 불필요한 속성을 보내지 않습니다. 타임 시리즈 인사이트 사용법에 따라 청구서를 미리 보기. 쿼리할 데이터만 저장하고 처리하는 것이 가장 좋습니다.
* 정적 데이터에 인스턴스 필드를 사용합니다. 이 방법을 사용하면 네트워크를 통해 정적 데이터를 전송하지 않도록 할 수 있습니다. 시계열 모델의 구성 요소인 인스턴스 필드는 일반적으로 사용할 수 있는 시계열 인사이트 서비스의 참조 데이터와 같이 작동합니다. 인스턴스 필드에 대한 자세한 내용은 [타임시리즈 모델](./time-series-insights-update-tsm.md)을 참조하십시오.
* 둘 이상의 이벤트 간에 차원 속성을 공유합니다. 이 방법은 네트워크를 통해 보다 효율적으로 데이터를 보내는 데 도움이 됩니다.
* 여러 배열 중첩을 사용하지 않습니다. Time Series Insights 미리 보기는 개체를 포함하는 중첩배열의 최대 두 수준을 지원합니다. Time Series Insights 미리 보기에서는 메시지의 배열을 속성 값 쌍이 있는 여러 이벤트로 평면화합니다.
* 대부분 또는 모든 이벤트에 몇 가지 측정값이 존재하는 경우 이러한 측정값을 동일한 개체 내에서 별도 속성으로 전송하는 것이 좋습니다. 별도로 보내면 이벤트 수가 줄어들고 처리해야 하는 이벤트 수가 적기 때문에 쿼리 성능이 향상될 수 있습니다.

## <a name="column-flattening"></a>열 병합

인젝션 하는 동안 중첩된 개체를 포함하는 페이로드가 병합되어 열 이름이 설명자가 있는 단일 값이 됩니다.

* 예를 들어, 다음 중첩 된 JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   된다: `data_flow` 평평 해 질 때.

> [!IMPORTANT]
> * Azure Time Series 인사이트`_`미리 보기는 열 묘사에 대해 밑줄() 을 사용합니다.
> * 대신 마침표 ()`.`를 사용하는 일반 공급과의 차이를 유의하십시오.

더 복잡한 시나리오는 아래에 설명되어 있습니다.

#### <a name="example-1"></a>예제 1:

다음 시나리오에는 *유량,* *엔진 오일 압력,* *온도*및 *습도와*같은 측정값(신호)을 보내는 두 개 이상의 장치가 있습니다.

외부 배열에 공통 차원 값의 공유 섹션이 포함된 단일 Azure IoT Hub 메시지가 전송됩니다(메시지에 포함된 두 장치 항목 참고).

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

**테이크 아웃:**

* 예제 JSON에는 메시지의 효율성을 높이기 위해 [타임시리즈 인스턴스](./time-series-insights-update-tsm.md#time-series-model-instances) 데이터를 사용하는 외부 배열이 있습니다. Time Series 인스턴스 장치 메타데이터가 변경될 가능성이 없더라도 데이터 분석에 유용한 속성을 제공하는 경우가 많습니다.

* JSON은 두 개 이상의 메시지(각 장치에서 하나)를 단일 페이로드로 결합하여 시간이 지남에 따라 대역폭을 절약합니다.

* 각 장치에 대한 개별 계열 데이터 요소가 단일 **계열** 특성으로 결합되어 각 장치에 대한 업데이트를 지속적으로 스트리밍할 필요가 줄어듭니다.

> [!TIP]
> 데이터를 보내고 원격 분석을 보다 효율적으로 만드는 데 필요한 메시지 수를 줄이려면 공통 차원 값과 Time Series 인스턴스 메타데이터를 단일 JSON 페이로드로 일괄 처리하는 것이 좋습니다.

#### <a name="time-series-instance"></a>Time Series Instance 

[타임시리즈 인스턴스를](./time-series-insights-update-tsm.md#time-series-model-instances) 사용하여 JSON을 보다 최적으로 형성하는 방법을 자세히 살펴보겠습니다. 

> [!NOTE]
> 아래의 [타임시리즈 ID는](./time-series-insights-update-how-to-id.md) *장치ID입니다.*

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

| deviceId  | Type | L1 | L2 | timestamp | series_Flow 속도 ft3/s | series_Engine 오일 압력 사이오니 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | 시뮬레이터 | 배터리 시스템 | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | 시뮬레이터 |   배터리 시스템 |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | 시뮬레이터 |    배터리 시스템 |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  앞의 테이블은 [타임시리즈 미리 보기 탐색기의](./time-series-insights-update-explorer.md)쿼리 보기를 나타냅니다.

**테이크 아웃:**

* 앞의 예제에서 정적 속성은 네트워크로 전송된 데이터를 최적화하기 위해 타임시리즈 인사이트 미리 보기에 저장됩니다.
* 타임시리즈 인사이트 미리 보기 데이터는 인스턴스에 정의된 타임시리즈 ID를 통해 쿼리 시 조인됩니다.
* 두 개의 중첩 레이어가 사용됩니다. 이 숫자는 타임시리즈 인사이트 미리 보기가 지원하는 가장 많은 수치입니다. 여러 중첩된 배열을 방지해야 합니다.
* 측정값이 거의 없으므로 동일한 개체 내의 개별 속성으로 전송됩니다. 예에서 **series_Flow 속도 psi,** **series_Engine 오일 압력 psi,** **및 series_Flow 속도 ft3/s는** 고유 한 열입니다.

>[!IMPORTANT]
> 인스턴스 필드는 원격 분석과 함께 저장되지 않습니다. 그들은 타임 시리즈 모델의 메타데이터와 함께 저장됩니다.

#### <a name="example-2"></a>예제 2:

JSON은 다음을 고려하십시오.

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

위의 예에서 병합된 `data["flow"]` 속성은 속성과 명명 충돌을 `data_flow` 표시합니다.

이 경우 *최신* 속성 값은 이전 값과 덮어씁니다. 

> [!TIP]
> 더 많은 도움이 필요하십니까?

> [!WARNING] 
> * 병합 또는 다른 메커니즘으로 인해 동일한(단수) 이벤트 페이로드에 중복 속성이 있는 경우 최신 > 속성 값이 저장되어 이전 값을 과도하게 작성합니다.
> * 일련의 결합된 이벤트는 서로를 재정의하지 않습니다.

## <a name="next-steps"></a>다음 단계

* 이러한 지침을 실행하려면 [Azure Time Series Insights 미리 보기 쿼리 구문을](./time-series-insights-query-data-csharp.md)읽습니다. 데이터 액세스를 위한 타임시리즈 인사이트 [미리 보기 REST API에](https://docs.microsoft.com/rest/api/time-series-insights/preview) 대한 쿼리 구문에 대해 자세히 알아봅니다.

* JSON 모범 사례를 [타임 시리즈 모델 과 결합합니다.](./time-series-insights-update-how-to-tsm.md)
