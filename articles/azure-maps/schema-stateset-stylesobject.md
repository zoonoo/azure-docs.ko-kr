---
title: 동적 Azure Maps에 대한 StylesObject 스키마 참조 가이드
description: 동적 Azure Maps StylesObject 스키마 및 구문에 대한 참조 가이드입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 08379e66c97d34eea53410190475e90e156a58e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903346"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>동적 Maps에 대한 StylesObject 스키마 참조 가이드

> [!IMPORTANT]
> Azure Maps Creator 서비스는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

 `StylesObject`는 상태 세트 스타일을 나타내는 `StyleObject` 배열입니다. Azure Maps Creator(미리 보기) [기능 상태 서비스](/rest/api/maps/featurestate)를 사용하여 실내 맵 데이터 기능에 상태 집합 스타일을 적용합니다. 상태 집합 스타일을 만들고 실내 맵 기능과 연결한 후에는 이 스타일을 사용하여 동적 실내 맵을 만들 수 있습니다. 동적 실내 맵을 만드는 방법에 대한 자세한 내용은 [Creator 실내 맵에 대한 동적 스타일 지정 구현](indoor-map-dynamic-styling.md)을 참조하세요.

## <a name="styleobject"></a>StyleObject

`StyleObject`는 다음 스타일 규칙 중 하나입니다.

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

