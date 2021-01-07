---
title: Android maps에 다각형 계층 추가 | Microsoft Azure 맵
description: 지도에 다각형 또는 원을 추가 하는 방법에 대해 알아봅니다. Azure Maps Android SDK를 사용 하 여 기하학적 모양을 사용자 지정 하 고 쉽게 업데이트 하 고 유지 관리할 수 있도록 하는 방법을 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679488"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>지도에 다각형 계층 추가 (Android SDK)

이 문서에서는 다각형 계층을 사용하여 맵에 `Polygon` 및 `MultiPolygon` 기능 기하 도형의 영역을 렌더링하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은 maps 이벤트 처리기에 삽입할 수 있습니다 `onReady` .

## <a name="use-a-polygon-layer"></a>다각형 계층 사용

다각형 계층이 데이터 원본에 연결되고 맵에 로드되면 `Polygon` 및 `MultiPolygon` 기능을 사용하여 영역을 렌더링합니다. 다각형을 만들려면 데이터 소스에 다각형을 추가 하 고 클래스를 사용 하 여 다각형 계층으로 렌더링 `PolygonLayer` 합니다.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

다음 스크린샷은 다각형 계층을 사용 하 여 다각형의 영역을 렌더링 하는 위의 코드를 보여 줍니다.

![채우기 영역이 렌더링 된 다각형](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>다각형 및 선 계층을 함께 사용

선 계층은 다각형의 윤곽선을 렌더링하는 데 사용됩니다. 다음 코드 샘플에서는 이전 예와 같이 다각형을 렌더링하지만, 이제는 선 계층을 추가합니다. 이 선 계층은 데이터 원본에 연결된 두 번째 계층입니다.  

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

다음 스크린샷에서는 선 계층을 사용 하 여 윤곽선이 렌더링 된 다각형을 렌더링 하는 위의 코드를 보여 줍니다.

![채우기 영역과 윤곽선이 렌더링 된 다각형](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> 선 계층을 사용 하 여 다각형의 개요를 표시 하는 경우 각 점 배열의 시작점과 끝점이 동일 하도록 다각형의 모든 고리를 닫아야 합니다. 이 작업을 수행 하지 않으면 선 계층이 다각형의 마지막 점을 첫 번째 점에 연결 하지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)
