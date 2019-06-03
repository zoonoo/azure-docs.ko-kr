---
title: Azure Time Series Insights 미리 보기의 시계열 모델 | Microsoft Docs
description: Azure Time Series Insights 시계열 모델을 이해합니다.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 3e6e8ae76c0ae6f688dd4a039b34c52af16b6e0f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244021"
---
# <a name="time-series-model"></a>시계열 모델

이 문서에서는 Azure Time Series Insights 미리 보기의 시계열 모델 부분을 설명합니다. 모델 자체, 해당 기능, 사용자 고유의 모델을 빌드하고 업데이트하는 방법을 설명합니다.

전통적으로 IoT 디바이스에서 수집되는 데이터는 컨텍스트 정보가 부족하기 때문에 신속하게 센서를 찾아서 분석하기가 어렵습니다. 시계열 모델을 사용하는 주요 이유는 IoT 데이터를 쉽게 찾아서 분석하는 것입니다. 시계열 모델은 소비자가 즉시 사용 가능한 데이터 세트를 준비하기 위해 시계열 데이터를 큐레이션, 유지 관리 및 보강하여 이 목표를 달성합니다.

시계열 모델은 디바이스 및 비 디바이스 엔터티를 컨텍스트화하기 때문에 쿼리 및 탐색에서 중요한 역할을 합니다. 시계열 모델에 유지되는 데이터는 저장된 수식을 활용하여 시계열 쿼리 계산을 구동합니다.

