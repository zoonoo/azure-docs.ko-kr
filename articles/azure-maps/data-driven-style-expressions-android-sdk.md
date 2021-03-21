---
title: Android maps의 데이터 기반 스타일 식 | Microsoft Azure 맵
description: 데이터 기반 스타일 식에 대해 알아봅니다. Azure Maps Android SDK에서 이러한 식을 사용 하 여 맵의 스타일을 조정 하는 방법을 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1babf1feb550109486089c45469ab4ce32f72cb3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097417"
---
# <a name="data-driven-style-expressions-android-sdk"></a>데이터 기반 스타일 식 (Android SDK)

식을 사용 하면 데이터 원본의 각 셰이프에 정의 된 속성을 관찰 하는 옵션에 대 한 스타일 지정 옵션에 비즈니스 논리를 적용할 수 있습니다. 식은 데이터 원본 또는 계층의 데이터를 필터링 할 수 있습니다. 식은 if 문과 같은 조건부 논리로 구성 될 수 있습니다. 또한 문자열 연산자, 논리 연산자, 수학 연산자를 사용 하 여 데이터를 조작 하는 데 사용할 수 있습니다.

데이터 기반 스타일은 스타일 지정에 대 한 비즈니스 논리를 구현 하는 데 필요한 코드의 양을 줄여 줍니다. 계층에서 사용 하는 경우 별도의 스레드에서 렌더링 시 식이 계산 됩니다. 이 기능은 UI 스레드에서 비즈니스 논리를 평가 하는 것과 비교 하 여 향상 된 성능을 제공 합니다.

Azure Maps Android SDK Azure Maps 웹 SDK와 거의 동일한 스타일 식을 모두 지원 하므로 [데이터 기반 스타일 식 (웹 SDK)](data-driven-style-expressions-web-sdk.md) 에 설명 된 것과 동일한 모든 개념을 Android 앱으로 전달할 수 있습니다. Azure Maps Android SDK의 모든 스타일 식은 네임 스페이스에서 사용할 수 있습니다 `com.microsoft.azure.maps.mapcontrol.options.Expression` . 스타일 식에는 여러 가지 형식이 있습니다.

