---
title: Android maps에서 지도 스타일 설정 | Microsoft Azure 맵
description: 지도의 스타일을 설정 하는 두 가지 방법을 알아봅니다. 레이아웃 파일 또는 작업 클래스에서 Azure Maps Android SDK를 사용 하 여 스타일을 조정 하는 방법을 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aef8fbacf8302fb5dd4b5fe28afc615c6bf56090
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100987"
---
# <a name="set-map-style-android-sdk"></a>지도 스타일 설정 (Android SDK)

이 문서에서는 Azure Maps Android SDK를 사용 하 여 지도 스타일을 설정 하는 두 가지 방법을 보여 줍니다. Azure Maps에는 6 가지 맵 스타일을 선택할 수 있습니다. 지원 되는 지도 스타일에 대 한 자세한 내용은 [Azure Maps에서 지원 되는 맵 스타일](supported-map-styles.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료 해야 합니다.

## <a name="set-map-style-in-the-layout"></a>레이아웃에서 지도 스타일 설정

지도 컨트롤을 추가할 때 작업 클래스의 레이아웃 파일에서 지도 스타일을 설정할 수 있습니다. 다음 코드는 중심 위치, 확대/축소 수준 및 지도 스타일을 설정 합니다.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

다음 스크린샷은 회색조 짙은 스타일로도로 지도를 표시 하는 위의 코드를 보여 줍니다.

![회색조 어두운도로 지도 스타일을 사용 하 여 지도](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>코드에서 지도 스타일 설정

맵의 메서드를 사용 하 여 프로그래밍 방식으로 코드에서 지도 스타일을 설정할 수 있습니다 `setStyle` . 다음 코드는 `setCamera` map 메서드와 지도 스타일을 사용 하 여 센터 위치 및 확대/축소 수준을로 설정 합니다 `SATELLITE_ROAD_LABELS` .

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS))
}
```

::: zone-end

다음 스크린샷은 위성도로 지도 레이블을 표시 하는 위의 코드를 보여 줍니다.

![위성도로 지도 레이블 스타일](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>지도 카메라 설정

지도 카메라는 지도에 표시 되는 지도의 부분을 제어 합니다. 카메라는 프로그래밍 방식으로 코드에서 레이아웃에 있을 수 있습니다. 코드에서 설정할 때 맵의 위치를 설정 하는 두 가지 주요 방법이 있습니다. 중심 및 확대/축소를 사용 하거나 경계 상자를 전달 합니다. 다음 코드에서는 및를 사용할 때 모든 선택적 카메라 옵션을 설정 하는 방법을 보여 줍니다 `center` `zoom` .

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

데이터 집합에 대 한 지도에 집중 하는 것이 바람직한 경우가 종종 있습니다. 경계 상자는 메서드를 사용 하 여 기능에서 계산 `MapMath.fromData` 되며 `bounds` map 카메라의 옵션으로 전달 될 수 있습니다. 경계 상자를 기준으로 지도 보기를 설정 하는 경우 `padding` 거품 또는 기호로 렌더링 되는 지점의 픽셀 크기를 고려 하는 값을 지정 하는 것이 유용한 경우가 많습니다. 다음 코드에서는 경계 상자를 사용 하 여 카메라의 위치를 설정 하는 경우 모든 옵션 카메라 옵션을 설정 하는 방법을 보여 줍니다.

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

경계 상자의 가로 세로 비율은 지도의 가로 세로 비율과 동일 하지 않을 수 있습니다. 이와 같이 맵은 전체 경계 상자 영역을 표시 하지만 세로 또는 가로로만 표시 되는 경우가 많습니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)
