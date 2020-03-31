---
title: 타임시리즈 모델 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure 열열 인사이트 미리 보기에서 열렬 모델에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476657"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Azure 타임시리즈 인사이트 미리 보기의 타임시리즈 모델

이 문서에서는 Azure Time Series Insights 미리 보기 환경에서 사용자 고유의 모델을 빌드하고 업데이트하는 방법 및 시간대 모델, 기능 및 방법에 대해 설명합니다.

> [!TIP]
>  * 라이브 타임 시리즈 모델 예제를 보려면 [Contoso 풍력 발전 단지 데모](https://insights.timeseries.azure.com/preview/samples) 환경으로 이동합니다.
> * [Azure Time Series 인사이트 미리 보기 탐색기를](time-series-insights-update-explorer.md) 읽고 열계 모델 UI를 탐색하는 방법을 알아봅니다.
> * 시계열 인사이트 웹 탐색기를 사용하여 [시계열 모델로 작업하는 방법을](time-series-insights-update-how-to-tsm.md) 알아봅니다.

## <a name="summary"></a>요약

전통적으로 IoT 디바이스에서 수집되는 데이터는 컨텍스트 정보가 부족하기 때문에 신속하게 센서를 찾아서 분석하기가 어렵습니다. 타임시리즈 모델의 주요 동기는 IoT 또는 타임시리즈 데이터를 찾고 분석하는 것을 단순화하는 것입니다. 이 목표를 달성하려면 타임시리즈 데이터의 큐레이션, 유지 관리 및 보강을 지원하여 소비자가 사용할 수 있는 데이터 집합을 분석할 수 있도록 지원합니다.

## <a name="scenario-contosos-new-smart-oven"></a>시나리오: 콘토소의 새로운 스마트 오븐

**Contoso 스마트 오븐의 가상 시나리오를 고려하십시오.** 이 시나리오에서는 각 Contoso 스마트 오븐에 5개의 온도 센서가 있으며 각 온도 센서는 4개의 상단 버너에 하나씩, 하나는 오븐 자체에 대해 있다고 가정합니다. 최근까지 각 Contoso 온도 센서는 데이터를 개별적으로 전송, 저장 및 시각화했습니다. 주방 기기 모니터링을 위해 Contoso는 각 개별 센서에 대해 하나씩 기본 차트에 의존했습니다.

Contoso는 초기 데이터 및 시각화 솔루션에 만족했지만 다음과 같은 몇 가지 제한 사항이 분명해졌습니다.

* 고객은 상위 버너의 대부분이 켜졌을 때 전체 오븐이 얼마나 뜨거워질지 알고 싶었습니다. Contoso는 전체 오븐의 조건에 대한 통합된 답변을 분석하고 제시하는 데 더 많은 어려움을 겪었습니다.
* Contoso 엔지니어들은 최고 버너가 동시에 실행되는 것이 비효율적인 전력 소모로 이어지지 않는지 확인하고자 했습니다. 어떤 온도 및 전압 센서가 서로 연관되어 있는지, 그리고 매장에서 센서를 찾는 방법을 상호 참조하는 데 어려움이 있었습니다.
* Contoso 품질 보증 팀은 두 센서 버전 간의 이력을 감사하고 비교하기를 원했습니다. 어떤 데이터가 어떤 센서 버전에 속하는지 결정하는 데 어려움이 있었습니다.

가장 중요한 스마트 오븐 시간계 모델을 구성, 구성 및 정의할 수 없는 각 온도 센서는 탈구, 격리 및 덜 유익한 데이터 포인트를 유지관리합니다. 각 데이터 집합이 다른 데이터 집합과 독립적으로 사용되었기 때문에 이러한 데이터 데이터를 실행 가능한 통찰력으로 전환하는 것이 더 어려웠습니다.

이러한 제한 은 Contoso의 새로운 오븐과 함께 스마트 데이터 집계 및 시각화 도구의 중요성을 밝혀 :

* 데이터 시각화는 데이터를 연결하고 편리한 뷰로 결합할 수 있을 때 유용합니다. 예를 들어 온도 센서와 함께 전압 센서를 표시하는 경우를 예로 들 수 있습니다.
* 비교, 확대/축소 및 시간 범위 기능과 함께 여러 엔터티에 대한 다차원 데이터를 관리하는 것은 수행하기 어려울 수 있습니다.

타임 시리즈 모델은 이 가상 예제에서 발생하는 많은 시나리오에 **대한 편리한 솔루션을 제공합니다.**

[![타임 시리즈 모델 스마트 오븐 차트 예제](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* 타임 시리즈 모델은 시간 범위와 센서 및 장치 종류 간에 비교를 그릴 수 있도록 하여 데이터를 컨텍스트화하기 때문에 쿼리 및 탐색에서 중요한 역할을 합니다. **(A)** 
* Time Series Model에 유지된 데이터는 타임시리즈 쿼리 계산을 변수로 유지하고 쿼리 시간에 다시 사용하기 때문에 데이터가 추가 컨텍스트화됩니다.
* 타임시리즈 모델은 향상된 시각화 및 관리 기능을 위해 데이터를 구성하고 집계합니다. **(B)** 

### <a name="key-capabilities"></a>주요 기능

시계열 컨텍스트화를 간단하고 손쉽게 관리하기 위해 개발된 시계열 모델은 Time Series Insights 미리 보기에서 다음과 같은 기능을 지원합니다. 다음과 같은 도움을 줍니다.

* 스칼라 함수, 집계 작업 등을 활용하는 계산 또는 수식을 작성하고 관리합니다.
* 탐색, 검색 및 참조를 사용하도록 부모-자식 관계를 정의합니다.
* 인스턴스 *필드로*정의된 인스턴스와 연결된 속성을 정의하고 이를 사용하여 계층을 만듭니다.

### <a name="components"></a>구성 요소

열렬 모델에는 세 가지 핵심 구성 요소가 있습니다.

* [타임시리즈 모델 인스턴스](#time-series-model-instances)
* [타임시리즈 모델 계층 구조](#time-series-model-hierarchies)
* [시계열 모델 형식](#time-series-model-types)

이러한 구성 요소는 결합되어 열렬 모델을 지정하고 Azure Time Series Insights 데이터를 구성합니다.

[![타임시리즈 모델 개요 차트](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

타임시리즈 [인사이트 미리 보기](time-series-insights-update-how-to-tsm.md) 인터페이스를 통해 타임시리즈 모델을 만들고 관리할 수 있습니다. 타임시리즈 모델 설정은 [모델 설정 API를](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)통해 관리할 수 있습니다.

## <a name="time-series-model-instances"></a>시계열 모델 인스턴스

시간계 모델 *인스턴스는* 타임계 자체의 가상 표현입니다.

대부분의 경우 인스턴스는 **deviceId** 또는 **assetId로**고유하게 식별되며 이 ID는 타임계 ID로 저장됩니다.

인스턴스에는 타임계 ID, 유형, 이름, 설명, 계층 및 인스턴스 필드와 같은 *인스턴스 속성이라고*하는 설명 정보가 있습니다. 인스턴스 속성에는 적어도 계층 구조 정보가 포함됩니다.

*인스턴스 필드는* 계층 수준뿐만 아니라 제조업체, 운영자 등의 값을 포함할 수 있는 설명 정보 모음입니다.

이벤트 소스가 타임시리즈 Insights 환경에 대해 구성되면 인스턴스가 자동으로 검색되고 타임시리즈 모델에서 생성됩니다. 타임시리즈 모델 쿼리를 사용하여 타임시리즈 인사이트 탐색기를 통해 인스턴스를 만들거나 업데이트할 수 있습니다.

[Contoso 풍력 발전 단지 데모는](https://insights.timeseries.azure.com/preview/samples) 몇 가지 라이브 인스턴스 예제를 제공합니다.

[![타임시리즈 모델 인스턴스 예제](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>인스턴스 속성

인스턴스는 **timeSeriesId**, **typeId**, **이름,** **설명,** 계층 Id 및 **인스턴스필드로** **정의됩니다.** 각 인스턴스는 하나의 *유형과*하나 이상의 계층구조에만 *매핑됩니다.*

| 속성 | 설명 |
| --- | ---|
| 타임시리즈ID | 인스턴스가 연결된 시간계의 UUID입니다. |
| typeId | 타임시리즈 모델의 UUID는 인스턴스가 연관된 유형을 입력합니다. 기본적으로 검색된 모든 새 인스턴스는 기본 형식에 연결됩니다.
| name | **name** 속성은 선택 사항이며 대/소문자를 구분합니다. **이름을** 사용할 수 없는 경우 기본적으로 **timeSeriesId**. 이름이 제공되면 **timeSeriesId는** [웰에서](time-series-insights-update-explorer.md#4-time-series-well)계속 사용할 수 있습니다. |
| description | 인스턴스에 대한 텍스트 설명입니다. |
| 계층 ID | 인스턴스가 속한 계층을 정의합니다. |
| 인스턴스 필드 | 인스턴스의 속성및 인스턴스를 정의하는 정적 데이터입니다. 계층 구조 또는 비 계층 구조의 값을 정의하는 한편 검색 작업을 수행하기 위한 인덱싱도 지원합니다. |

> [!NOTE]
> 계층은 인스턴스 필드를 사용하여 빌드됩니다. 추가 인스턴스 속성 정의에 대 한 추가 **인스턴스 필드를** 추가할 수 있습니다.

인스턴스에는 다음과 같은 JSON 표현이 있습니다.

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
> [서비스화] 인사이트 인스턴스 API를 만들고, 읽고, 업데이트하고, 삭제(CRUD) 지원을 위해 [데이터 쿼리](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) 문서와 인스턴스 API [REST 문서를](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)읽어보십시오.

## <a name="time-series-model-hierarchies"></a>시계열 모델 계층 구조

시간계 모델 계층구조는 속성 이름과 해당 관계를 지정하여 인스턴스를 *구성합니다.*

지정된 시간계 인사이트 환경에서 여러 계층을 구성할 수 있습니다. 타임 시리즈 모델 인스턴스는 단일 계층 또는 여러 계층(다대다 관계)에 매핑할 수 있습니다.

[Contoso Wind Farm 데모](https://insights.timeseries.azure.com/preview/samples) 클라이언트 인터페이스는 표준 인스턴스 및 형식 계층 구조를 표시합니다.

[![타임시리즈 모델 계층 구조 예제](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>계층 구조 정의

계층 구조는 계층 **ID,** **이름**및 **소스로**정의됩니다.

| 속성 | 설명 |
| ---| ---|
| id | 예를 들어 인스턴스를 정의할 때 사용되는 계층 구조의 고유 식별자입니다. |
| name | 계층 구조에 대한 이름을 제공하는 데 사용되는 문자열입니다. |
| source | 사용자가 만들려는 계층의 하향식 부모 자식 순서인 조직 계층 또는 경로를 지정합니다. 부모-자식 속성은 인스턴스 필드로 매핑됩니다. |

계층구조는 JSON에서 다음과 같이 표시됩니다.

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

이전 JSON 예제에서는 다음과 같은

* `Location`부모 `states` 및 자식으로 `cities`계층 구조를 정의합니다. 각각에는 `location` 여러 `states`개의 을 가질 `cities`수 있으며, 차례로 여러 을 가질 수 있습니다.
* `ManufactureDate`부모 `year` 및 자식으로 `month`계층 구조를 정의합니다. 각각에는 `ManufactureDate` 여러 `years`개의 을 가질 `months`수 있으며, 차례로 여러 을 가질 수 있습니다.

> [!TIP]
> [서비스화] 인사이트 인스턴스 API 및 CRUD 지원은 [데이터 쿼리](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) 문서 및 계층 API [REST 문서를](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)참조하십시오.

### <a name="hierarchy-example"></a>계층 예

계층 구조 **instanceFieldNames** **H1이** `building`있는 `floor`예제를 `room` 고려합니다.

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

이전 정의및 여러 열류에 사용된 인스턴스 필드를 감안할 때 계층 구조 특성 및 값은 다음 표에 표시된 대로 표시됩니다.

| Time Series ID | 인스턴스 필드 |
| --- | --- |
| ID1 | "건물" = "1000", "바닥" = "10", "방" = "55"  |
| ID2 | "건물" = "1000", "방" = "55" |
| ID3 | "바닥" = "10" |
| ID4 | "건물" = "1000", "바닥" = "10"  |
| ID5 | "건물", "바닥" 또는 "룸"은 설정되지 않습니다. |

열계 **ID1** 및 **ID4는** 건물, *바닥*및 *룸* 매개 변수를 완전히 정의하고 올바르게 *정렬했기*때문에 Azure Time Series [Insights 탐색기에서](time-series-insights-update-explorer.md) 계층 구조 **H1의** 일부로 표시됩니다.

다른 것들은 지정된 데이터 계층 구조를 따르지 않기 때문에 *부모가 없는 인스턴스로* 분류됩니다.

## <a name="time-series-model-types"></a>시계열 모델 형식

시계열 모델 *형식*은 계산을 수행하기 위한 변수 또는 수식을 정의하는 데 도움이 됩니다. 형식은 특정 타임시리즈 인사이트 인스턴스와 연결됩니다.

형식에는 하나 이상의 변수가 포함될 수 있습니다. 예를 들어, 타임시리즈 모델 인스턴스는 변수 *평균 온도,* *최소 온도*및 *최대 온도로*구성된 온도 *센서*형식일 수 있습니다.

[Contoso 풍력 발전 단지 데모는](https://insights.timeseries.azure.com/preview/samples) 해당 인스턴스와 연관된 여러 타임시리즈 모델 유형을 시각화합니다.

[![타임시리즈 모델 유형 예제](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> [서비스화] 인사이트 인스턴스 API 및 CRUD 지원은 [데이터 쿼리](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) 문서 및 API REST [유형 설명서를](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)참조하십시오.

### <a name="type-properties"></a>형식 속성

시간계 모델 유형은 **id,** **이름,** **설명**및 **변수로 정의됩니다.**

| 속성 | 설명 |
| ---| ---|
| id | 형식의 UUID입니다. |
| name | 형식의 이름을 제공하는 데 사용되는 문자열입니다. |
| description | 형식에 대한 문자열 설명입니다. |
| variables | 형식과 연결된 변수를 지정합니다. |

형식은 다음 JSON 예제를 준수합니다.

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
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>변수

Time Series Insights 유형에는 이벤트에 대한 수식 및 계산 규칙을 지정하는 많은 변수가 있을 수 있습니다.

각 변수는 *숫자,* *범주형*및 *집계의*세 가지 종류 중 하나일 수 *있습니다.*

* **숫자** 종류는 연속 값으로 작동합니다. 
* **범주형** 종류는 정의된 개별 값 집합으로 작동합니다.
* **집계** 값은 단일 종류의 여러 변수를 결합합니다(모든 숫자 또는 모든 범주형).

다음 표에는 각 변수 종류와 관련된 속성이 표시됩니다.

[![타임시리즈 모델 변수 테이블](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>숫자 변수

| 가변 속성 | 설명 |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려되는 행 수를 제한하는 선택적 조건절입니다. |
| 변수 값 | 장치 또는 센서에서 오거나 타임시리즈 표현식을 사용하여 변환된 계산에 사용되는 원격 분석 값입니다. 숫자 종류 변수는 *Double*형식이어야 합니다.|
| 가변 보간 | 보간은 기존 데이터를 사용하여 신호를 재구성하는 방법을 지정합니다. *숫자* 변수에 대해 단계 및 *선형* 보간 옵션을 사용할 수 있습니다. |
| 변수 집계 | *평균,* *최소,* *최대,* *합계,* *카운트,* *첫 번째,* *마지막* 및 시간 가중 *(평균,* *최소,* *최대,* *합계,* *왼쪽)* 연산을 통해 계산을 지원합니다. |

변수는 다음 JSON 예제를 준수합니다.

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>범주형 변수

| 가변 속성 | 설명 |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려되는 행 수를 제한하는 선택적 조건절입니다. |
| 변수 값 | 장치 또는 센서에서 계산하는 데 사용되는 원격 분석 값입니다. 범주형 종류 변수는 *Long* 또는 *String이어야*합니다. |
| 가변 보간 | 보간은 기존 데이터를 사용하여 신호를 재구성하는 방법을 지정합니다. *단계* 보간 옵션은 범주형 변수에 사용할 수 있습니다. |
| 변수 범주 | 범주는 장치 또는 센서에서 레이블로 오는 값 간의 매핑을 만듭니다. |
| 가변 기본 범주 | 기본 범주는 "범주" 속성에 매핑되지 않는 모든 값에 대한 것입니다. |

변수는 다음 JSON 예제를 준수합니다.

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>집계 변수

| 가변 속성 | 설명 |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려되는 행 수를 제한하는 선택적 조건절입니다. |
| 변수 집계 | *평균,* *최소,* *최대,* *합계,* *카운트,* *첫 번째,* *마지막을*통해 계산을 지원합니다. |

변수는 다음 JSON 예제를 준수합니다.

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

변수는 열렬 모델의 형식 정의에 저장되며 [쿼리 API를](time-series-insights-update-tsq.md) 통해 인라인으로 제공되어 저장된 정의를 재정의할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 읽기 [Azure Time Series 인사이트 미리 보기 저장소 및 시작.](./time-series-insights-update-storage-ingress.md)

- Azure Time Series [인사이트 미리 보기에서 데이터 모델링에서](./time-series-insights-update-how-to-tsm.md) 일반적인 열렬 모델 작업에 대해 알아보기

- 새로운 [타임시리즈 모델](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) 참조 문서를 읽어보십시오.
