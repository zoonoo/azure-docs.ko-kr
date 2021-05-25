---
title: 시계열 모델 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 시계열 모델에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 37c24f2d785bbdd9847e0dadaa47969f5b090ef2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698096"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2의 시계열 모델

이 문서에서는 시계열 모델, 기능 및 Azure Time Series Insights Gen2 환경에서 고유 모델 빌드 및 업데이트를 시작하는 방법을 설명합니다.

> [!TIP]
>
> * 라이브 시계열 모델 예제를 보려면  [Contoso Wind Farm 데모](https://insights.timeseries.azure.com/preview/samples) 환경으로 이동합니다.
> * Azure Time Series Insights 탐색기를 사용하여 [시계열 모델을 사용하는 방법](./how-to-edit-your-model.md)에 대해 알아봅니다.

## <a name="summary"></a>요약

전통적으로 IoT 디바이스에서 수집되는 데이터는 컨텍스트 정보가 부족하기 때문에 신속하게 센서를 찾아서 분석하기가 어렵습니다. 시계열 모델을 사용하는 주요 이유는 IoT 또는 시계열 데이터를 쉽게 찾아서 분석하기 위한 것입니다. 시계열 모델은 분석을 위해 소비자가 즉시 사용 가능한 데이터 세트를 준비하기 위해 시계열 데이터를 큐레이션, 유지 관리 및 보강하여 이 목표를 달성합니다.

## <a name="scenario-contosos-new-smart-oven"></a>시나리오: Contoso의 새로운 스마트 오븐

**가상의 Contoso 스마트 오븐 시나리오를 가정합니다.** 이 시나리오에서 Contoso 스마트 오븐에는 각각의 상단 버너 4개와 오븐 자체를 포함하여 5개의 온도 센서가 있습니다. 최근까지는 각 Contoso 온도 센서가 데이터를 개별적으로 전송, 저장 및 시각화했습니다. 주방 기기 모니터링을 위해 Contoso에는 개별 센서에 대해 하나씩 기본 차트가 사용되었습니다.

초기 데이터 및 시각화 솔루션은 만족스러웠지만 일부 제한이 드러났습니다.

* 고객들은 상단 버너를 거의 모두 사용할 때 오븐이 전반적으로 얼마나 뜨거워질 수 있는지 알 수 있기를 원했습니다. Contoso는 오븐의 전반적인 조건을 분석하고 통합 답변을 제공하는 데 어려움을 겪었습니다.
* Contoso 엔지니어들은 상단 버너를 동시에 작동할 때 전력 소비 효율이 낮아지지 않는지 확인할 수 있기를 원했습니다. 온도 및 전압 센서가 서로 어떻게 연관되어 있고 이를 찾는 방법을 상호 참조하는 데 어려움이 있었습니다.
* Contoso 품질 보증 팀은 두 센서 버전 사이의 기록을 감사하고 비교할 수 있기를 원했습니다. 어떤 데이터가 어떤 센서 버전에 속하는지 확인하는 것이 어려웠습니다.

가장 중요한 스마트 오븐 시계열 모델을 구성, 정리 및 정의할 수 있는 기능 없이 각 온도 센서가 분리되어 있고, 효율적이지 않은 데이터 포인트를 유지 관리했습니다. 각 데이터 세트가 서로 독립적으로 존재했기 때문에 이러한 데이터 포인트를 실행 가능한 인사이트로 전환하는 것이 더 어려웠습니다.

이러한 제한에 따라 Contoso의 새 오븐에 포함할 스마트 데이터 수집 및 시각화 도구의 중요성이 드러났습니다.

* 데이터 시각화는 데이터를 편리한 보기로 연결하고 조합할 수 있을 때 유용성이 입증됩니다. 한 가지 예는 온도 센서와 함께 전압 센서를 표시하는 것입니다.
* 비교, 확대/축소 및 시간 범위 기능과 함께 여러 요소들에 대한 다차원 데이터 관리하는 것은 달성하기 어려운 목표일 수 있습니다.

이러한 가상의 예시에서 다루는 여러 시나리오에 대해 **시계열 모델은 편리한 솔루션을 제공** 합니다.

[![시계열 모델 스마트 오븐 차트 예제](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* 시계열 모델은 시간 범위 내에서 그리고 센서와 디바이스 종류 간 비교를 수행하여 데이터를 컨텍스트화하기 때문에 쿼리 및 탐색에서 중요한 역할을 수행합니다. (**A**)
* 시계열 모델에 저장되는 데이터가 시계열 쿼리 계산을 변수로 보존하고 쿼리 시에 이를 재사용하기 때문에 데이터가 추가로 컨텍스트화됩니다.
* 시계열 모델은 향상된 시각화 및 관리 기능을 위해 데이터를 관리하고 집계합니다. (**B**)

### <a name="key-capabilities"></a>주요 기능

시계열 컨텍스트화를 간단하고 손쉽게 관리하기 위해 개발된 시계열 모델은 Azure Time Series Insights Gen2에서 다음과 같은 기능을 지원합니다. 다음과 같은 도움을 줍니다.

* 스칼라 함수를 활용하여 계산 또는 수식을 작성 및 관리하고, 작업을 집계하는 등 여러 가지를 할 수 있습니다.
* 탐색, 검색 및 참조를 사용하도록 부모-자식 관계를 정의합니다.
* *인스턴스 필드* 로 정의된 인스턴스와 연결된 속성을 정의하고 계층 구조를 만드는 데 사용합니다.

### <a name="components"></a>구성 요소

시계열 모델에는 세 가지 핵심 구성 요소가 있습니다.

* [시계열 모델 인스턴스](#time-series-model-instances)
* [시계열 모델 계층 구조](#time-series-model-hierarchies)
* [시계열 모델 형식](#time-series-model-types)

이러한 구성 요소를 결합하여 시계열 모델을 지정하고 데이터를 관리합니다.

[![시계열 모델 개요 차트](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

시계열 모델은 [Azure Time Series Insights 탐색기](./concepts-ux-panels.md)를 통해 만들고 관리할 수 있습니다. 시계열 모델 설정은 [모델 설정 API](/rest/api/time-series-insights/reference-model-apis)를 통해 관리할 수 있습니다.

## <a name="time-series-model-instances"></a>시계열 모델 인스턴스

시계열 모델 *인스턴스* 는 시계열 자체에 대한 가상 표현입니다.

대부분의 경우 시계열 ID로 저장되는 **deviceId** 또는 **assetId** 로 고유하게 식별됩니다.

인스턴스에는 시계열 ID, 유형, 이름, 설명, 계층 구조 및 인스턴스 필드와 같이 *인스턴스 속성* 이라고 부르는 연관된 설명 정보가 포함되어 있습니다. 인스턴스 속성에는 적어도 계층 구조 정보가 포함됩니다.

*인스턴스 필드* 는 제조업체, 운영자 등의 계층 구조 수준에 대한 값을 포함할 수 있는 설명적인 정보의 모음입니다.

Azure Time Series Insights Gen2 환경에 대해 이벤트 원본이 구성된 후 시계열 모델에서 인스턴스가 자동으로 검색되고 생성됩니다. 시계열 모델 쿼리를 사용하여 Azure Time Series Insights 탐색기를 통해 인스턴스를 만들거나 업데이트할 수 있습니다.

[Contoso Wind Farm 데모](https://insights.timeseries.azure.com/preview/samples)는 몇 가지 라이브 인스턴스 예제를 제공합니다.

[![시계열 모델 인스턴스 예제](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>인스턴스 속성

인스턴스는 **timeSeriesId**, **typeId**, **name**, **description**, **hierarchyIds** 및 **instanceFields** 로 정의됩니다. 각 인스턴스는 한 가지 *형식* 과 하나 이상의 *계층 구조* 에 매핑됩니다.

| 속성 | 설명 |
| --- | ---|
| timeSeriesId | 인스턴스가 연결된 시계열의 고유 ID입니다. 대부분의 경우 deviceId 또는 assetId와 같은 속성으로 인스턴스가 고유하게 식별됩니다. 경우에 따라 최대 3개의 속성이 결합된 보다 구체적인 복합 ID를 사용할 수 있습니다. |
| typeId | 인스턴스가 연결된 시계열 모델 유형의 대/소문자를 구분하는 고유 문자열 ID입니다. 기본적으로 검색된 모든 새 인스턴스가 기본 유형에 연결됩니다.
| name | **name** 속성은 선택 사항이며 대/소문자를 구분합니다. **이름** 을 사용할 수 없으면 기본적으로 **timeSeriesId** 로 지정됩니다. 이름이 제공된 경우 **timeSeriesId** 를 [well](./concepts-ux-panels.md#4-time-series-well)에 계속 사용할 수 있습니다. |
| description | 인스턴스에 대한 텍스트 설명입니다. |
| hierarchyIds | 인스턴스가 속한 계층 구조를 정의합니다. |
| instanceFields | 인스턴스 그리고 인스턴스를 정의하는 모든 정적 데이터입니다. 계층 구조 또는 비 계층 구조의 값을 정의하는 한편 검색 작업을 수행하기 위한 인덱싱도 지원합니다. |

> [!NOTE]
> 계층 구조는 인스턴스 필드를 사용하여 빌드됩니다. 추가 인스턴스 속성 정의를 위해 **instanceFields** 를 추가할 수 있습니다.

인스턴스에 다음 JSON 표현이 포함됩니다.

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> 인스턴스 API CRUD(만들기, 읽기, 업데이트 및 삭제) 지원에 대해서는 [데이터 쿼리](./concepts-query-overview.md#time-series-model-query-tsm-q-apis) 문서 및 [인스턴스 API REST 설명서](/rest/api/time-series-insights/reference-model-apis#instances-api)를 참조하세요.

## <a name="time-series-model-hierarchies"></a>시계열 모델 계층 구조

시계열 모델 *계층 구조* 는 속성 이름 및 해당 관계를 지정하여 인스턴스를 구성합니다.

지정된 Azure Time Series Insights Gen2 환경에서 여러 계층 구조를 구성할 수 있습니다. 시계열 모델 인스턴스는 단일 또는 여러 계층 구조에 매핑될 수 있습니다(다대다 관계).

[Contoso Wind Farm 데모](https://insights.timeseries.azure.com/preview/samples)는 표준 인스턴스 및 유형 계층 구조를 보여줍니다.

[![시계열 모델 계층 구조 예제](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>계층 구조 정의

계층 구조는 계층 구조 **ID**, **이름** 및 **원본** 을 통해 정의됩니다.

| 속성 | Description |
| ---| ---|
| id | 예를 들어 인스턴스를 정의할 때 사용되는 계층 구조에 대한 고유 식별자입니다. |
| name | 계층 구조에 대해 이름을 제공하기 위해 사용되는 문자열입니다. |
| source | 사용자가 만들려는 계층 구조의 하향식 부모-자식 순서를 나타내는 조직 계층 구조 또는 경로를 지정합니다. 부모-자식 속성은 인스턴스 필드로 매핑됩니다. |

계층 구조는 JSON에서 다음으로 표시됩니다.

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

이전 JSON 예제:

* `Location`이 부모 `states` 및 자식 `cities`가 포함된 계층 구조를 정의합니다. 각 `location`에는 여러 `states`가 포함될 수 있고, 여기에는 다시 여러 `cities`가 포함될 수 있습니다.
* `ManufactureDate`가 부모 `year` 및 자식 `month`가 포함된 계층 구조를 정의합니다. 각 `ManufactureDate`에는 여러 `years`가 포함될 수 있고, 여기에는 다시 여러 `months`가 포함될 수 있습니다.

> [!TIP]
> 계층 구조 API CRUD(만들기, 읽기, 업데이트 및 삭제) 지원에 대해서는 [데이터 쿼리](concepts-query-overview.md#time-series-model-query-tsm-q-apis) 문서 및 [계층 구조 API REST 설명서](/rest/api/time-series-insights/reference-model-apis#hierarchies-api)를 참조하세요.

### <a name="hierarchy-example"></a>계층 예

계층 구조 **H1** 에 `building`, `floor` 및 `room`이 해당 **instanceFieldNames** 정의의 일부로 포함된 예제를 가정해보세요.

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

이전 정의 및 일부 시계열에 사용된 인스턴스 필드에 따라 계층 구조 특성 및 값이 다음 표에 표시된 것과 같이 나타납니다.

| Time Series ID | 인스턴스 필드 |
| --- | --- |
| ID1 | "building" = "1000", "floor" = "10", "room" = "55"  |
| ID2 | "building" = "1000", "room" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "building" = "1000", "floor" = "10"  |
| ID5 | "building", "floor" 또는 "room"이 설정되지 않습니다. |

시계열 **ID1** 및 **ID4** 는 *building*, *floor* 및 *room* 매개 변수를 완전히 정의하고 올바르게 정렬했기 때문에 [Azure Time Series Insights 탐색기](./concepts-ux-panels.md)에 **H1** 계층 구조의 일부로 표시됩니다.

다른 항목은 지정된 데이터 계층 구조를 준수하지 않기 때문에 *부모가 없는 인스턴스* 로 분류됩니다.

## <a name="time-series-model-types"></a>시계열 모델 형식

시계열 모델 *형식* 은 계산을 수행하기 위한 변수 또는 수식을 정의하는 데 도움이 됩니다. 형식은 특정 인스턴스와 연결됩니다.

형식에는 하나 이상의 변수가 포함될 수 있습니다. 예를 들어 시계열 모델 인스턴스는 *평균 온도*, *최소 온도* 및 *최대 온도* 변수로 구성되는 *온도 센서* 유형일 수 있습니다.

[Contoso Wind Farm 데모](https://insights.timeseries.azure.com/preview/samples)는 각 인스턴스와 연관된 몇 가지 시계열 모델 유형을 시각화합니다.

[![시계열 모델 유형 예제](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> 유형 API CRUD(만들기, 읽기, 업데이트 및 삭제) 지원에 대해서는 [데이터 쿼리](concepts-query-overview.md#time-series-model-query-tsm-q-apis) 문서 및 [유형 API REST 설명서](/rest/api/time-series-insights/reference-model-apis#types-api)를 참조하세요.

### <a name="type-properties"></a>형식 속성

시계열 모델 유형은 **id**, **이름**, **설명** 및 **변수로** 정의됩니다.

| 속성 | Description |
| ---| ---|
| id | 유형에 대한 대/소문자를 구분하는 고유 문자열 ID입니다. |
| name | 유형에 대해 이름을 제공하기 위해 사용되는 문자열입니다. |
| description | 유형에 대한 문자열 설명입니다. |
| variables | 유형과 연결된 변수를 지정합니다. |

유형은 다음 JSON 예제를 따릅니다.

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
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
      }
    }
  ]
}
```

시계열 모델 유형은 이벤트에서 수식 및 계산 규칙을 지정하는 여러 변수를 포함할 수 있습니다. [시계열 모델 변수 정의 방법](./concepts-variables.md)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

* API를 통해 모델을 편집하는 방법에 대한 자세한 내용은 [시계열 모델](/rest/api/time-series-insights/reference-model-apis) 참조 설명서를 읽어보세요.

* [시계열 모델 변수](./concepts-variables.md)로 만들 수 있는 수식 및 계산을 살펴봅니다.

* Azure Time Series Insights Gen2에서 [데이터를 쿼리](concepts-query-overview.md)하는 방법에 대해 알아봅니다.