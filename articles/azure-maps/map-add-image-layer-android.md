---
title: Android 맵에 이미지 계층 추가 | Microsoft Azure 맵
description: 지도에 이미지를 추가 하는 방법을 알아봅니다. Azure Maps Android SDK를 사용 하 여 고정 된 좌표 집합에서 이미지 레이어 및 오버레이 이미지를 사용자 지정 하는 방법을 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054692"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>지도에 이미지 계층 추가 (Android SDK)

이 문서에서는 고정 좌표 집합에 이미지를 오버레이 하는 방법을 보여 줍니다. 지도에 중첩 될 수 있는 다양 한 이미지 유형의 몇 가지 예는 다음과 같습니다.

* 드 론에서 캡처된 이미지
* Floorplans 빌드
* 기록 또는 기타 특수 지도 이미지
* 작업 사이트의 청사진

> [!TIP]
> 이미지 계층은 지도에서 이미지를 오버레이 하는 쉬운 방법입니다. 큰 이미지는 많은 메모리를 사용 하 고 잠재적으로 성능 문제를 일으킬 수 있습니다. 이 경우 이미지를 타일로 나누고 타일 계층으로 지도에 로드 하는 것이 좋습니다.

## <a name="add-an-image-layer"></a>이미지 레이어 추가

다음 코드는 map에서 [1922의 New Jersey 뉴어크 맵](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 이미지를 오버레이 합니다. 이 이미지는 `drawable` 프로젝트의 폴더에 추가 됩니다. 이미지 계층은 네 모퉁이에 대 한 이미지와 좌표를 형식으로 설정 하 여 생성 됩니다 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` . 레이어 아래에 이미지 레이어를 추가 하 `label` 는 것이 바람직한 경우가 많습니다.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

또는 온라인에서 호스트 되는 이미지에 대 한 URL을 지정할 수 있습니다. 그러나 시나리오에서 허용 하는 경우 이미지를 프로젝트 폴더에 추가 하면 `drawable` 이미지를 로컬에서 사용할 수 있고 다운로드할 필요가 없기 때문에 더 빨리 로드 됩니다.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

다음 스크린샷에서는 이미지 계층을 사용 하 여 중첩 된 1922에서 뉴어크 새 Jersey의 맵을 보여 줍니다.

![이미지 계층을 사용 하 여 오버레이 된 뉴어크, New Jersey, 1922의 맵](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>KML 파일을 그라운드 오버레이로 가져오기

이 샘플에서는 KML 그라운드 오버레이 정보를 지도에 이미지 계층으로 추가 하는 방법을 보여 줍니다. KML 접지 오버레이는 북쪽, 남쪽, 동쪽 및 서 부 좌표와 시계 반대 방향 회전을 제공 합니다. 그러나 이미지 계층에는 이미지의 각 모퉁이에 대 한 좌표가 필요 합니다. 이 샘플의 KML 그라운드 오버레이는 Chartres cathedral에 대 한 것 이며 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)에서 원본입니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

이 코드에서는 클래스의 정적 메서드를 사용 합니다 `getCoordinatesFromEdges` `ImageLayer` . 이 메서드는 KML 그라운드 오버레이의 북쪽, 남부, 동쪽, 서 부 및 회전 정보를 사용 하 여 이미지의 네 모퉁이를 계산 합니다.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

다음 스크린샷은 이미지 계층을 사용 하 여 KML 그라운드 오버레이가 중첩 된 지도를 보여 줍니다.

![이미지 계층을 사용 하 여 KML 그라운드 오버레이가 중첩 된 지도](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> `getPixels` `getPositions` 이미지 계층 클래스의 및 메서드를 사용 하 여 배치 된 이미지 계층의 지리적 좌표와 로컬 이미지 픽셀 좌표로 변환 합니다.

## <a name="next-steps"></a>다음 단계

지도에서 이미지를 오버레이 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [타일 레이어 추가](how-to-add-tile-layer-android-map.md)