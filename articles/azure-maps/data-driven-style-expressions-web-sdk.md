---
title: Azure 지도 웹 SDK의 데이터 기반 스타일 표현식 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK에서 데이터 기반 스타일 식을 사용하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d6009a655adcc26ebef31588eff2332a05f3a001
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804727"
---
# <a name="data-driven-style-expressions-web-sdk"></a>데이터 기반 스타일 표현식(웹 SDK)

식을 사용하면 데이터 원본의 각 셰이프에 정의된 속성을 관찰하는 스타일 지정 옵션에 비즈니스 논리를 적용할 수 있습니다. 식은 데이터 원본 또는 레이어에서 데이터를 필터링할 수 있습니다. 식은 if-문과 같은 조건부 논리로 구성될 수 있습니다. 또한 문자열 연산자, 논리 연산자 및 수학 연산자 등을 사용하여 데이터를 조작하는 데 사용할 수 있습니다.

데이터 기반 스타일은 스타일 지정과 관련된 비즈니스 논리를 구현하는 데 필요한 코드의 양을 줄입니다. 레이어와 함께 사용하면 표현식은 별도의 스레드에서 렌더링 시 평가됩니다. 이 기능은 UI 스레드에서 비즈니스 논리를 평가하는 것에 비해 성능이 향상됩니다.

이 비디오에서는 Azure Maps Web SDK에서 데이터 기반 스타일에 대한 개요를 제공합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

표현식은 JSON 배열로 표시됩니다. 배열에서 식의 첫 번째 요소는 식 연산자의 이름을 지정하는 문자열입니다. 예를 들어 "+" 또는 "대/소문자"입니다. 다음 요소(있는 경우)는 식에 대한 인수입니다. 각 인수는 리터럴 값(문자열, 숫자, 부울 `null`또는) 또는 다른 식 배열입니다. 다음 의사 코드는 식의 기본 구조를 정의합니다. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps 웹 SDK는 다양한 유형의 식을 지원합니다. 식은 단독으로 사용하거나 다른 식과 함께 사용할 수 있습니다.

