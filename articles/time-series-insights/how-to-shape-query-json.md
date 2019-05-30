---
title: Azure Time Series Insights 쿼리에서 JSON을 셰이핑하는 방법 모범 사례 | Microsoft Docs
description: Azure Time Series Insights 쿼리 효율성을 개선하는 방법을 알아봅니다.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244470"
---
# <a name="shape-json-to-maximize-query-performance"></a>쿼리 성능을 최대화 하는 JSON 셰이프 

이 문서에서는 Azure Time Series Insights 쿼리의 효율성을 최대화 하는 JSON 셰이프 하는 방법에 지침을 제공 합니다.

## <a name="video"></a>비디오

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>저장소 요구 사항에 맞게 JSON 셰이핑 하는 것에 대 한 모범 사례에 알아봅니다.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>모범 사례
Time Series Insights에 이벤트를 보내는 방법을 생각해 보세요. 즉, 사용자:

1. 네트워크를 통해 최대한 효율적으로 데이터를 보냅니다.
1. 시나리오에 적합 한 집계를 수행할 수 있도록 데이터 방식으로 저장 되어 있는지 확인 합니다.
1. Time Series Insights 최대 속성 한계에 도달 하지 있는지 확인 합니다.
   - S1 환경의 경우 600개의 속성(열)
   - S2 환경의 경우 800개의 속성(열)

최상의 쿼리 성능을 보장 하기 위해 다음 지침을 사용 하면:

1. 속성 이름으로 태그 ID와 같은 동적 속성을 사용 하지 마세요. 이 사용 하이 여 속성을 최대 한도 도달에 기여 합니다.
1. 불필요한 속성을 보내지 않습니다. 쿼리 속성 필요 없으면 보낼 필요가 적합 합니다. 이러한 방식으로 저장소 제한 방지할 수 있습니다.
1. 사용 하 여 [참조 데이터](time-series-insights-add-reference-data-set.md) 네트워크를 통해 정적 데이터를 보내지 않도록 합니다.
1. 네트워크를 통해 데이터를 보다 효율적으로 보내는 여러 이벤트 간에 차원 속성을 공유 합니다.
1. 여러 배열 중첩을 사용하지 않습니다. Time Series Insights에는 중첩 된 배열 개체를 포함 하는 최대 2 개의 수준을 지원 합니다. Time Series Insights는 메시지의 배열 속성 값 쌍을 사용 하 여 여러 이벤트를 평면화합니다.
1. 대부분 또는 모든 이벤트에 몇 가지 측정값이 존재하는 경우 이러한 측정값을 동일한 개체 내에서 별도 속성으로 전송하는 것이 좋습니다. 개별적으로 보내고 이벤트 수가 줄어들고 더 적은 이벤트를 처리 해야 하기 때문에 쿼리 성능이 향상 될 수 있습니다. 여러 측정값의 경우 최대 속성 한도 도달 가능성이 최소화 됩니다 단일 속성의 값으로 보냅니다.

## <a name="example-overview"></a>예제 개요

다음 두 예제에는 이전 권장 사항을 강조 표시 하는 이벤트를 보내는 방법을 보여 줍니다. 각 예제에서는 다음 권장 사항을 적용 된 방식을 볼 수 있습니다.

예제는 여러 디바이스가 측정값 또는 신호를 보내는 시나리오를 기반으로 합니다. 측정값 또는 신호 흐름 속도, 엔진 오일 압력, 온도 및 습도 수 있습니다. 첫 번째 예제에는 모든 디바이스에 대한 몇 가지 측정값이 있습니다. 두 번째 예제는 여러 장치 및 각 장치는 고유한 측정값을 보냅니다.

## <a name="scenario-one-only-a-few-measurements-exist"></a>시나리오 1: 몇 가지 측정만 존재

> [!TIP]
> 열 또는 별도 속성으로 각 측정 또는 신호를 보내는 것이 좋습니다.

다음 예의 경우 외부 배열 공통 차원 값의 공유 섹션에 포함 된 단일 Azure IoT Hub 메시지 외부 배열은 참조 데이터를 사용하여 메시지의 효율성을 높입니다. 참조 데이터는 모든 이벤트를 사용 하 여 변경 되지 않는 장치 메타 데이터를 포함 하지만 데이터 분석에 대 한 유용한 속성을 제공 합니다. 일반 차원 값을 일괄 처리 및 데이터 연결을 통해 전송 된 바이트에 저장 하는 참조에는 메시지 더 효율적입니다.

예제 JSON 페이로드:

