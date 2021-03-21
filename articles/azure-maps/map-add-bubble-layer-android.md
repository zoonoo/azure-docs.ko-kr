---
title: Android maps에 거품형 계층 추가 | Microsoft Azure 맵
description: 지도에서 요소를 고정 크기의 원으로 렌더링 하는 방법에 대해 알아봅니다. 이 목적을 위해 Azure Maps Android SDK를 사용 하 여 거품형 계층을 추가 하 고 사용자 지정 하는 방법을 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: f3c175f30c5c0e6206f4fee274e0f3f000e55a74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100171"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>지도에 거품형 계층 추가 (Android SDK)

이 문서에서는 데이터 원본의 점 데이터를 지도에서 거품형 계층으로 렌더링 하는 방법을 보여 줍니다. 거품형 계층은 고정 픽셀 반지름이 있는 지도에서 요소를 원으로 렌더링 합니다.

> [!TIP]
> 기본적으로 거품형 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 점 기 하 도형 기능만 렌더링 하도록 계층을 제한 하려면 `filter` 계층의 옵션을로 설정 `eq(geometryType(), "Point")` 합니다. MultiPoint 기능도 포함 하려면 `filter` 계층의 옵션을로 설정 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` 합니다.

## <a name="prerequisites"></a>필수 조건

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은 maps 이벤트 처리기에 삽입할 수 있습니다 `onReady` .

## <a name="add-a-bubble-layer"></a>거품형 레이어 추가

다음 코드는 데이터 소스에 점의 배열을 로드 합니다. 그런 다음 데이터 요소를 거품형 계층에 연결 합니다. 거품형 계층은 각 거품의 반지름을 5 픽셀 및 흰색 채우기 색으로 렌더링 합니다. 그리고, 파란색의 스트로크 색 및 6 픽셀의 스트로크 너비입니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create point locations.
val points: Array<Point> = arrayOf<Point>(
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
)

//Add multiple points to the data source.
source.add(points)

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
val layer = BubbleLayer(
    source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
)

map.layers.add(layer)
```

::: zone-end

다음 스크린샷은 거품형 계층에서 위의 코드를 렌더링 하는 위치를 보여 줍니다.

![거품형 계층을 사용 하 여 렌더링 된 점이 있는 지도](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>거품형 계층을 사용하여 레이블 표시

이 코드는 거품형 계층을 사용 하 여 지도에서 점을 렌더링 하는 방법을 보여 줍니다. 기호 계층을 사용 하 여 레이블을 렌더링 하는 방법을 보여 줍니다. 기호 계층의 아이콘을 숨기려면 `iconImage` 옵션을로 설정 `"none"` 합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641, 47.627631))

//Add a bubble layer.
map.layers.add(
    BubbleLayer(
        source,
        bubbleRadius(5f),
        bubbleColor("white"),
        bubbleStrokeColor("#4288f7"),
        bubbleStrokeWidth(6f)
    )
)

//Add a symbol layer to display text, hide the icon image.
map.layers.add(
    SymbolLayer(
        source,  //Hide the icon image.
        iconImage("none"),
        textField("Museum of History & Industry (MOHAI)"),
        textColor("#005995"),
        textOffset(arrayOf(0f, -2.2f))
    )
)
```

::: zone-end

다음 스크린샷에서는 위의 코드 렌더링 거품형 계층의 지점과 기호 계층이 있는 점의 텍스트 레이블을 보여 줍니다.

![거품형 계층을 사용 하 여 렌더링 된 점이 있는 지도 및 기호 계층이 있는 텍스트 레이블](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [기능 정보 표시](display-feature-information-android.md)