| 식의 유형 | Description |
|---------------------|-------------|
| [집계 식](#aggregate-expression) | 데이터 집합을 통해 처리되고 `clusterProperties` `DataSource`의 옵션과 함께 사용할 수 있는 계산을 정의하는 식입니다. |
| [부울 식](#boolean-expressions) | 부울 식은 부울 비교를 평가하기 위한 부울 연산자 식 집합을 제공합니다. |
| [색상 표현식](#color-expressions) | 색상 표현식을 사용하면 색상 값을 쉽게 만들고 조작할 수 있습니다. |
| [조건부 식](#conditional-expressions) | 조건식은 if-문과 같은 논리 연산을 제공합니다. |
| [데이터 표현식](#data-expressions) | 피쳐의 속성 데이터에 대한 액세스를 제공합니다. |
| [보간 및 단계 표현식](#interpolate-and-step-expressions) | 보간 및 단계 표현식을 사용하여 보간된 곡선 또는 단계 함수를 따라 값을 계산할 수 있습니다. |
| [레이어별 표현식](#layer-specific-expressions) | 단일 레이어에만 적용할 수 있는 특수 식입니다. |
| [수학 표현식](#math-expressions) | 수학적 연산자가 식 프레임워크 내에서 데이터 기반 계산을 수행할 수 있도록 합니다. |
| [문자열 연산자 식](#string-operator-expressions) | 문자열 연산자 식은 서비스 케이스를 연결 및 변환하는 등의 문자열에서 변환 작업을 수행합니다. |
| [형식 식](#type-expressions) | 형식 식은 문자열, 숫자 및 부울 값과 같은 다양한 데이터 형식을 테스트하고 변환하는 도구를 제공합니다. |
| [가변 바인딩 식](#variable-binding-expressions) | 변수 바인딩 식은 저장된 값을 다시 계산할 필요 없이 계산 결과를 변수에 저장하고 식의 다른 곳에서 여러 번 참조합니다. |
| [확대/축소 표현식](#zoom-expression) | 렌더링 시 맵의 현재 확대/축소 수준을 검색합니다. |

이 문서의 모든 예제에서는 다음 기능을 사용하여 다양한 유형의 식을 사용할 수 있는 다양한 방법을 보여 줍니다. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>데이터 표현식

데이터 표현식은 피쳐의 속성 데이터에 대한 액세스를 제공합니다. 

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['at', number, array]` | object | 배열에서 항목을 검색합니다. |
| `['geometry-type']` | 문자열 | 점, 다중 점, 라인 스트링, 다중 줄 문자열, 다각형, 다각형, 다각형: 피처의 형상 유형을 가져옵니다. |
| `['get', string]` | 값 | 현재 피쳐의 속성에서 속성 값을 가져옵니다. 요청된 속성이 없는 경우 null을 반환합니다. |
| `['get', string, object]` | 값 | 제공된 개체의 속성에서 속성 값을 가져옵니다. 요청된 속성이 없는 경우 null을 반환합니다. |
| `['has', string]` | boolean | 피쳐의 속성에 지정된 속성이 있는지 확인합니다. |
| `['has', string, object]` | boolean | 개체의 속성에 지정된 속성이 있는지 여부를 확인합니다. |
| `['id']` | 값 | 기능이 있는 경우 기능의 ID를 가져옵니다. |
| `['length', string | array]` | number | 문자열 또는 배열의 길이를 가져옵니다. |
| `['in', boolean | string | number, array]` | boolean | 배열에 항목이 있는지 여부를 결정합니다. |
| `['in', substring, string]` | boolean | 문자열에 하위 문자열이 있는지 여부를 결정합니다. |

**예**

기능의 속성은 `get` 식을 사용하여 식에서 직접 액세스할 수 있습니다. 이 예제에서는 피처의 "zoneColor" 값을 사용하여 거품 레이어의 색상 속성을 지정합니다. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

위의 예제는 모든 점 피처에 속성이 있는 경우 정상적으로 작동합니다. `zoneColor` 그렇지 않으면 색상이 "검은색"으로 대체될 수 있습니다. 대체 색상을 수정하려면 `case` `has` 식과 함께 식을 사용하여 속성이 있는지 확인합니다. 속성이 없는 경우 대체 색상을 반환합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

버블 및 심볼 레이어는 기본적으로 데이터 원본의 모든 셰이프의 좌표를 렌더링합니다. 이 동작은 다각형 또는 선의 정점을 강조 표시할 수 있습니다. 레이어 `filter` 옵션을 사용하여 부울 식 내에서 식을 `['geometry-type']` 사용하여 렌더링하는 피처의 형상 유형을 제한할 수 있습니다. 다음 예제에서는 피처만 `Point` 렌더링되도록 거품 레이어를 제한합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

다음 예제에서는 `Point` 피처와 `MultiPoint` 피처를 모두 렌더링할 수 있습니다. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

마찬가지로 다각형의 윤곽선은 선 레이어에서 렌더링됩니다. 선 레이어에서 이 동작을 사용하지 않도록 설정하려면 `MultiLineString` 허용 및 기능만 허용하는 `LineString` 필터를 추가합니다.  

## <a name="math-expressions"></a>수학 표현식

수학 식은 식 프레임워크 내에서 데이터 기반 계산을 수행하는 수학 연산을 제공합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 지정된 숫자의 합계를 계산합니다. |
| `['-', number]` | number | 지정된 숫자로 0을 뺍니다. |
| `['-', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 뺍니다. |
| `['*', number, number, …]` | number | 지정된 숫자를 함께 곱합니다. |
| `['/', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 나눕니다. |
| `['%', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 나눌 때 나머지를 계산합니다. |
| `['^', number, number]` | number | 두 번째 숫자의 전원으로 발생한 첫 번째 값의 값을 계산합니다. |
| `['abs', number]` | number | 지정된 숫자의 절대 값을 계산합니다. |
| `['acos', number]` | number | 지정된 숫자의 아크코신을 계산합니다. |
| `['asin', number]` | number | 지정된 숫자의 호신을 계산합니다. |
| `['atan', number]` | number | 지정된 숫자의 아크탄선을 계산합니다. |
| `['ceil', number]` | number | 숫자를 다음 전체 정수까지 반올림합니다. |
| `['cos', number]` | number | 지정된 숫자의 코스를 계산합니다. |
| `['e']` | number | 수학 상수를 `e`반환합니다. |
| `['floor', number]` | number | 숫자를 이전 전체 정수로 반올림합니다. |
| `['ln', number]` | number | 지정된 수의 자연 엽을 계산합니다. |
| `['ln2']` | number | 수학 상수를 `ln(2)`반환합니다. |
| `['log10', number]` | number | 지정된 숫자의 기본 10 로그릿헴을 계산합니다. |
| `['log2', number]` | number | 지정된 숫자의 기본 2 로그릿헴을 계산합니다. |
| `['max', number, number, …]` | number | 지정된 숫자 집합의 최대 숫자를 계산합니다. |
| `['min', number, number, …]` | number | 지정된 숫자 집합에서 최소 숫자를 계산합니다. |
| `['pi']` | number | 수학 상수를 `PI`반환합니다. |
| `['round', number]` | number | 숫자를 가장 가까운 정수로 반올림합니다. 중간 값은 0에서 반올림됩니다. 예를 들어 `['round', -1.5]` -2로 평가합니다. |
| `['sin', number]` | number | 지정된 숫자의 사수를 계산합니다. |
| `['sqrt', number]` | number | 지정된 숫자의 제곱근을 계산합니다. |
| `['tan', number]` | number | 지정된 숫자의 접선을 계산합니다. |

## <a name="aggregate-expression"></a>집계 식

집계 식은 데이터 집합을 통해 처리되는 계산을 정의하며 `clusterProperties` `DataSource`의 옵션과 함께 사용할 수 있습니다. 이러한 식의 출력은 숫자 또는 부울이어야 합니다. 

집계 식은 연산자 값및 초기 값과 데이터의 각 기능에서 속성을 검색하여 집계 작업을 적용하는 식의 세 가지 값으로 합니다. 이 식에는 다음과 같은 형식이 있습니다.

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 연산자: 클러스터의 각 점에 의해 계산된 `mapExpression` 모든 값에 대해 적용되는 식 함수입니다. 지원되는 연산자: 
    - 숫자: `+`, `*` `max``min`
    - 부울의 경우: `all``any`
- initialValue: 첫 번째 계산된 값이 집계되는 초기 값입니다.
- mapExpression: 데이터 집합의 각 점에 적용되는 식입니다.

**예**

데이터 집합의 모든 피쳐에 숫자인 `revenue` 속성이 있는 경우. 그런 다음 데이터 집합에서 생성된 클러스터의 모든 포인트의 총 수익을 계산할 수 있습니다. 이 계산은 다음 집계 식을 사용하여 수행됩니다.`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>부울 식

부울 식은 부울 비교를 평가하기 위한 부울 연산자 식 집합을 제공합니다.

값을 비교할 때 비교는 엄격하게 입력됩니다. 다른 형식의 값은 항상 같지 않은 것으로 간주됩니다. 구문 분석 시간에 형식이 다른 것으로 알려진 경우는 유효하지 않은 것으로 간주되며 구문 분석 오류가 발생합니다. 

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | 논리적 부정. `true` 입력이 `false`있는 경우 `false` 및 입력이 `true`있는 경우 를 반환합니다. |
| `['!= ', value, value]` | boolean | 그렇지 않으면 입력 값이 같지 않으면 반환합니다. `true` `false` |
| `['<', value, value]` | boolean | 그렇지 않으면 첫 번째 입력이 두 번째 입력보다 엄격히 적으면 반환됩니다. `true` `false` 인수는 문자열 또는 두 숫자 모두여야 합니다. |
| `['<=', value, value]` | boolean | 그렇지 않으면 첫 번째 입력이 두 번째 입력보다 적거나 같으면 반환됩니다. `true` `false` 인수는 문자열 또는 두 숫자 모두여야 합니다. |
| `['==', value, value]` | boolean | 그렇지 않으면 입력 값이 같으면 반환합니다. `true` `false` 인수는 문자열 또는 두 숫자 모두여야 합니다. |
| `['>', value, value]` | boolean | 그렇지 않으면 첫 번째 입력이 두 번째 입력보다 엄격히 큰 경우 반환합니다. `true` `false` 인수는 문자열 또는 두 숫자 모두여야 합니다. |
| `['>=' value, value]` | boolean | 그렇지 않으면 첫 번째 입력이 두 번째 입력보다 크거나 같으면 반환됩니다. `true` `false` 인수는 문자열 또는 두 숫자 모두여야 합니다. |
| `['all', boolean, boolean, …]` | boolean | 그렇지 않으면 모든 입력이 있는 경우 반환합니다. `true` `true` `false` |
| `['any', boolean, boolean, …]` | boolean | 입력이 `true` `false` 있는 경우 반환합니다. `true` |

## <a name="conditional-expressions"></a>조건부 식

조건식은 if-문과 같은 논리 연산을 제공합니다.

다음 식은 입력 데이터에 대한 조건부 논리 작업을 수행합니다. 예를 들어 `case` 식은 "if/then/else" 논리를 `match` 제공하지만 식은 "switch-문"과 같습니다. 

### <a name="case-expression"></a>대/소문자 표현식

`case` 식은 "if/then/else" 논리를 제공하는 조건식의 유형입니다. 이 유형의 식은 부울 조건 목록을 단계화합니다. true로 평가할 첫 번째 부울 조건의 출력 값을 반환합니다.

다음 의사 코드는 식의 구조를 `case` 정의합니다. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**예제**

다음 예제에서는 `true`을 평가한 다음 해당 관련 값을 반환할 때까지 다른 부울 조건을 단계로 진행합니다. 부울 조건이 평가되지 `true`않으면 대체 값이 반환됩니다. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>일치 식

`match` 식은 논리와 같은 switch 문을 제공하는 조건부 식의 유형입니다. 입력은 문자열이나 숫자를 `['get', 'entityType']` 반환하는 식과 같은 식일 수 있습니다. 각 레이블은 단일 리터럴 값 또는 리터럴 값의 배열이어야 하며, 이 값은 모든 문자열 또는 모든 숫자여야 합니다. 배열의 값이 일치하는 경우 입력이 일치합니다. 각 레이블은 고유해야 합니다. 입력 유형이 레이블 의 형식과 일치하지 않으면 결과는 대체 값이 됩니다.

다음 의사 코드는 식의 구조를 `match` 정의합니다. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**예**

다음 예제에서는 거품 `entityType` 레이어에서 점 피쳐의 속성을 검색하여 일치를 찾습니다. 일치하는 값을 찾으면 지정된 값이 반환되거나 대체 값을 반환합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

다음 예제에서는 배열을 사용하여 모두 동일한 값을 반환해야 하는 레이블 집합을 나열합니다. 이 방법은 각 레이블을 개별적으로 나열하는 것보다 훨씬 효율적입니다. 이 경우 숙소가 `entityType` "레스토랑" 또는 "grocery_store"인 경우 "빨간색"의 색상이 반환됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

다음 예제에서는 match 식을 사용하여 "배열" 또는 "배열 포함" 형식 필터를 수행합니다. 이 경우 식은 허용된 ID 목록에 있는 ID 값이 있는 데이터를 필터링합니다. 필터가 있는 식을 사용하는 경우 결과는 부울 값이어야 합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>병합 식

식은 `coalesce` 첫 번째 비null 값을 가져오고 해당 값을 반환할 때까지 식 집합을 단계화합니다. 

다음 의사 코드는 식의 구조를 `coalesce` 정의합니다. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**예제**

다음 예제에서는 `coalesce` 식을 사용하여 `textField` 기호 레이어 옵션을 설정합니다. 속성이 `title` 피처에서 누락되었거나 `null`로 설정된 경우 식은 `subtitle` 누락된 경우 속성을 `null`찾으려고 시도합니다. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

다음 예제에서는 `coalesce` 표현식을 사용하여 지정된 이미지 이름 목록에서 맵 스프라이트에서 사용할 수 있는 첫 번째 이미지 아이콘을 검색합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>형식 식

형식 식은 문자열, 숫자 및 부울 값과 같은 다양한 데이터 형식을 테스트하고 변환하는 도구를 제공합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | 배열 \| 개체 | 리터럴 배열 또는 개체 값을 반환합니다. 이 식을 사용하여 배열 또는 개체가 식으로 평가되지 않도록 합니다. 이는 식에서 배열 또는 개체를 반환해야 하는 경우에 필요합니다. |
| `['image', string]` | 문자열 | 지정된 이미지 ID가 맵 이미지 스프라이트에 로드되는지 확인합니다. 이 경우 ID가 반환되고 그렇지 않으면 null이 반환됩니다. |
| `['to-boolean', value]` | boolean | 입력 값을 부울로 변환합니다. 그 결과 `false` 입력이 빈 문자열, `0` `false`" `null`또는 `NaN`; 그렇지 `true`않으면 . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | 색 | 입력 값을 색상으로 변환합니다. 여러 값이 제공되면 첫 번째 성공적인 변환을 얻을 때까지 각 값이 순서대로 평가됩니다. 입력을 변환할 수 없는 경우 식은 오류입니다. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 가능하면 입력 값을 숫자로 변환합니다. 입력이 `null` 또는 `false`에 이하면 결과는 0입니다. 입력이 `true`이경우 결과는 1입니다. 입력이 문자열인 경우 ECMAScript 언어 사양의 [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) 문자열 함수를 사용하여 숫자로 변환됩니다. 여러 값이 제공되면 첫 번째 성공적인 변환을 얻을 때까지 각 값이 순서대로 평가됩니다. 입력을 변환할 수 없는 경우 식은 오류입니다. |
| `['to-string', value]` | 문자열 | 입력 값을 문자열로 변환합니다. 입력이 `null`있는 경우 결과는 `""`입니다. 입력이 부울인 경우 결과는 `"true"` 또는 `"false"`. 입력이 숫자인 경우 ECMAScript 언어 사양의 [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) 번호 함수를 사용하여 문자열로 변환됩니다. 입력이 색상이면 CSS RGBA 색상 문자열로 `"rgba(r,g,b,a)"`변환됩니다. 그렇지 않으면 입력은 ECMAScript 언어 사양의 [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) 함수를 사용하여 문자열로 변환됩니다. |
| `['typeof', value]` | 문자열 | 지정된 값의 형식을 설명하는 문자열을 반환합니다. |

> [!TIP]
> 브라우저 콘솔에 유사한 `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` 오류 메시지가 나타나는 경우 첫 번째 값에 대한 문자열이 없는 배열이 있는 함수가 코드의 어딘가에 있다는 의미입니다. 식이 배열을 반환하려면 배열을 식으로 래핑합니다. `literal` 다음 예제에서는 `offset` `match` 점 피처의 `entityType` 속성 값에 따라 두 개의 오프셋 값 중에서 선택할 수 있는 식을 사용하여 두 개의 숫자를 포함하는 배열이어야 하는 기호 레이어의 아이콘 옵션을 설정합니다.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>색상 표현식

색상 표현식을 사용하면 색상 값을 쉽게 만들고 조작할 수 있습니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | 색 | 의 알파 구성 요소 와 *blue* 사이의 `0` `255`범위를 가져야 하는 *빨간색,* *녹색* `1`및 파란색 구성 요소에서 색상 값을 만듭니다. 구성 요소가 범위를 벗어난 경우 표현식은 오류입니다. |
| `['rgba', number, number, number, number]` | 색 | *빨간색,* *녹색,* *파란색* 구성 요소 `0` 사이의 범위와 `255`및 `0` `1`의 범위 내에서 알파 구성 요소에서 색상 값을 만듭니다. 구성 요소가 범위를 벗어난 경우 표현식은 오류입니다. |
| `['to-rgba']` | \[번호, 번호, 번호, 번호, 번호\] | 입력 색상의 *빨간색,* *녹색,* *파란색*및 *알파* 구성 요소를 포함하는 4개 요소 배열을 해당 순서대로 반환합니다. |

**예제**

다음 예제에서는 *빨간색* `255`값이 있는 RGB 색상 값과 `2.5` `temperature` 속성 값에 곱하여 계산되는 *녹색* 및 *파란색* 값을 만듭니다. 온도가 변함에 따라 색상이 *빨간색의*다른 음영으로 바뀝니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>문자열 연산자 식

문자열 연산자 식은 서비스 케이스를 연결 및 변환하는 등의 문자열에서 변환 작업을 수행합니다. 

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 문자열 | 여러 문자열을 함께 연결합니다. 각 값은 문자열이어야 합니다. 필요한 `to-string` 경우 형식 식을 사용하여 다른 값 형식을 문자열로 변환합니다. |
| `['downcase', string]` | 문자열 | 지정된 문자열을 소문자로 변환합니다. |
| `['upcase', string]` | 문자열 | 지정된 문자열을 대문자로 변환합니다. |

**예제**

다음 예제에서는 점 `temperature` 피쳐의 속성을 문자열로 변환한 다음 "°F"를 끝까지 연결합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

위의 표현식은 아래 이미지와 같이 "64°F" 텍스트가 겹쳐져 맵에 핀을 렌더링합니다.

<center>

![문자열 연산자 식 예제](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>보간 및 단계 표현식

보간 및 단계 표현식을 사용하여 보간된 곡선 또는 단계 함수를 따라 값을 계산할 수 있습니다. 이러한 식은 숫자 `['get',  'temperature']`값을 입력으로 반환하는 식을 사용합니다. 입력 값은 입력 및 출력 값 쌍에 대해 평가되어 보간된 곡선 또는 단계 함수에 가장 적합한 값을 결정합니다. 출력 값을 "중지"라고 합니다. 각 중지에 대한 입력 값은 숫자여야 하며 오름차순이어야 합니다. 출력 값은 숫자, 숫자 배열 또는 색상이어야 합니다.

### <a name="interpolate-expression"></a>식 보간

식을 `interpolate` 사용하여 정지 값 간에 보간하여 연속되고 매끄러운 값 집합을 계산할 수 있습니다. 색상 `interpolate` 값을 반환하는 표현식은 결과 값이 선택되는 색상 그라데이션을 생성합니다.

식에 사용할 수 있는 세 가지 보간 `interpolate` 메서드가 있습니다.
 
* `['linear']`- 경유지 쌍 사이에 선형으로 보간합니다.
* `['exponential', base]`- 정류장 사이에 기하급수적으로 보간됩니다. 값은 `base` 출력이 증가하는 속도를 제어합니다. 값이 높을수록 출력이 범위의 하이 엔드쪽으로 더 많이 증가합니다. 값이 `base` 1에 가까우면 선형으로 증가하는 출력이 생성됩니다.
* `['cubic-bezier', x1, y1, x2, y2]`- 지정된 제어점에 의해 정의된 [입방 베지어 곡선을](https://developer.mozilla.org/docs/Web/CSS/timing-function) 사용하여 보간합니다.

다음은 이러한 다양한 유형의 보간모양을 예로 들 수 있습니다. 

| 선형  | 지수 | 큐빅 베지어 |
|---------|-------------|--------------|
| ![선형 보간 그래프](media/how-to-expressions/linear-interpolation.png) | ![지수 보간 그래프](media/how-to-expressions/exponential-interpolation.png) | ![큐빅 베지어 보간 그래프](media/how-to-expressions/bezier-curve-interpolation.png) |

다음 의사 코드는 식의 구조를 `interpolate` 정의합니다. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**예제**

다음 예제에서는 `linear interpolate` 표현식을 `color` 사용하여 점 피쳐의 `temperature` 속성을 기반으로 거품 레이어의 속성을 설정합니다. `temperature` 값이 60보다 적으면 "파란색"이 반환됩니다. 60에서 70 미만인 경우 노란색이 반환됩니다. 70에서 80 미만인 경우 "주황색"이 반환됩니다. 80 이상인 경우 "빨간색"이 반환됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

다음 이미지는 위의 표현식에 대해 색상을 선택한 방법을 보여 줍니다.
 
<center>

![식 보간](media/how-to-expressions/interpolate-expression-example.png) 예제</center>

### <a name="step-expression"></a>단계 식

식은 `step` 스톱에 의해 정의된 [조각상수 함수를](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) 평가하여 불연속, 단계별 결과 값을 계산하는 데 사용할 수 있습니다. 

다음 의사 코드는 식의 구조를 `step` 정의합니다. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

단계 식은 입력 값 바로 전에 중지의 출력 값을 반환하거나 입력이 첫 번째 중지보다 적은 경우 첫 번째 입력 값을 반환합니다. 

**예제**

다음 예제에서는 `step` 표현식을 `color` 사용하여 점 피쳐의 `temperature` 속성을 기반으로 거품 레이어의 속성을 설정합니다. `temperature` 값이 60보다 적으면 "파란색"이 반환됩니다. 60에서 70 미만인 경우 "노란색"이 반환됩니다. 70에서 80 미만인 경우 "주황색"이 반환됩니다. 80 이상인 경우 "빨간색"이 반환됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

다음 이미지는 위의 표현식에 대해 색상을 선택한 방법을 보여 줍니다.
 
<center>

![단계 발현 예](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>레이어별 표현식

특정 레이어에만 적용되는 특수 식입니다.

### <a name="heat-map-density-expression"></a>히트 맵 밀도 표현

히트 맵 밀도 표현식은 히트 맵 레이어의 각 픽셀에 대한 `['heatmap-density']`히트 맵 밀도 값을 검색하고 로 정의됩니다. 이 값은 와 `0` `1`사이의 숫자입니다. 히트 맵의 색상을 지정하는 데 사용되는 색상 그라데이션을 정의하기 위해 `interpolation` 또는 `step` 표현식과 함께 사용됩니다. 이 표현식은 히트 맵 레이어의 [색상 옵션에서만](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) 사용할 수 있습니다.

> [!TIP]
> 인덱스 0의 색상은 보간 식 또는 단계 색상의 기본 색상에서 데이터가 없는 영역의 색상을 정의합니다. 인덱스 0의 색상을 사용하여 배경 색을 정의할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다.

**예제**

이 예제에서는 라이너 보간 식을 사용하여 히트 맵을 렌더링하기 위한 부드러운 색상 그라데이션을 만듭니다. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

부드러운 그라데이션을 사용하여 히트 맵의 색상을 지정하는 것 외에도 `step` 표현식을 사용하여 범위 집합 내에서 색상을 지정할 수 있습니다. 히트 `step` 맵의 색상을 지정하기 위한 식을 사용하면 밀도가 등고선 또는 레이더 스타일 맵과 유사한 범위로 시각적으로 나뉩니다.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

자세한 내용은 히트 [맵 레이어 추가 설명서를](map-add-heat-map-layer.md) 참조하십시오.

### <a name="line-progress-expression"></a>선 진행 식

선 진행표현식은 선 레이어의 그라데이션 선을 따라 진행률을 검색하고 `['line-progress']`로 정의됩니다. 이 값은 0과 1 사이의 숫자입니다. `interpolation` 또는 식과 `step` 함께 사용됩니다. 이 표현식은 선 레이어의 [스트로크그라데이션 옵션에서만]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) 사용할 수 있습니다. 

> [!NOTE]
> `strokeGradient` 선 레이어 옵션을 사용하려면 `lineMetrics` 데이터 원본을 로 설정해야 합니다. `true`

**예제**

이 예제에서는 `['line-progress']` 표현식을 사용하여 선 의 획에 색상 그라데이션을 적용합니다.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[라이브 예제 보기](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>텍스트 필드 형식 식

텍스트 필드 형식 표현식은 `textField` 혼합 텍스트 서식을 제공하기 위해 기호 레이어 `textOptions` 속성 의 옵션과 함께 사용할 수 있습니다. 이 식을 사용하면 입력 문자열 및 서식 옵션을 지정할 수 있습니다. 이 식의 각 입력 문자열에 대해 다음 옵션을 지정할 수 있습니다.

 * `'font-scale'`- 글꼴 크기에 대한 배율 조정률을 지정합니다. 지정하면 이 값은 개별 `size` 문자열의 `textOptions` 속성을 재정의합니다.
 * `'text-font'`- 이 문자열에 사용해야 하는 하나 이상의 글꼴 패밀리를 지정합니다. 지정하면 이 값은 개별 `font` 문자열의 `textOptions` 속성을 재정의합니다.
 * `'text-color'`- 렌더링 할 때 텍스트에 적용할 색상을 지정합니다. 

다음 의사 코드는 텍스트 필드 형식 식의 구조를 정의합니다. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**예제**

다음 예제에서는 굵은 글꼴을 추가하고 피처 `title` 속성의 글꼴 크기를 확장하여 텍스트 필드의 서식을 지정합니다. 또한 이 예제에서는 `subtitle` 글꼴 크기와 빨간색으로 축소된 줄 바탕 화면에 피처의 속성을 추가합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

이 레이어는 아래 이미지와 같이 점 피쳐를 렌더링합니다.
 
<center>

![서식이 지정된 텍스트 필드가](media/how-to-expressions/text-field-format-expression.png) 있는 점 피쳐 이미지</center>

### <a name="number-format-expression"></a>숫자 형식 식

식은 `number-format` 기호 레이어 옵션에서만 `textField` 사용할 수 있습니다. 이 식은 제공된 숫자를 서식이 지정된 문자열로 변환합니다. 이 식은 JavaScript의 [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 함수를 래핑하고 다음 옵션 집합을 지원합니다.

 * `locale`- 지정된 언어에 맞는 방식으로 숫자를 문자열로 변환하기 위한 이 옵션을 지정합니다. 이 옵션에 [BCP 47 언어 태그를](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) 전달합니다.
 * `currency`- 통화를 나타내는 문자열로 번호를 변환합니다. 가능한 값은 미국 달러의 "USD", 유로화의 "EUR" 또는 중국 위안화의 "CNY"와 같은 [ISO 4217 통화 코드입니다.](https://en.wikipedia.org/wiki/ISO_4217)
 * `'min-fraction-digits'`- 숫자의 문자열 버전에 포함할 소수 자릿수의 최소 수를 지정합니다.
 * `'max-fraction-digits'`- 숫자의 문자열 버전에 포함할 소수 자릿수의 최대 수를 지정합니다.

다음 의사 코드는 텍스트 필드 형식 식의 구조를 정의합니다. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**예제**

다음 예제에서는 `number-format` 식을 사용하여 `revenue` 점 피쳐의 속성이 미국 `textField` 달러 값으로 표시되도록 기호 레이어 옵션에서 렌더링되는 방법을 수정합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

이 레이어는 아래 이미지와 같이 점 피쳐를 렌더링합니다.

<center>

![숫자 형식](media/how-to-expressions/number-format-expression.png) 표현 예</center>

### <a name="image-expression"></a>이미지 표현

이미지 표현식은 심볼 `image` `textField` 레이어의 옵션 및 다각형 레이어 `fillPattern` 옵션과 함께 사용할 수 있습니다. 이 식은 요청된 이미지가 스타일에 있는지 확인하고 확인된 이미지 `null`이름 또는 이미지가 현재 스타일에 있는지 여부에 따라 반환됩니다. 이 유효성 검사 프로세스는 동기식이며 이미지 인수에서 요청하기 전에 이미지가 스타일에 추가되어야 합니다.

**예제**

다음 예제에서는 `image` 식을 사용하여 기호 레이어에 텍스트가 있는 아이콘을 인라인으로 추가합니다. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

이 레이어는 아래 이미지와 같이 기호 레이어의 텍스트 필드를 렌더링합니다.

<center>

![이미지 표현](media/how-to-expressions/image-expression.png) 예</center>

## <a name="zoom-expression"></a>확대/축소 표현식

`zoom` 표현식은 렌더링 시 맵의 현재 확대/축소 수준을 검색하는 데 사용되며 `['zoom']`로 정의됩니다. 이 표현식은 맵의 최소 및 최대 확대/축소 수준 범위 사이의 숫자를 반환합니다. 웹 및 Android 지원 25 줌 수준에 대한 Azure Maps 대화형 맵 컨트롤은 0부터 24까지의 숫자로 표시됩니다. 표현식을 사용하면 맵의 `zoom` 확대/축소 수준이 변경될 때 스타일을 동적으로 수정할 수 있습니다. 식과 `zoom` 함께만 `interpolate` `step` 사용할 수 있습니다.

**예제**

기본적으로 히트 맵 레이어에서 렌더링된 데이터 점의 반지름은 모든 확대/축소 수준에 대해 고정된 픽셀 반지름을 가며 있습니다. 맵을 확대하면 데이터가 함께 집계되고 히트 맵 레이어가 다르게 보입니다. 표현식을 `zoom` 사용하여 각 데이터 포인트가 맵의 동일한 물리적 영역을 덮도록 각 확대/축소 수준에 대한 반지름을 배율 조정할 수 있습니다. 히트 맵 레이어가 더 정적이고 일관되게 보이게 합니다. 맵의 각 확대/축소 수준은 이전 확대/축소 수준보다 수직 및 수평으로 두 배 더 많은 픽셀을 가짐을 가짐입니다. 각 확대/축소 수준으로 두 배가 되도록 반경의 배율을 조정하면 모든 확대/축소 수준에서 일관되게 보이는 히트 맵이 생성됩니다. 아래와 같이 `zoom` `2 * Math.pow(2, minZoom - maxZoom)` 계산된 최대 `base 2 exponential interpolation` 확대/축소 레벨에 대해 픽셀 반지름이 최소 확대 수준에 대해 설정되고 배율이 조정된 반지름을 사용하여 식을 사용하여 수행할 수 있습니다.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[라이브 예제 보기](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>가변 바인딩 식

변수 바인딩 식은 계산 결과를 변수에 저장합니다. 따라서 계산 결과를 식의 다른 곳에서 여러 번 참조할 수 있습니다. 많은 계산이 포함된 표현식에 유용한 최적화입니다.

| 식 | 반환 형식 | Description |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'하자',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: 문자열,<br/>&nbsp;&nbsp;&nbsp;&nbsp;값1:<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: 문자열,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2:<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 결과를 반환하는 자식 식의 `var` 식에서 사용할 변수로 하나 이상의 값을 저장합니다. |
| `['var', name: string]` | any | 식을 사용하여 만든 변수를 참조합니다. `let` |

**예제**

이 예제에서는 온도 비율을 기준으로 수익을 계산한 다음 `case` 식을 사용하여 이 값에 대한 다른 부울 작업을 평가합니다. 식은 `let` 온도 비율을 기준으로 수익을 저장하는 데 사용되므로 한 번만 계산하면 됩니다. 식은 `var` 이 변수를 다시 계산할 필요 없이 필요에 따라 자주 참조합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>다음 단계

식을 구현하는 자세한 코드 샘플은 다음 문서를 참조하십시오.

> [!div class="nextstepaction"] 
> [기호 레이어 추가](map-add-pin.md)

> [!div class="nextstepaction"] 
> [거품 레이어 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"] 
> [열 지도 계층 추가](map-add-heat-map-layer.md)

표현을 지원하는 레이어 옵션에 대해 자세히 알아보십시오.

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [라인 레이어 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [다각형레이어옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
