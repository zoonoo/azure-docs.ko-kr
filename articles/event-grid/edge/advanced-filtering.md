---
title: 고급 필터링 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드에서 고급 필터링.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992562"
---
# <a name="advanced-filtering"></a>고급 필터링
이벤트 그리드를 사용하면 json 페이로드의 모든 속성에 필터를 지정할 수 있습니다. 이러한 필터는 `AND` 조건 집합으로 모델링되며 각 외부 조건은 선택적 내부 `OR` 조건을 가집니다. 각 `AND` 조건에 대해 다음 값을 지정합니다.

* `OperatorType`- 비교 유형.
* `Key`- 필터를 적용할 속성에 대한 json 경로입니다.
* `Value`- 필터가 실행되는 참조 값(또는) `Values` - 필터가 실행되는 참조 값 집합입니다.

## <a name="json-syntax"></a>JSON 구문

고급 필터에 대한 JSON 구문은 다음과 같습니다.

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>배열 값 필터링

Event Grid는 현재 값 배열에 대한 필터링을 지원하지 않습니다. 들어오는 이벤트에 고급 필터 키에 대한 배열 값이 있는 경우 일치 작업이 실패합니다. 들어오는 이벤트는 이벤트 구독과 일치하지 않습니다.

## <a name="and-or-not-semantics"></a>및-또는-하지 의미 체계

앞에서 언급한 json 예제에서는 `AdvancedFilters` 배열입니다. 각 `AdvancedFilter` 배열 요소를 `AND` 조건으로 간주합니다.

`NumberIn`여러 값(예: `NumberNotIn`" 등)을 `StringIn`지원하는 연산자의 경우 각 `OR` 값은 조건으로 처리됩니다. 따라서 `StringBeginsWith("a", "b", "c")` a는 `a` 또는 `b` 또는 `c`로 시작하는 모든 문자열 값과 일치합니다.

> [!CAUTION]
> NOT 연산자 `NumberNotIn` `StringNotIn` - `Values` 필드에 지정된 각 값에 대한 AND 조건으로 행동합니다.
>
> 이렇게 하지 않을 경우 필터는 모두 허용 필터가 되고 필터링의 목적이 무효화됩니다.

## <a name="floating-point-rounding-behavior"></a>부동 점 반올림 동작

Event Grid는 `decimal` .NET 형식을 사용하여 모든 숫자 값을 처리합니다. 이벤트 구독 JSON에 지정된 숫자 값은 부동 소수점 반올림 동작의 적용을 받지 않습니다.

## <a name="case-sensitivity-of-string-filters"></a>문자열 필터의 대/소문자 민감도

모든 문자열 비교는 대/소문자를 구분하지 않습니다. 오늘날이 동작을 변경할 수 있는 방법은 없습니다.

## <a name="allowed-advanced-filter-keys"></a>고급 필터 키 허용

속성은 `Key` 잘 알려진 최상위 속성이거나 여러 점이 있는 json 경로일 수 있으며 각 점은 중첩된 json 개체로 단계별로 들어갑니다.

이벤트 그리드는 JSONPath 사양과 `$` 달리 키의 문자에 특별한 의미가 없습니다.

### <a name="event-grid-schema"></a>이벤트 그리드 스키마

이벤트 그리드 스키마의 이벤트의 경우:

* ID
* 항목
* 제목
* EventType
* DataVersion
* 데이터.소품1
* 데이터.소품*소품2.소품3.Prop4.Prop5

### <a name="custom-event-schema"></a>사용자 지정 이벤트 스키마

Event Grid는 페이로드에 `Key` 봉투 스키마를 적용하지 않으므로 사용자 지정 이벤트 스키마에는 제한이 없습니다.

## <a name="numeric-single-value-filter-examples"></a>숫자 단일 값 필터 예제

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>숫자 범위-값 필터 예제

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>문자열 범위 값 필터 예제

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>부울 단일 값 필터 예제

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