| 식 형식 | 설명 |
|---------------------|-------------|
| [부울 식](#boolean-expressions) | 부울 식은 부울 비교를 평가 하기 위한 부울 연산자 식 집합을 제공 합니다. |
| [색 식](#color-expressions) | 색 식을 사용 하면 색 값을 보다 쉽게 만들고 조작할 수 있습니다. |
| [조건식](#conditional-expressions) | 조건식은 if 문과 같은 논리 연산을 제공 합니다. |
| [데이터 식](#data-expressions) | 기능에서 속성 데이터에 대 한 액세스를 제공 합니다. |
| [보간 및 단계 식](#interpolate-and-step-expressions) | 보간 및 단계 식은 보간된 곡선이 나 step 함수를 따라 값을 계산 하는 데 사용할 수 있습니다. |
| [JSON 기반 식](#json-based-expressions) | Android SDK를 사용 하 여 웹 SDK에 대해 만든 스타일 원시 JSON 기반 식을 쉽게 다시 사용할 수 있습니다. |  
| [계층 관련 식](#layer-specific-expressions) | 단일 계층에만 적용 되는 특수 식입니다. |
| [수학 식](#math-expressions) | 식 프레임 워크 내에서 데이터 기반 계산을 수행 하는 수치 연산자를 제공 합니다. |
| [문자열 연산자 식](#string-operator-expressions) | 문자열 연산자 식은 사례를 연결 하 고 변환 하는 등의 문자열에 대해 변환 작업을 수행 합니다. |
| [Type 식](#type-expressions) | 형식 식은 문자열, 숫자, 부울 값 등의 다양 한 데이터 형식을 테스트 하 고 변환 하는 도구를 제공 합니다. |
| [변수 바인딩 식](#variable-binding-expressions) | 변수 바인딩 식은 계산 결과를 변수에 저장 하 고 저장 된 값을 다시 계산할 필요 없이 식의 다른 위치에서 여러 번 참조 합니다. |
| [확대/축소 식](#zoom-expression) | 렌더링 시 지도의 현재 확대/축소 수준을 검색 합니다. |

> [!NOTE]
> 식의 구문은 Java 및 Kotlin에서 거의 동일 합니다. 설명서가 Kotlin로 설정 되어 있지만 Java 용 코드 블록을 참조 하는 경우 코드는 두 언어에서 동일 합니다.

문서의이 섹션에 나오는 모든 예제에서는 다음 기능을 사용 하 여 이러한 식을 사용할 수 있는 여러 가지 방법을 보여 줍니다.

```json
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
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

다음 코드는 앱에서이 GeoJSON 기능을 수동으로 만드는 방법을 보여 줍니다.

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

다음 코드에서는 문자열 형식 버전의 JSON 개체를 앱의 GeoJSON 기능으로 deserialize 하는 방법을 보여 줍니다.

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>JSON 기반 식

Azure Maps 웹 SDK는 JSON 배열을 사용 하 여 표시 되는 데이터 기반 스타일 식도 지원 합니다. 이러한 동일한 식은 Android SDK의 네이티브 클래스를 사용 하 여 다시 만들 수 있습니다 `Expression` . 또는와 같은 웹 함수를 사용 하 여 이러한 JSON 기반 식을 문자열로 변환 하 `JSON.stringify` 고 메서드에 전달할 수 있습니다 `Expression.raw(String rawExpression)` . 예를 들어 다음 JSON 식을 사용 합니다.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

위 식의 문자열 형식 버전은 `"['get','title']"` 이며 다음과 같이 Android SDK 읽을 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

이 방법을 사용 하면 Azure Maps를 사용 하는 모바일 앱과 웹 앱 간에 스타일 식을 쉽게 재사용할 수 있습니다.

이 비디오는 Azure Maps의 데이터 기반 스타일에 대 한 개요를 제공 합니다.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>데이터 식

데이터 식은 기능에서 속성 데이터에 대 한 액세스를 제공 합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `accumulated()` | 숫자 | 지금까지 누적 된 클러스터 속성 값을 가져옵니다. |
| `at(number | Expression, Expression)` | 값 | 배열에서 항목을 검색 합니다. |
| `geometryType()` | 문자열 | 기능의 기 하 도형 유형인 Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon을 가져옵니다. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | 값 | 제공 된 개체의 속성에서 속성 값을 가져옵니다. 요청 된 속성이 없는 경우 null을 반환 합니다. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | 기능의 속성에 지정 된 속성이 있는지 여부를 확인 합니다. |
| `id()` | 값 | 기능 ID가 있는 경우 해당 ID를 가져옵니다. |
| `in(string | number | Expression, Expression)` | boolean | 항목이 배열에 있는지 여부를 확인 합니다. |
| `length(string | Expression)` | 숫자 | 문자열이 나 배열의 길이를 가져옵니다. |
| `properties()`| 값 | 기능 속성 개체를 가져옵니다. |

다음 웹 SDK 스타일 식은 Android SDK 지원 되지 않습니다.

- 인덱스-
- slice

**예**

식을 사용 하 여 식에서 직접 기능의 속성에 액세스할 수 있습니다 `get` . 이 예에서는 기능 값을 사용 하 여 `zoneColor` 거품형 계층의 color 속성을 지정 합니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

위의 예제는 모든 지점 기능에 속성이 있는 경우 제대로 작동 합니다 `zoneColor` . 그렇지 않으면 색이 "black"으로 대체 될 가능성이 높습니다. 대체 (fallback) 색을 수정 하려면 식을 식과 함께 사용 하 여 `switchCase` `has` 속성이 있는지 확인 합니다. 속성이 없으면 대체 (fallback) 색을 반환 합니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

거품형 및 기호 계층은 기본적으로 데이터 원본에 있는 모든 셰이프의 좌표를 렌더링 합니다. 이 동작은 다각형의 꼭 짓 점 또는 선을 강조 표시할 수 있습니다. `filter`계층의 옵션을 사용 하 여 `geometryType` 부울 식에서 식을 사용 하 여 렌더링 하는 기능의 기 하 도형 유형을 제한할 수 있습니다. 다음 예에서는 기능만 렌더링 되도록 거품형 계층을 제한 합니다 `Point` .

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

다음 예에서는 `Point` 및 `MultiPoint` 기능을 모두 렌더링할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

마찬가지로 다각형의 윤곽선이 선 계층에서 렌더링 됩니다. 선 계층에서이 동작을 사용 하지 않도록 설정 하려면 및 기능만 허용 하는 필터를 추가 `LineString` `MultiLineString` 합니다.  

다음은 데이터 식을 사용 하는 방법에 대 한 몇 가지 추가 예입니다.

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>수학 식

수학 식은 식 프레임 워크 내에서 데이터 기반 계산을 수행 하는 수치 연산자를 제공 합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `abs(number | Expression)` | 숫자 | 지정된 숫자의 절대 값을 계산합니다. |
| `acos(number | Expression)` | 숫자 | 지정 된 숫자의 아크코사인을 계산 합니다. |
| `asin(number | Expression)` | 숫자 | 지정 된 숫자의 아크사인을 계산 합니다. |
| `atan(number | Expression)` | 숫자 | 지정 된 숫자의 아크탄젠트를 계산 합니다. |
| `ceil(number | Expression)` | 숫자 | 숫자를 다음 정수 정수로 반올림 합니다. |
| `cos(number | Expression)` | 숫자 | 지정 된 수의 cos를 계산 합니다. |
| `division(number, number)` \| `division(Expression, Expression)` | 숫자 | 첫 번째 숫자를 두 번째 숫자로 나눕니다. 웹 SDK 동급 식: `/` |
| `e()` | 숫자 | 수학 상수를 반환 합니다 `e` . |
| `floor(number | Expression)` | 숫자 | 숫자를 이전 정수 정수로 내림 합니다. |
| `log10(number | Expression)` | 숫자 | 지정 된 숫자의 밑이 10 인 로그를 계산 합니다. |
| `log2(number | Expression)` | 숫자 | 지정 된 숫자의 밑이 2 인 로그를 계산 합니다. |
| `ln(number | Expression)` | 숫자 | 지정 된 숫자의 자연 로그를 계산 합니다. |
| `ln2()` | 숫자 | 수학 상수를 반환 합니다 `ln(2)` . |
| `max(numbers... | expressions...)` | 숫자 | 지정 된 숫자 집합의 최대 수를 계산 합니다. |
| `min(numbers... | expressions...)` | 숫자 | 지정 된 숫자 집합의 최소 수를 계산 합니다. |
| `mod(number, number)` \| `mod(Expression, Expression)` | 숫자 | 첫 번째 숫자를 두 번째 숫자로 나눌 때 나머지를 계산 합니다. 웹 SDK 동급 식: `%` |
| `pi()` | 숫자 | 수학 상수를 반환 합니다 `PI` . |
| `pow(number, number)` \| `pow(Expression, Expression)` | 숫자 | 두 번째 숫자의 거듭제곱으로 발생 한 첫 번째 값의 값을 계산 합니다. |
| `product(numbers... | expressions...)` | 숫자 | 지정 된 숫자를 곱합니다. 웹 SDK 동급 식: `*` |
| `round(number | Expression)` | 숫자 | 숫자를 가장 가까운 정수로 반올림 합니다. 중간 값은 0에서 먼 쪽으로 반올림 됩니다. 예를 들어은 `round(-1.5)` 로 계산 `-2` 됩니다. |
| `sin(number | Expression)` | 숫자 | 지정 된 숫자의 사인을 계산 합니다. |
| `sqrt(number | Expression)` | 숫자 | 지정된 숫자의 제곱근을 계산합니다. |
| `subtract(number | Expression` | 숫자 | 지정 된 수 만큼 0을 뺍니다. |
| `subtract(number | Expression, number | Expression)` | 숫자 | 첫 번째 숫자를 두 번째 숫자로 뺍니다. |
| `sum(numbers... | expressions...)` | 숫자 | 지정 된 숫자의 합계를 계산 합니다. |
| `tan(number | Expression)` | 숫자 | 지정 된 숫자의 탄젠트를 계산 합니다. |

## <a name="boolean-expressions"></a>부울 식

부울 식은 부울 비교를 평가 하기 위한 부울 연산자 식 집합을 제공 합니다.

값을 비교할 때 비교는 엄격 하 게 형식화 됩니다. 다른 형식의 값은 항상 동일 하지 않은 것으로 간주 됩니다. 구문 분석 시 형식이 다른 것으로 알려진 사례는 잘못 된 것으로 간주 되며 구문 분석 오류를 생성 합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | `true`모든 입력이 이면를 반환 `true` 하 고, 그렇지 않으면를 반환 `false` 합니다. |
| `any(Expression...)` | boolean | `true`입력이 이면를 반환 하 고 `true` , 그렇지 않으면를 반환 `false` 합니다. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`입력 값이 같으면를 반환 하 고, `false` 그렇지 않으면를 반환 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`첫 번째 입력이 두 번째 보다 엄격 하 게 크면를 반환 하 고, 그렇지 않으면를 반환 `false` 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`첫 번째 입력 값이 두 번째 값 보다 크거나 같으면를 반환 하 고, 그렇지 않으면를 반환 `false` 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`첫 번째 입력이 두 번째 보다 엄격 하 게 작으면를 반환 하 고, 그렇지 않으면를 반환 `false` 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`첫 번째 입력이 두 번째 값 보다 작거나 같으면를 반환 하 고, `false` 그렇지 않으면를 반환 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`입력 값이 같지 않으면를 반환 하 고, 그렇지 않으면를 반환 `false` 합니다. |
| `not(Expression | boolean)` | boolean | 논리 부정. `true`입력이 이면를 반환 `false` 하 고, `false` 입력이 이면를 반환 합니다 `true` . |

## <a name="conditional-expressions"></a>조건부 식

조건식은 if 문과 같은 논리 연산을 제공 합니다.

다음 식은 입력 데이터에 대해 조건부 논리 연산을 수행 합니다. 예를 들어 식에서 " `switchCase` if/then/else" 논리를 제공 하는 반면 `match` 식은 "switch 문"과 유사 합니다.

### <a name="switch-case-expression"></a>Switch case 식

`switchCase`식은 "if/then/else" 논리를 제공 하는 조건식의 유형입니다. 이 유형의 식은 부울 조건 목록을 통해 단계를 진행 합니다. True로 평가할 첫 번째 부울 조건의 출력 값을 반환 합니다.

다음 의사 코드에서는 식의 구조를 정의 합니다 `switchCase` .

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**예제**

다음 예에서는로 계산 된 값을 찾은 다음 연결 된 값을 반환할 때까지 다른 부울 조건을 단계별로 안내 합니다 `true` . 부울 조건이로 계산 되지 않으면 `true` 대체 (fallback) 값이 반환 됩니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>일치 식

`match`식은 논리와 같은 switch 문을 제공 하는 조건식의 유형입니다. 입력은 `get( "entityType")` 문자열이 나 숫자를 반환 하는 등의 모든 식일 수 있습니다. 각 중지에는 단일 리터럴 값 또는 리터럴 값의 배열인 레이블이 있어야 합니다. 값은 모든 문자열 또는 모든 숫자 여야 합니다. 배열의 값이 일치 하는 경우 입력이 일치 합니다. 각 중지 레이블은 고유 해야 합니다. 입력 유형이 레이블의 유형과 일치 하지 않는 경우 결과는 기본 대체 (fallback) 값이 됩니다.

다음 의사 코드에서는 식의 구조를 정의 합니다 `match` .

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**예**

다음 예에서는 `entityType` 거품형 계층에서 Point 기능의 속성을 검색 하 여 일치 하는 항목을 찾습니다. 일치 하는 항목이 발견 되 면 지정 된 값이 반환 되거나 대체 (fallback) 값이 반환 됩니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

다음 예제에서는 배열을 사용 하 여 모두 동일한 값을 반환 해야 하는 레이블 집합을 나열 합니다. 이 방법은 각 레이블을 개별적으로 나열 하는 것 보다 훨씬 효율적입니다. 이 경우 `entityType` 속성이 "식당" 또는 "grocery_store" 이면 색 "red"가 반환 됩니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>병합 식

`coalesce`식에서는 첫 번째 null이 아닌 값을 가져와 값을 반환할 때까지 식 집합을 단계별로 진행 합니다.

다음 의사 코드에서는 식의 구조를 정의 합니다 `coalesce` .

```java
coalesce(Expression... input)
```

**예제**

다음 예에서는 식을 사용 하 여 `coalesce` `textField` 기호 계층의 옵션을 설정 합니다. `title`속성이 기능에서 누락 되었거나로 설정 된 경우에는 `null` 식에서 속성을 찾으려고 시도 합니다 `subTitle` . 또는가 누락 된 경우에는 `null` 빈 문자열로 대체 합니다.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>Type 식

형식 식은 문자열, 숫자, 부울 값 등의 다양 한 데이터 형식을 테스트 하 고 변환 하는 도구를 제공 합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `array(Expression)` | Object [] | 입력이 배열 임을 어설션 합니다. |
| `bool(Expression)` | boolean | 입력 값이 부울 임을 어설션 합니다. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | 병합기 | 로캘 종속 비교 작업에 사용할 병합기를 반환 합니다. 대/소문자 구분 및 분음 부호 구분 옵션은 기본적으로 false로 설정 됩니다. Locale 인수는 사용할 로캘의 IETF 언어 태그를 지정 합니다. 제공 된 항목이 없으면 기본 로캘이 사용 됩니다. 요청한 로캘을 사용할 수 없는 경우 병합기는 시스템 정의 대체 로캘을 사용 합니다. 해결 된 로캘을 사용 하 여 로캘 대체 동작의 결과를 테스트 합니다.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | 부울 \| 숫자 \| 문자열 \| 개체 \| 개체 [] | 리터럴 배열 또는 개체 값을 반환 합니다. 배열이 나 개체가 식으로 계산 되지 않도록 하려면이 식을 사용 합니다. 배열 또는 개체를 식에서 반환 해야 하는 경우이 작업이 필요 합니다. |
| `number(Expression)` | 숫자 | 입력 값이 숫자 임을 어설션 합니다. |
| `object(Expression)` | Object | 입력 값이 개체 임을 어설션 합니다. |
| `string(Expression)` | 문자열 | 입력 값이 문자열 임을 어설션 합니다. |
| `toArray(Expression)` | Object [] | 식을 JSON 개체 배열로 변환 합니다. |
| `toBool(Expression)` | boolean | 입력 값을 부울로 변환 합니다. |
| `toNumber(Expression)` | 숫자 | 가능한 경우 입력 값을 숫자로 변환 합니다. |
| `toString(Expression)` | 문자열 | 입력 값을 문자열로 변환 합니다. |
| `typeoOf(Expression)` | 문자열 | 지정 된 값의 형식을 설명 하는 문자열을 반환 합니다. |

## <a name="color-expressions"></a>색 식

색 식을 사용 하면 색 값을 보다 쉽게 만들고 조작할 수 있습니다.

| 식 | 반환 형식 | 설명 |
|------------|-------------|-------------|
| `color(int)` | 색 | 색 정수 값을 색 식으로 변환 합니다. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | 색 | 와 사이에 있어야 하는 *빨강*, *녹색* 및 *파랑* 구성 요소 `0` `255` 와의 알파 구성 요소에서 색 값을 만듭니다 `1` . 구성 요소가 범위를 벗어난 경우 식에 오류가 발생 합니다. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | 색 | 와 사이에 있어야 하는 *빨강*, *녹색*, *파랑* 구성 요소 `0` `255` 와 및 범위 내의 알파 구성 요소에서 색 값을 만듭니다 `0` `1` . 구성 요소가 범위를 벗어난 경우 식에 오류가 발생 합니다. |
| `toColor(Expression)` | 색  | 입력 값을 색으로 변환 합니다. |
| `toRgba(Expression)` | 색 | 입력 색의 빨간색, 녹색, 파랑 및 알파 구성 요소를 포함 하는 4 개 요소 배열을 해당 순서로 반환 합니다. |

**예제**

다음 예에서는 *빨강* 값이이 `255` 고 속성 값을 곱하여 계산 되는 *녹색* 및 *파랑* 값을 가진 RGB 색 값을 만듭니다 `2.5` `temperature` . 온도가 변경 됨에 따라 색은 *빨간색* 의 다른 음영으로 변경 됩니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

모든 색 매개 변수가 숫자인 경우 식으로 래핑할 필요가 없습니다 `literal` . 예를 들면 다음과 같습니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> 문자열 색 값은 메서드를 사용 하 여 색으로 변환할 수 있습니다 `android.graphics.Color.parseColor` . 다음에서는 16 진수 색 문자열을 계층과 함께 사용할 수 있는 색 식으로 변환 합니다.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>문자열 연산자 식

문자열 연산자 식은 사례를 연결 하 고 변환 하는 등의 문자열에 대해 변환 작업을 수행 합니다.

| 식 | 반환 형식 | 설명 |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | 문자열 | 여러 문자열을 연결 합니다. 각 값은 문자열 이어야 합니다. `toString`필요한 경우 형식 식을 사용 하 여 다른 값 형식을 문자열로 변환 합니다. |
| `downcase(string)` \| `downcase(Expression)` | 문자열 | 지정된 문자열을 소문자로 변환합니다. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | 입력 문자열이 현재 글꼴 스택에서 지 원하는 문자 집합을 사용 하는지 여부를 확인 합니다. `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | 문자열 | 제공 된 병합기에서 사용 하는 로캘의 IETF 언어 태그를 반환 합니다. 이는 기본 시스템 로캘을 확인 하거나 요청 된 로캘이 성공적으로 로드 되었는지 여부를 확인 하는 데 사용할 수 있습니다. |
| `upcase(string)` \| `upcase(Expression)` | 문자열 | 지정된 문자열을 대문자로 변환합니다. |

**예제**

다음 예에서는 `temperature` point 기능의 속성을 문자열로 변환 하 고 "° f"을 끝에 연결 합니다.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

위의 식은 아래 이미지에 표시 된 것 처럼 텍스트 "64 ° F"이 맨 위에 겹쳐서 표시 된 상태로 맵에 핀을 렌더링 합니다.

![문자열 연산자 식 예제](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>보간 및 단계 식

보간 및 단계 식은 보간된 곡선이 나 step 함수를 따라 값을 계산 하는 데 사용할 수 있습니다. 이러한 식은 숫자 값을 입력으로 반환 하는 식 (예:)을 사용 `get("temperature")` 합니다. 입력 값은 입력 및 출력 값 쌍에 대해 계산 되어 보간된 커브나 단계 함수에 가장 적합 한 값을 결정 합니다. 출력 값을 "중지" 라고 합니다. 각 중지의 입력 값은 숫자 여야 하며 오름차순 이어야 합니다. 출력 값은 숫자, 숫자 배열 또는 색 이어야 합니다.

### <a name="interpolate-expression"></a>보간 식

`interpolate`보간를 사용 하 여 값의 연속, 부드러운 값 집합을 계산할 수 있습니다. `interpolate`색 값을 반환 하는 식은 결과 값이에서 선택 되는 색 그라데이션을 생성 합니다. `interpolate`식의 형식은 다음과 같습니다.

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

식에 사용할 수 있는 보간 방법에는 다음 세 가지 유형이 있습니다 `interpolate` .

| Name | 설명 |
|------|-------------|
| `linear()` | 중지점의 쌍 사이를 선형으로 보간합니다.  |
| `exponential(number)` \| `exponential(Expression)` | 정지 사이에 지를 보간합니다. "Base"가 지정 되 고 출력이 늘어나는 속도를 제어 합니다. 값이 높을수록 출력이 범위의 높은 쪽 끝에서 증가 합니다. 1에 가까운 "기본" 값은 보다 선형적으로 향상 되는 출력을 생성 합니다.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | 지정 된 제어점에서 정의 하는 [입방 형 3 차원 곡선](https://developer.mozilla.org/docs/Web/CSS/timing-function) 을 사용 하 여 보간합니다. |

`stop`식의 형식은 `stop(stop, value)` 입니다.

다음은 이러한 여러 유형의 보간의 예입니다.

| 선형  | 지수 | 입방 형 3 차원 |
|---------|-------------|--------------|
| ![선형 보간 그래프](media/how-to-expressions/linear-interpolation.png) | ![지 수 보간 그래프](media/how-to-expressions/exponential-interpolation.png) | ![입방 형 3 차원 보간 그래프](media/how-to-expressions/bezier-curve-interpolation.png) |

**예제**

다음 예에서는 식을 사용 하 여 `linear interpolate` `bubbleColor` point 기능의 속성을 기반으로 거품형 계층의 속성을 설정 합니다 `temperature` . `temperature`값이 60 보다 작은 경우 "blue"가 반환 됩니다. 60 ~ 70 보다 작은 경우에는 노란색이 반환 됩니다. 70 ~ 80 보다 작은 경우 "주황색" ()이 반환 됩니다 `#FFA500` . 80 이상인 경우 "red"가 반환 됩니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

다음 이미지는 위의 식에서 색을 선택 하는 방법을 보여 줍니다.

![보간 식 예제](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Step 식

`step`식은 중지로 정의 된 [부분 일정 분포 상수 함수](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) 를 평가 하 여 불연속의 단계별 결과 값을 계산 하는 데 사용할 수 있습니다.

`interpolate`식의 형식은 다음과 같습니다.

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

단계 식은 입력 값 바로 앞에 있는 stop의 출력 값 또는 입력이 첫 번째 중지 보다 작은 경우 첫 번째 입력 값을 반환 합니다.

**예제**

다음 예에서는 식을 사용 하 여 `step` `bubbleColor` point 기능의 속성을 기반으로 거품형 계층의 속성을 설정 합니다 `temperature` . `temperature`값이 60 보다 작은 경우 "blue"가 반환 됩니다. 60 ~ 70 보다 작은 경우 "노란색"이 반환 됩니다. 70 ~ 80 보다 작은 경우 "주황색"이 반환 됩니다. 80 이상인 경우 "red"가 반환 됩니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

다음 이미지는 위의 식에서 색을 선택 하는 방법을 보여 줍니다.

![Step 식 예](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>계층 관련 식

특정 레이어에만 적용 되는 특수 식입니다.

### <a name="heat-map-density-expression"></a>열 지도 밀도 식

열 지도 밀도 식은 열 지도 계층의 각 픽셀에 대 한 열 지도 밀도 값을 검색 하 고로 정의 됩니다 `heatmapDensity` . 이 값은에서 사이의 숫자 `0` 입니다 `1` . 또는 식과 함께 사용 되어 `interpolation` `step` 열 지도를 색으로 지정 하는 데 사용 되는 색 그라데이션을 정의 합니다. 이 식은 `heatmapColor` 열 지도 계층의 옵션에만 사용할 수 있습니다.

> [!TIP]
> 보간 식의 인덱스 0에 있는 색 또는 단계 색의 기본 색은 데이터가 없는 영역의 색을 정의 합니다. 인덱스 0에 있는 색은 배경색을 정의 하는 데 사용할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다.

**예제**

이 예제에서는 liner 보간 식을 사용 하 여 열 지도를 렌더링 하기 위한 부드러운 색 그라데이션을 만듭니다.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

부드러운 그라데이션을 사용 하 여 열 지도를 색상화 하는 것 외에도 식을 사용 하 여 범위 집합 내에서 색을 지정할 수 있습니다 `step` . 열 지도를 색으로 하는 식을 사용 하 여 `step` 윤곽선 또는 방사형 스타일 맵과 유사한 범위로 밀도를 시각적으로 나눕니다.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

자세한 내용은 [열 지도 계층 추가](map-add-heat-map-layer-android.md) 설명서를 참조 하세요.

### <a name="line-progress-expression"></a>줄 진행률 식

줄 진행률 식은 선 계층에서 그라데이션 선을 따라 진행률을 검색 하 고로 정의 됩니다 `lineProgress()` . 이 값은 0에서 1 사이의 숫자입니다. 또는 식과 함께 사용 `interpolation` `step` 됩니다. 이 식은 `strokeGradient` 선 계층의 옵션에만 사용할 수 있습니다.

> [!NOTE]
> `strokeGradient`선 계층의 옵션을 사용 하려면 `lineMetrics` 데이터 소스의 옵션을로 설정 해야 합니다 `true` .

**예제**

이 예제에서는 식을 사용 하 여 `lineProgress()` 선의 스트로크에 색 그라데이션을 적용 합니다.

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[라이브 예제 참조](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>텍스트 필드 형식 식

`format`식에는 `textField` 기호 계층의 옵션과 함께 사용 하 여 혼합 텍스트 서식을 제공할 수 있습니다. 이 식은 `formatEntry` 문자열을 지정 하는 하나 이상의 식과 `formatOptions` 텍스트 필드에 추가할의 집합을 사용 합니다.

| 식 | 설명 |
|------------|-------------|
| `format(Expression...)` | 혼합 형식 텍스트 필드 항목에 사용할 주석이 포함 된 서식 있는 텍스트를 반환 합니다. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | 식에 사용할 형식이 지정 된 문자열 항목을 반환 합니다 `format` . |

사용할 수 있는 형식 옵션은 다음과 같습니다.

| 식 | 설명 |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | 글꼴 크기의 배율 인수를 지정 합니다. 지정 된 경우이 값은 `textSize` 개별 문자열의 속성을 재정의 합니다. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | 렌더링할 때 텍스트에 적용할 색을 지정 합니다. |

**예제**

다음 예에서는 굵은 글꼴을 추가 하 고 기능 속성의 글꼴 크기를 확장 하 여 텍스트 필드의 서식을 지정 합니다 `title` . 또한이 예제에서는 `subTitle` 축소 된 글꼴 크기를 사용 하 여 기능의 속성을 줄 바꿈에 추가 합니다.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

이 계층은 아래 이미지와 같이 point 기능을 렌더링 합니다.

![서식이 지정 된 텍스트 필드가 있는 Point 기능 이미지](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>확대/축소 식

`zoom`식은 렌더링 시 지도의 현재 확대/축소 수준을 검색 하는 데 사용 되 고로 정의 됩니다 `zoom()` . 이 식은 지도의 최소 및 최대 확대/축소 수준 범위 사이의 숫자를 반환 합니다. 웹 및 Android에 대 한 대화형 맵 컨트롤 Azure Maps 25 개의 확대/축소 수준 (0-24)을 지원 합니다. 식을 사용 `zoom` 하면 지도의 확대/축소 수준을 변경할 때 스타일을 동적으로 수정할 수 있습니다. `zoom`식에는 및 식만 사용할 수 있습니다 `interpolate` `step` .

**예제**

기본적으로 열 지도 계층에 렌더링 되는 데이터 요소의 반지름에는 모든 확대/축소 수준에 대 한 고정 픽셀 반지름이 있습니다. 지도가 확대 되 면 데이터가 함께 집계 되 고 열 지도 계층이 다르게 보입니다. 각 `zoom` 데이터 요소가 지도의 동일한 실제 영역을 포함 하도록 각 확대/축소 수준에 대 한 반지름의 크기를 조정 하는 데 식을 사용할 수 있습니다. 열 지도 계층을 더 정적이 고 일관 되 게 보이게 합니다. 지도의 각 확대/축소 수준에는 이전 확대/축소 수준과 가로 및 세로로 두 배의 픽셀이 있습니다. 각 확대/축소 수준에서 두 배가 되도록 반경 크기를 조정 하면 모든 확대/축소 수준에서 일치 하는 열 지도를 만듭니다. 식에 식을 사용 하 여이를 수행할 수 있습니다 `zoom` `base 2 exponential interpolation` . 최소 확대/축소 수준에 대해 픽셀 반지름이 설정 되 고 아래와 같이 계산 된 최대 확대/축소 수준에 대해 배율이 조정 된 반지름이 사용 됩니다 `2 * Math.pow(2, minZoom - maxZoom)` .

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>변수 바인딩 식

변수 바인딩 식은 계산 결과를 변수에 저장 합니다. 따라서 계산 결과를 식의 다른 위치에서 여러 번 참조할 수 있습니다. 많은 계산을 포함 하는 식에 대 한 유용한 최적화입니다.

| 식 | 반환 형식 | Description |
|--------------|---------------|--------------|
| `let(Expression... input)` | | `var`결과를 반환 하는 자식 식에서 식에 사용할 변수로 하나 이상의 값을 저장 합니다. |
| `var(Expression expression)` \| `var(string variableName)` | Object | 식을 사용 하 여 만든 변수를 참조 `let` 합니다. |

**예제**

이 예에서는 온도 비율을 기준으로 수익을 계산 하는 식을 사용한 다음 식을 사용 하 여 `case` 이 값에 대 한 다양 한 부울 연산을 계산 합니다. `let`식은 한 번만 계산 하면 되는 온도 비율을 기준으로 수익을 저장 하는 데 사용 됩니다. `var`식에서이 변수를 다시 계산할 필요 없이 자주 참조 합니다.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>다음 단계

식을 지 원하는 계층에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [열 지도 추가](map-add-heat-map-layer-android.md)
