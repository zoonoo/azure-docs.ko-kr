---
title: Azure Time Series Insights 미리 보기를 사용하여 이벤트 셰이핑 | Microsoft Docs
description: Azure Time Series Insights 미리 보기를 사용하여 이벤트를 셰이핑하는 방법을 이해합니다.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: eb398ad621167ad9f9b245fb8aa98c6942b87668
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557430"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기를 사용하여 이벤트 셰이핑

이 문서는 Azure Time Series Insights 미리 보기 쿼리의 효율성을 최대화하도록 JSON 파일을 셰이핑하는 데 도움이 됩니다.

## <a name="best-practices"></a>모범 사례

Time Series Insights 미리 보기에 이벤트를 보내는 방법을 고려해야 합니다. 즉, 다음을 수행해야 합니다.

* 네트워크를 통해 최대한 효율적으로 데이터를 보냅니다.
* 시나리오에 보다 적합한 집계에 도움이 되는 방식으로 데이터를 저장합니다.

최적의 쿼리 성능을 얻으려면 다음을 수행합니다.

* 불필요한 속성을 보내지 않습니다. Time Series Insights 미리 보기에서는 사용량에 따라 요금을 청구합니다. 쿼리할 데이터를 저장하고 처리하는 것이 가장 좋습니다.
* 정적 데이터에 인스턴스 필드를 사용합니다. 이 방법은 네트워크를 통한 정적 데이터 전송을 방지하는 데 도움이 됩니다. 시계열 모델의 구성 요소인 인스턴스 필드는 Time Series Insights GA 서비스의 참조 데이터처럼 작동합니다. 인스턴스 필드에 대한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.
* 둘 이상의 이벤트 간에 차원 속성을 공유합니다. 이 방법은 네트워크를 통해 보다 효율적으로 데이터를 보내는 데 도움이 됩니다.
* 여러 배열 중첩을 사용하지 않습니다. Time Series Insights 미리 보기에서는 개체를 포함하는 중첩 배열을 최대 두 수준까지 지원합니다. Time Series Insights 미리 보기에서는 메시지의 배열을 속성 값 쌍이 있는 여러 이벤트로 평면화합니다.
* 대부분 또는 모든 이벤트에 몇 가지 측정값이 존재하는 경우 이러한 측정값을 동일한 개체 내에서 별도 속성으로 전송하는 것이 좋습니다. 개별적으로 보내면 이벤트 수가 감소하며, 처리해야 하는 이벤트 수가 더 적기 때문에 쿼리 성능이 개선될 수 있습니다.

## <a name="example"></a>예

다음 예제는 둘 이상의 디바이스가 측정값 또는 신호를 보내는 시나리오를 기반으로 합니다. 측정값 또는 신호는 *흐름 속도*, *엔진 오일 압력*, *온도* 및 *습도*일 수 있습니다.

다음 예제에서는 단일 IoT Hub 메시지가 있습니다. 여기서 외부 배열에는 일반적인 차원 값의 공유 섹션이 포함됩니다. 외부 배열은 Time Series Instance 데이터를 사용하여 메시지의 효율성을 높입니다. Time Series Instance에는 모든 이벤트에서 변경되지 않는 디바이스 메타데이터가 포함되지만 데이터 분석에 유용한 속성을 제공합니다. 네트워크를 통해 전송되는 바이트를 줄이고 메시지를 보다 효율적으로 만들려면 공통 차원 값을 일괄 처리하고 Time Series Instance 메타데이터를 이용합니다.

### <a name="example-json-payload"></a>예제 JSON 페이로드

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Time Series Instance 
> [!NOTE]
> 시계열 ID는 *deviceId*입니다.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Time Series Instance 미리 보기에서는 쿼리 시간 중에 테이블을 조인합니다(평면화 후). 테이블에는 **유형** 등의 추가 열이 포함됩니다. 다음 예제에서는 원격 분석 데이터를 [셰이핑](./time-series-insights-send-events.md#json)하는 방법을 보여 줍니다.

| deviceId  | type | L1 | L2 | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | 리볼트 시뮬레이터 | 배터리 시스템 | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA    REVOLT | 시뮬레이터 |    배터리 시스템 |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | 시뮬레이터 |    배터리 시스템 |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

앞의 예제에서 다음 사항에 유의하세요.

* 정적 속성은 네트워크를 통해 전송되는 데이터를 최적화하기 위해 Time Series Insights 미리 보기에 저장됩니다.
* Time Series Insights 미리 보기 데이터는 인스턴스에 정의된 *timeSeriesId*를 사용하여 쿼리 시간에 조인됩니다.
* 두 개의 중첩 계층이 사용되며, 이것이 Time Series Insights 미리 보기에서 지원되는 최대 개수입니다. 여러 중첩된 배열을 방지해야 합니다.
* 측정값이 거의 없으므로 동일한 개체 내의 개별 속성으로 전송됩니다. 여기에서 **series.Flow Rate psi**, **series.Engine Oil Pressure psi** 및 **series.Flow Rate ft3/s**는 고유한 열입니다.

>[!IMPORTANT]
> 인스턴스 필드는 원격 분석과 함께 저장되지 않습니다. **Time Series Model**에 메타데이터와 함께 저장됩니다.
> 앞의 표는 쿼리 보기를 나타냅니다.

## <a name="next-steps"></a>다음 단계

이러한 지침을 실행하려면 [Azure Time Series Insights 미리 보기 쿼리 구문](./time-series-insights-query-data-csharp.md)을 참조하세요. Time Series Insights 미리 보기 데이터 액세스 REST API의 쿼리 구문에 대해 자세히 알아봅니다.

지원되는 JSON 셰이프에 대한 자세한 내용은 [지원되는 JSON 셰이프](./time-series-insights-send-events.md#json)를 참조하세요.