[![시간 시계열 모델 개요](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>주요 기능

시계열 컨텍스트화를 간단하고 손쉽게 관리하기 위해 개발된 시계열 모델은 Time Series Insights 미리 보기에서 다음과 같은 기능을 지원합니다. 다음과 같은 도움을 줍니다.

* 계산 또는 수식을 작성 및 관리하고, 스칼라 함수를 활용하여 데이터를 변환하고, 작업을 집계하는 등 여러 가지를 할 수 있습니다.
* 탐색 및 참조를 사용하고 시계열 원격 분석에 컨텍스트를 제공하는 부모-자식 관계를 정의합니다.
* *인스턴스 필드*의 인스턴스 부분과 연결된 속성을 정의하고 계층 구조를 만드는 데 사용합니다.

## <a name="entity-components"></a>엔터티 구성 요소

시계열 모델에는 세 가지 핵심 구성 요소가 있습니다.

* <a href="#time-series-model-types">시간 시계열 모델 형식</a>
* <a href="#time-series-model-hierarchies">시간 시계열 모델 계층</a>
* <a href="#time-series-model-instances">시간 시계열 모델 인스턴스</a>

이러한 구성 요소는 시계열 모델을 지정 하 고 Azure Time Series Insights 데이터를 구성에 결합 됩니다.

## <a name="time-series-model-types"></a>시계열 모델 형식

시계열 모델 *형식*은 계산을 수행하기 위한 변수 또는 수식을 정의하는 데 도움이 됩니다. 형식은 특정 Time Series Insights 인스턴스와 연결됩니다. 형식에는 하나 이상의 변수가 포함될 수 있습니다. 예를 들어 Time Series Insights 인스턴스는 변수 *평균 온도*, *최소 온도* 및 *최대 온도*로 구성되는 *온도 센서* 형식일 수 있습니다. 데이터가 Time Series Insights로 흐르기 시작하면 기본 형식이 생성됩니다. 기본 형식은 모델 설정에서 검색하고 업데이트할 수 있습니다. 기본 형식에는 이벤트 수를 집계하는 변수가 있습니다.

### <a name="time-series-model-type-json-example"></a>시계열 모델 형식 JSON 예제

샘플:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

시계열 모델 형식에 대한 자세한 내용은 [참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)를 참조하세요.

### <a name="variables"></a>variables

Time Series Insights 형식에는 변수가 있으며, 이러한 변수는 이벤트의 값에 대해 명명되는 계산입니다. Time Series Insights 변수 정의는 수식 및 계산 규칙을 포함합니다. 변수 정의는 *종류*, *값*, *필터*, *감소* 및 *경계*를 포함합니다. 변수는 시계열 모델의 형식 정의에 저장되며, Query API를 통해 인라인으로 제공하여 저장된 정의를 재정의할 수 있습니다.

다음 행렬은 변수 정의에 대한 범례로 작동합니다.

[![변수 정의 테이블 형식](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| 정의 | 설명 |
| --- | ---|
| 변수 종류 |  *숫자* 하 고 *집계* 종류는 지원 |
| 변수 필터 | 변수 필터는 조건에 따라 계산에 넣을 행 수를 제한하는 선택적 필터 절을 지정합니다. |
| 변수 값 | 변수 값은 계산에 사용되며 또 그래야 합니다. 관련 필드에 데이터 요소에 대 한 참조입니다. |
| 변수 집계 | 변수의 집계 함수는 계산의 일부를 가능하게 합니다. Time Series Insights는 일반 집계(예: *최소*, *최대*, *평균*, *합계*, *개수*)를 지원합니다. |

## <a name="time-series-model-hierarchies"></a>시계열 모델 계층 구조

계층 구조는 속성 이름 및 해당 관계를 지정하여 인스턴스를 구성합니다. 계층 구조는 하나일 수도 있고 여러 개일 수도 있습니다. 데이터의 현재 부분일 필요는 없지만, 각 인스턴스가 계층 구조에 매핑되어야 합니다. 시계열 모델 인스턴스는 단일 또는 여러 계층 구조에 매핑될 수 있습니다.

계층 구조는 *계층 구조 ID*, *이름* 및 *원본*을 통해 정의됩니다. 계층 구조에는 사용자가 만들려고 하는 하향식 부모-자식 계층 경로 순서인 경로가 있습니다. 부모-자식 속성은 *인스턴스 필드*로 매핑됩니다.

### <a name="time-series-model-hierarchy-json-example"></a>시계열 모델 계층 구조 JSON 예제

샘플:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

시계열 모델 계층 구조에 대한 자세한 내용은 [참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)를 참조하세요.

### <a name="hierarchy-definition-behavior"></a>계층 구조 정의 동작

계층 구조 H1의 정의에 *building*, *floor* 및 *room*이 포함된 다음 예제를 살펴보세요.

```plaintext
 H1 = [“building”, “floor”, “room”]
```

*인스턴스 필드*에 따라 계층 구조 특성 및 값이 다음 표에 나와 있는 것처럼 표시됩니다.

| Time Series ID | 인스턴스 필드 |
| --- | --- |
| ID1 | “building” = “1000”, “floor” = “10”, “room” = “55”  |
| ID2 | “building” = “1000”, “room” = “55” |
| ID3 | “floor” = “10” |
| ID4 | “building” = “1000”, “floor” = “10”  |
| ID5 | “building”, “floor” 또는 “room”이 설정되지 않습니다. |

위의 예에서 **ID1** 하 고 **ID4** H1 Azure Time Series Insights 탐색기에서 계층의 일부로 표시 하 고 나머지는 분류 *부모가 아닌 인스턴스* 지정 된 데이터 계층에 준수 하지 있습니다.

## <a name="time-series-model-instances"></a>시계열 모델 인스턴스

인스턴스는 시계열 자체입니다. 대부분의 경우 *deviceId* 또는 *assetId*는 환경 내 자산의 고유 식별자입니다. 각 인스턴스에는 자신에 대한 구체적인 정보가 연결되는데, 이를 인스턴스 속성이라 합니다. 인스턴스 속성에는 적어도 계층 구조 정보가 포함됩니다. 제조업체, 운영자, 마지막 서비스 날짜 같은 유용하고 구체적인 데이터도 포함될 수 있습니다.

인스턴스는 *typeId*, *timeSeriesId*, *name*, *description*, *hierarchyIds* 및 *instanceFields*에 의해 정의됩니다. 각 인스턴스는 한 가지 *형식*과 하나 이상의 계층 구조에 매핑됩니다. 인스턴스는 계층 구조의 모든 속성을 상속하며, *instanceFields*를 추가하여 인스턴스 속성을 더욱 구체적으로 정의할 수 있습니다.

*instanceFields*는 인스턴스 그리고 인스턴스를 정의하는 정적 데이터의 속성입니다. 계층 구조 또는 비 계층 구조의 값을 정의하는 한편 검색 작업을 수행하기 위한 인덱싱도 지원합니다.

합니다 *이름을* 속성은 선택 사항이 며 대/소문자 구분 합니다. *name*을 사용할 수 없는 경우 기본값은 시계열 ID입니다. *name*이 제공되는 경우 시계열 ID는 Well(탐색기의 차트 아래에 있는 그리드)에서 계속 사용할 수 있습니다.

### <a name="time-series-model-instance-json-example"></a>시계열 모델 인스턴스 JSON 예제

샘플:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

시계열 모델 인스턴스에 대한 자세한 내용은 [참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)를 참조하세요.

### <a name="time-series-model-settings-example"></a>시계열 모델 설정 예제

샘플:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

시계열 모델 설정에 대한 자세한 내용은 [참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Time Series Insights 미리 보기 스토리지 및 수신](./time-series-insights-update-storage-ingress.md)에 대해 알아보세요.

- 새로운 [시계열 모델](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)에 대해 알아보세요.