```json
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

* 키 속성이 있는 참조 데이터 테이블 **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* Time Series Insights 이벤트 테이블을 결합 한 후:

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

이러한 두 테이블에 대 한 참고 사항:

- **deviceId** 열은 다수의 다양한 디바이스에서 열 헤더로 사용합니다. DeviceId 값을 만드는 자체 속성 이름 (예: S1 환경) 595 또는 795 (S2 환경)에 대 한 나머지 5 개 열을 사용 하 여 전체 장치를 제한 합니다.
- 예, 제조업체 및 모델 정보에 대 한 불필요 한 속성 방지 됩니다. 속성을 나중에 쿼리할 수 없습니다 때문에 더 나은 네트워크 및 저장소 효율성 사용 하면 제거 합니다.
- 참조 데이터는 네트워크를 통해 전송된 바이트 수를 줄이는 데 사용됩니다. 두 특성 **messageId** 하 고 **deviceLocation** 키 속성을 사용 하 여 조인 됩니다 **deviceId**합니다. 이 데이터는 수신 시 원격 분석 데이터와 결합 되 고 쿼리용 Time Series Insights에 저장 됩니다.
- Time Series Insights에서 지원 되는 중첩의 최대 크기는 두 중첩 레이어 사용 됩니다. 여러 중첩된 배열을 방지해야 합니다.
- 몇 가지 측정값이 측정값의 이름이 동일한 개체 내에서 별도 속성으로 보내집니다. 여기에서 **series.Flow Rate psi** 및 **series.Engine Oil Pressure ft3/s**는 고유한 열입니다.

## <a name="scenario-two-several-measures-exist"></a>시나리오 2: 여러 측정값이 존재

> [!TIP]
> 입력할"," "단위" 및 "value" 튜플 측정을 보내도록 하는 것이 좋습니다.

예제 JSON 페이로드:

```json
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

* 키 속성이 포함 된 참조 데이터 테이블 **deviceId** 하 고 **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | 형식 | 단위 |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi |
   | FYYY | pumpRate | LINE\_DATA | US | 흐름 속도 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi |

* Time Series Insights 이벤트 테이블을 결합 한 후:

   | deviceId | series.tagId | messageId | deviceLocation | 형식 | 단위 | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | 흐름 속도 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi | 2018-01-17T01:18:00Z | 22.2 |

이러한 두 테이블에 대 한 참고 사항:

- 열 **deviceId** 하 고 **series.tagId** 다양 한 장치와 편대의 태그에 대 한 열 머리글로 사용 합니다. 다른 6 개 열을 사용 하 여 장치를 총 자체 특성 (예: S1 환경) 594 또는 794 (S2 환경)에 대 한 쿼리를 제한 하는 대로 각를 사용 하 여 합니다.
- 첫 번째 예제에서 설명한 이유로 인해 불필요 한 속성 방지할 되었습니다.
- 참조 데이터를 도입 하 여 네트워크를 통해 전송 된 바이트 수를 줄이기 위해 사용 됩니다 **deviceId**, 고유한 쌍에 사용 되는 **messageId** 고 **deviceLocation**. 복합 키 **series.tagId** 고유 쌍에 사용 됩니다 **형식** 하 고 **단위**합니다. 복합 키를 통해 합니다 **deviceId** 및 **series.tagId** 쌍 네 가지 값을 참조 하는 데 사용할: **messageId가 deviceLocation, 입력** 및 **단위**. 이 데이터는 수신 시 원격 분석 데이터와 조인 됩니다. 다음 쿼리를 위한 Time Series Insights에 저장 됩니다.
- 첫 번째 예제에서 설명한 이유로 인해 중첩의 두 계층 사용 됩니다.

### <a name="for-both-scenarios"></a>두 시나리오 모두의 경우

많은 수의 가능한 값을 사용 하 여 속성의 경우 각 값에 대해 새 열을 만들지 않고 단일 열 내에서 고유한 값으로 보내도록 적합 합니다. 앞의 두 예제에서:

  - 첫 번째 예제에서는 몇 가지 속성이 없으므로 여러 개의 값을 각각 별도 속성을 확인 하기에 적합 합니다.
  - 두 번째 예제에서는 측정값 개별 속성으로 지정 되지 않습니다. 대신 일련의 공통 속성 아래에 있는 측정값 또는 값의 배열 됩니다. 새 키 **tagId** 전송 되는 새 열을 만듭니다 **series.tagId** 플랫된 테이블의 합니다. 새 속성 **형식** 하 고 **단위** 속성의 제한에 도달 하지 않도록 참조 데이터를 사용 하 여 만들어집니다.

## <a name="next-steps"></a>다음 단계

- 읽기 [Azure Time Series Insights 쿼리 구문을](/rest/api/time-series-insights/ga-query-syntax) Time Series Insights 데이터 액세스 REST API에 대 한 쿼리 구문에 대 한 자세한 합니다.
- 에 대해 알아봅니다 [셰이프 이벤트 방법](./time-series-insights-send-events.md)합니다.
