---
title: JSON 형성을 위한 모범 사례 - Azure 타임시리즈 인사이트 쿼리 | 마이크로 소프트 문서
description: JSON을 형성하여 Azure Time Series Insights 쿼리 효율성을 개선하는 방법을 알아봅니다.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 8232fd4c2a1e17800c96854b4ba7298e57ed84b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989895"
---
# <a name="shape-json-to-maximize-query-performance"></a>쿼리 성능을 최대화하기 위한 셰이프 JSON 

이 문서에서는 Azure Time Series Insights 쿼리의 효율성을 최대화하기 위해 JSON을 셰이징하는 방법에 대한 지침을 제공합니다.

## <a name="video"></a>비디오

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>스토리지 요구 사항을 충족하기 위해 JSON을 형성하는 모범 사례를 알아보십시오.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>모범 사례

이벤트를 타임시리즈 인사이트로 보내는 방법을 생각해 보십시오. 즉, 당신은 항상 :

1. 네트워크를 통해 최대한 효율적으로 데이터를 보냅니다.
1. 시나리오에 적합한 집계를 수행할 수 있도록 데이터가 저장되어 있는지 확인합니다.
1. 다음의 최대 속성 제한에 도달하지 않았는지 확인합니다.
   - S1 환경의 경우 600개의 속성(열)
   - S2 환경의 경우 800개의 속성(열)

> [!TIP]
> Azure Time Series 인사이트 미리 보기에서 [제한 및 계획을](time-series-insights-update-plan.md) 검토합니다.

다음 지침은 최상의 쿼리 성능을 보장하는 데 도움이 됩니다.

1. 태그 ID와 같은 동적 속성을 속성 이름으로 사용하지 마십시오. 이 사용은 최대 속성 제한에 도달하는 데 기여합니다.
1. 불필요한 속성을 보내지 않습니다. 쿼리 속성이 필요하지 않은 경우 쿼리 속성을 보내지 않는 것이 좋습니다. 이렇게 하면 저장소 제한을 피할 수 있습니다.
1. [참조 데이터를](time-series-insights-add-reference-data-set.md) 사용하여 네트워크를 통해 정적 데이터를 전송하지 않도록 합니다.
1. 여러 이벤트 간에 차원 속성을 공유하여 네트워크를 통해 데이터를 보다 효율적으로 전송합니다.
1. 여러 배열 중첩을 사용하지 않습니다. Time Series Insights는 개체를 포함하는 중첩 배열의 최대 두 수준을 지원합니다. Time Series Insights는 메시지의 배열을 속성 값 쌍을 이어 여러 이벤트로 병합합니다.
1. 대부분 또는 모든 이벤트에 몇 가지 측정값이 존재하는 경우 이러한 측정값을 동일한 개체 내에서 별도 속성으로 전송하는 것이 좋습니다. 별도로 보내면 이벤트 수가 줄어들고 처리해야 하는 이벤트 수가 적기 때문에 쿼리 성능이 향상될 수 있습니다. 여러 측정값이 있는 경우 단일 속성에 값으로 보내면 최대 속성 제한에 도달할 가능성이 최소화됩니다.

## <a name="example-overview"></a>개요 예제

다음 두 예제에서는 이전 권장 사항을 강조 표시하는 이벤트를 보내는 방법을 보여 줍니다. 각 예제에 따라 권장 사항이 적용된 방법을 검토할 수 있습니다.

예제는 여러 디바이스가 측정값 또는 신호를 보내는 시나리오를 기반으로 합니다. 측정 또는 신호는 유량, 엔진 오일 압력, 온도 및 습도일 수 있습니다. 첫 번째 예제에는 모든 디바이스에 대한 몇 가지 측정값이 있습니다. 두 번째 예제에는 많은 장치가 있으며 각 장치는 많은 고유한 측정값을 전송합니다.

## <a name="scenario-one-only-a-few-measurements-exist"></a>시나리오 1: 몇 가지 측정값만 존재합니다.

> [!TIP]
> 각 측정 또는 신호를 별도의 속성 또는 열로 보내는 것이 좋습니다.

다음 예제에서는 외부 배열에 공통 차원 값의 공유 섹션이 포함된 단일 Azure IoT Hub 메시지가 있습니다. 외부 배열은 참조 데이터를 사용하여 메시지의 효율성을 높입니다. 참조 데이터에는 모든 이벤트마다 변경되지 않는 장치 메타데이터가 포함되어 있지만 데이터 분석을 위한 유용한 속성을 제공합니다. 공통 차원 값을 일괄 처리하고 참조 데이터를 사용하면 와이어를 통해 전송되는 바이트에 저장되어 메시지의 효율성이 향상됩니다.

