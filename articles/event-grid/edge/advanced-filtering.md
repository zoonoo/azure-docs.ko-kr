---
title: 고급 필터링-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge에서 Event Grid 고급 필터링.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171774"
---
# <a name="advanced-filtering"></a>고급 필터링
Event Grid json 페이로드의 모든 속성에 필터를 지정할 수 있습니다. 이러한 필터는 조건 집합으로 모델링 되며 `AND` 각 외부 조건은 선택적 내부 `OR` 조건이 있습니다. 각 조건에 대해 `AND` 다음 값을 지정 합니다.

* `OperatorType`-비교 유형입니다.
* `Key`-필터를 적용할 속성의 json 경로입니다.
* `Value`-필터가 실행 되는 참조 값 (또는) `Values` -필터가 실행 되는 참조 값의 집합입니다.

## <a name="json-syntax"></a>JSON 구문

고급 필터에 대 한 JSON 구문은 다음과 같습니다.

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

## <a name="filtering-on-array-values"></a>배열 값에 대 한 필터링

Event Grid은 현재 값 배열에 대 한 필터링을 지원 하지 않습니다. 들어오는 이벤트에 고급 필터의 키에 대 한 배열 값이 있으면 일치 작업이 실패 합니다. 들어오는 이벤트가 이벤트 구독과 일치 하지 않습니다.

## <a name="and-or-not-semantics"></a>및-또는-의미 체계가 아님

이전에 지정 된 json 예제에서는 `AdvancedFilters` 배열입니다. 각 `AdvancedFilter` 배열 요소를 조건으로 간주 `AND` 합니다.

여러 값 (예:,, 등)을 지 원하는 연산자의 경우 `NumberIn` `NumberNotIn` `StringIn` 각 값은 조건으로 처리 됩니다. `OR` 따라서는 `StringBeginsWith("a", "b", "c")` 또는 또는로 시작 하는 임의의 문자열 값과 일치 합니다 `a` `b` `c` .

> [!CAUTION]
> NOT 연산자- `NumberNotIn` and는 `StringNotIn` 필드에 지정 된 각 값에서 및 조건으로 동작 `Values` 합니다.
>
> 이렇게 하지 않으면 모두 수락 필터로 설정 되 고 필터링의 목적이 무효화 됩니다.

## <a name="floating-point-rounding-behavior"></a>부동 소수점 반올림 동작

Event Grid는 .Net 형식을 사용 하 여 `decimal` 모든 숫자 값을 처리 합니다. 이벤트 구독 JSON에 지정 된 숫자 값에는 부동 소수점 반올림 동작이 적용 되지 않습니다.

## <a name="case-sensitivity-of-string-filters"></a>문자열 필터의 대/소문자 구분

모든 문자열 비교는 대/소문자를 구분 하지 않습니다. 현재이 동작을 변경할 수 있는 방법은 없습니다.

## <a name="allowed-advanced-filter-keys"></a>허용 되는 고급 필터 키

`Key`속성은 잘 알려진 최상위 속성 이거나 여러 점이 있는 json 경로일 수 있습니다. 여기서 각 점은 중첩 된 json 개체의 단계별 실행을 의미 합니다.

`$`JSONPath 사양과 달리 Event Grid는 키의 문자에 대 한 특별 한 의미가 없습니다.

### <a name="event-grid-schema"></a>Event grid 스키마

Event Grid 스키마의 이벤트:

* ID
* 토픽
* 제목
* EventType
* DataVersion
* Prop1
* Prop2 * Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>사용자 지정 이벤트 스키마

`Key`Event Grid는 페이로드에 봉투 (envelope) 스키마를 적용 하지 않으므로 사용자 지정 이벤트 스키마에는 제한이 없습니다.

## <a name="numeric-single-value-filter-examples"></a>숫자 단일 값 필터 예

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

## <a name="numeric-range-value-filter-examples"></a>숫자 범위-값 필터 예

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

## <a name="string-range-value-filter-examples"></a>문자열 범위-값 필터 예

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

## <a name="boolean-single-value-filter-examples"></a>부울 단일 값 필터 예

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