아래 JSON은 세 가지 스타일 형식을 각각 사용하는 예제를 보여줍니다.  `BooleanTypeStyleRule`은 `occupied` 속성이 true 및 false인 기능의 동적 스타일을 결정하는 데 사용됩니다.  `NumericTypeStyleRule`은 `temperature` 속성이 특정 범위 내에 속하는 기능의 스타일을 결정하는 데 사용됩니다. 마지막으로 `StringTypeStyleRule`은 특정 스타일을 `meetingType`과 일치하는 데 사용됩니다.



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 `NumericTypeStyleRule`은 [`StyleObject`](#styleobject)이며 다음 속성으로 구성됩니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `keyName` | 문자열 | *상태* 또는 동적 속성 이름입니다. `keyName`은 `StyleObject` 배열 내에서 고유해야 합니다.| 예 |
| `type` | 문자열 | 값은 “숫자”입니다. | 예 |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| 연결된 색을 사용하는 숫자 스타일 범위의 배열입니다. 각 범위는 *상태* 값이 범위를 만족하는 경우 사용되는 색을 정의합니다.| 예 |

### <a name="numberruleobject"></a>NumberRuleObject

`NumberRuleObject`는 [`RangeObject`](#rangeobject) 및 `color` 속성으로 구성됩니다. *상태* 값이 범위 내에 속하는 경우 디스플레이 색은 `color` 속성에 지정된 색입니다.

겹치는 범위를 여러 개 정의하는 경우 선택한 색은 충족되는 첫 번째 범위에서 정의된 색이 됩니다.

다음 JSON 샘플에서는 *상태* 값이 50-60 사이인 경우 두 범위 모두 true가 됩니다. 하지만 사용되는 색은 충족된 목록의 첫 번째 범위이므로 `#343deb`입니다.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject)는 논리 범위 조건 집합을 정의하며 `true`일 경우 *상태* 디스플레이 색을 `color` 속성에 지정된 색으로 변경합니다. `range`가 지정되지 않은 경우 `color` 속성에서 정의된 색이 항상 사용됩니다.   | 예 |
| `color` | 문자열 | 상태 값이 범위 내에 속하는 경우 사용할 색입니다. `color` 속성은 다음 형식 중 하나에 해당하는 JSON 문자열입니다. <ul><li> HTML 스타일의 16진수 값 </li><li> RGB("#ff0", "#ffff00", "rgb(255, 255, 0)")</li><li> RGBA("rgba(255, 255, 0, 1)")</li><li> HSL("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> 노랑, 파랑 등의 미리 정의된 HTML 색 이름.</li></ul> | 예 |

### <a name="rangeobject"></a>RangeObject

`RangeObject`는 [`NumberRuleObject`](#numberruleobject)의 숫자 범위 값을 정의합니다. *상태* 값이 범위에 속하도록 하려면 정의된 모든 조건이 true여야 합니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `minimum` | double | x ≥ `minimum`인 모든 숫자 x.| 예 |
| `maximum` | double | x ≥ `maximum`인 모든 숫자 x. | 예 |
| `exclusiveMinimum` | double | x > `exclusiveMinimum`인 모든 숫자 x.| 예 |
| `exclusiveMaximum` | double | x < `exclusiveMaximum`인 모든 숫자 x.| 예 |

### <a name="example-of-numerictypestylerule"></a>NumericTypeStyleRule의 예

다음 JSON은 이름이 `temperature`인 `NumericTypeStyleRule` *상태* 를 보여줍니다. 이 예제에서는 [`NumberRuleObject`](#numberruleobject)에 정의된 온도 범위 2개와 연결된 색 스타일이 포함됩니다. 온도 범위가 50-69인 경우 디스플레이에 `#343deb` 색을 사용해야 합니다.  온도 범위가 31-70인 경우 디스플레이에 `#eba834` 색을 사용해야 합니다.

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStyleRule

`StringTypeStyleRule`은 [`StyleObject`](#styleobject)이며 다음 속성으로 구성됩니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `keyName` | 문자열 |  *상태* 또는 동적 속성 이름입니다.  `keyName`은 `StyleObject` 배열 내에서 고유해야 합니다.| 예 |
| `type` | 문자열 |값은 "문자열"입니다. | 예 |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| N개의 *상태* 값 배열입니다.| 예 |

### <a name="stringruleobject"></a>StringRuleObject

`StringRuleObject`는 기능 속성의 가능한 문자열 값인 최대 N개의 상태 값으로 구성됩니다. 기능 속성 값이 정의된 상태 값과 일치하지 않으면 해당 기능에 동적 스타일이 포함되지 않습니다. 중복된 상태 값이 지정된 경우 첫 번째 값이 우선적으로 적용됩니다.

문자열 값 일치는 대/소문자를 구분합니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `stateValue1` | 문자열 | 값 문자열이 stateValue1일 때의 색입니다. | 예 |
| `stateValue2` | 문자열 | 값 문자열이 stateValue일 때의 색입니다. | 예 |
| `stateValueN` | 문자열 | 값 문자열이 stateValueN일 때의 색입니다. | 예 |

### <a name="example-of-stringtypestylerule"></a>StringTypeStyleRule의 예

다음 JSON은 특정 회의 형식과 연결된 스타일을 정의하는 `StringTypeStyleRule`을 보여줍니다.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

`BooleanTypeStyleRule`은 [`StyleObject`](#styleobject)이며 다음 속성으로 구성됩니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `keyName` | 문자열 |  *상태* 또는 동적 속성 이름입니다.  `keyName`은 `StyleObject` 배열 내에서 고유해야 합니다.| 예 |
| `type` | 문자열 |값은 "부울"입니다. | 예 |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)[1]| `true` 및 `false` *상태* 값에 대한 색이 있는 부울 쌍입니다.| 예 |

### <a name="booleanruleobject"></a>BooleanRuleObject

`BooleanRuleObject`는 `true` 및 `false` 값에 대한 색을 정의합니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `true` | 문자열 | *상태* 값이 `true`인 경우 사용할 색입니다. `color` 속성은 다음 형식 중 하나에 해당하는 JSON 문자열입니다. <ul><li> HTML 스타일의 16진수 값 </li><li> RGB("#ff0", "#ffff00", "rgb(255, 255, 0)")</li><li> RGBA("rgba(255, 255, 0, 1)")</li><li> HSL("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> 노랑, 파랑 등의 미리 정의된 HTML 색 이름.</li></ul>| 예 |
| `false` | 문자열 | *상태* 값이 `false`인 경우 사용할 색입니다. | 예 |

### <a name="example-of-booleantypestylerule"></a>BooleanTypeStyleRule의 예

다음 JSON은 이름이 `occupied`인 `BooleanTypeStyleRule` *상태* 를 보여줍니다. [`BooleanRuleObject`](#booleanruleobject)는 `true` 및 `false` 값에 대한 색을 정의합니다.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```