Azure 클라우드로 보낼 때 JSON으로 직렬화되는 [IoT 장치 메시지 개체를](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) 사용하여 타임시리즈 Insights GA 환경으로 전송된 다음 JSON 페이로드를 고려하십시오.


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
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* 키 속성 장치가 있는 참조 데이터 **테이블Id:**

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* 플랫화 후 타임시리즈 인사이트 이벤트 테이블:

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - **deviceId** 열은 다수의 다양한 디바이스에서 열 헤더로 사용합니다. **deviceId** 값을 자체 속성 이름으로 만들면 총 장치가 595개(S1 환경의 경우) 또는 다른 5개 열의 경우 795(S2 환경의 경우)로 제한됩니다.
> - 불필요한 속성은 피할 수 있습니다(예: 메이크 및 모델 정보). 나중에 속성이 쿼리되지 않으므로 속성을 제거하면 네트워크 및 저장소 효율성이 향상됩니다.
> - 참조 데이터는 네트워크를 통해 전송된 바이트 수를 줄이는 데 사용됩니다. 두 속성 **messageId** 및 **deviceLocation** 키 속성 장치를 사용 하 여 조인 **됩니다Id**. 이 데이터는 inress 시간에 원격 분석 데이터와 결합된 다음 쿼리를 위해 타임시리즈 인사이트에 저장됩니다.
> - 두 개의 중첩 레이어가 사용되며, 이는 타임시리즈 Insights에서 지원하는 최대 중첩 양입니다. 여러 중첩된 배열을 방지해야 합니다.
> - 측정값이 거의 없기 때문에 측정값은 동일한 개체 내에서 별도의 속성으로 전송됩니다. 여기에서 **series.Flow Rate psi** 및 **series.Engine Oil Pressure ft3/s**는 고유한 열입니다.

## <a name="scenario-two-several-measures-exist"></a>시나리오 2: 몇 가지 측정값이 존재합니다.

> [!TIP]
> 측정값을 "유형", "단위" 및 "값" 투플으로 보내는 것이 좋습니다.

예제 JSON 페이로드:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* 키 속성 **장치ID** 및 **series.tagId가**있는 참조 데이터 테이블:

   | deviceId | series.tagId | messageId | deviceLocation | type | 단위 |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi |
   | FYYY | pumpRate | LINE\_DATA | US | 흐름 속도 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi |

* 플랫화 후 타임시리즈 인사이트 이벤트 테이블:

   | deviceId | series.tagId | messageId | deviceLocation | type | 단위 | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | 흐름 속도 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - 열 **deviceId** 및 **series.tagId는** 플릿의 다양한 장치 및 태그에 대한 열 헤더역할을 합니다. 각각을 고유한 특성으로 사용하면 쿼리가 594개(S1 환경의 경우) 또는 다른 6개 열이 있는 총 장치 794개(S2 환경의 경우)로 제한됩니다.
> - 첫 번째 예제에서 인용된 이유로 불필요한 속성을 피할 수 있었습니다.
> - 참조 데이터는 **messageId** 및 **deviceLocation의**고유 쌍에 사용되는 **deviceId를**도입하여 네트워크를 통해 전송되는 바이트 수를 줄이는 데 사용됩니다. 복합 키 **계열.tagId는** **고유한 형식** 및 **단위**쌍에 사용됩니다. 복합 키를 사용하면 **deviceId** 및 **series.tagId** 쌍을 사용하여 **messageId, deviceLocation, type** 및 **단위의**네 가지 값을 참조할 수 있습니다. 이 데이터는 입사 시 원격 분석 데이터와 결합됩니다. 그런 다음 쿼리를 위해 타임시리즈 인사이트에 저장됩니다.
> - 첫 번째 예제에서 인용된 이유로 두 개의 중첩 레이어가 사용됩니다.

### <a name="for-both-scenarios"></a>두 시나리오 모두의 경우

가능한 값이 많은 속성의 경우 각 값에 대해 새 열을 만드는 대신 단일 열 내에서 고유한 값으로 보내는 것이 가장 좋습니다. 앞의 두 예제에서:

  - 첫 번째 예에서 몇 가지 속성에는 여러 값이 있으므로 각 속성을 별도의 속성으로 만드는 것이 적절합니다.
  - 두 번째 예제에서는 측정값이 개별 속성으로 지정되지 않습니다. 대신 공통 계열 속성 아래의 값 또는 측정값의 배열입니다. 새 키 **tagId가** 전송되어 병합된 테이블에 새 열 **series.tagId가** 만들어집니다. 새 속성 **유형** 및 **단위는** 참조 데이터를 사용하여 작성되므로 속성 제한에 도달하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [IoT Hub 장치 메시지를 클라우드로](../iot-hub/iot-hub-devguide-messages-construct.md)보내는 것에 대해 자세히 읽어보십시오.

- [Azure Time Series Insights 쿼리 구문을](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) 읽고 타임시리즈 인사이트 데이터 액세스 REST API에 대한 쿼리 구문에 대해 자세히 알아보십시오.

- [이벤트 셰이프하는 방법에](./time-series-insights-send-events.md)대해 알아봅니다.
