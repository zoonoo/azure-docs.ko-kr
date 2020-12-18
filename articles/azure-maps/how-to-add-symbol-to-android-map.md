---
title: Android maps에 기호 계층 추가 | Microsoft Azure 맵
description: 지도에 표식을 추가 하는 방법에 대해 알아봅니다. Azure Maps Android SDK를 사용 하 여 데이터 원본의 지점 기반 데이터를 포함 하는 기호 계층을 추가 하는 예제를 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679349"
---
# <a name="add-a-symbol-layer-android-sdk"></a>기호 계층 추가 (Android SDK)

이 문서에서는 Azure Maps Android SDK를 사용 하 여 데이터 소스의 요소 데이터를 맵의 기호 계층으로 렌더링 하는 방법을 보여 줍니다. 기호 레이어는 위치를 지도에 이미지 및 텍스트로 렌더링 합니다.

> [!TIP]
> 기본적으로 기호 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 점 기 하 도형 기능만 렌더링 하도록 계층을 제한 하려면 `filter` 계층의 옵션을로 설정 `eq(geometryType(), "Point")` 합니다. MultiPoint 기능도 포함 하려면 `filter` 계층의 옵션을로 설정 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` 합니다.

## <a name="prerequisites"></a>필수 조건

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은 maps 이벤트 처리기에 삽입할 수 있습니다 `onReady` .

## <a name="add-a-symbol-layer"></a>기호 레이어 추가

지도에 기호 계층을 추가 하려면 몇 가지 단계를 수행 해야 합니다. 먼저 데이터 원본을 만들어 맵에 추가 합니다. 기호 계층을 만듭니다. 그런 다음 데이터 소스를 기호 계층에 전달 하 여 데이터 소스에서 데이터를 검색 합니다. 마지막으로 데이터 소스에 데이터를 추가 하 여 렌더링할 항목이 있는지를 파악 합니다.

아래 코드는 로드 된 후 맵에 추가 해야 하는 항목을 보여 줍니다. 이 샘플에서는 기호 계층을 사용 하 여 지도에서 단일 점을 렌더링 합니다.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

지도에 추가할 수 있는 점 데이터에는 다음 세 가지 유형이 있습니다.

- GeoJSON Point geometry-이 개체는 점의 좌표를 포함 하 고 다른 것은 포함 하지 않습니다. `Point.fromLngLat`정적 메서드를 사용 하 여 이러한 개체를 쉽게 만들 수 있습니다.
- GeoJSON MultiPoint geometry-이 개체는 여러 점의 좌표를 포함 하 고 다른 요소는 포함 하지 않습니다. 점의 배열을 클래스에 전달 `MultiPoint` 하 여 이러한 개체를 만듭니다.
- GeoJSON 기능-이 개체는 모든 GeoJSON 기 하 도형 및 기 하 도형에 연결 된 메타 데이터를 포함 하는 속성 집합으로 구성 됩니다.

자세한 내용은 지도에 데이터를 만들고 추가 하는 방법에 대 한 [데이터 원본 만들기](create-data-source-android-sdk.md) 문서를 참조 하세요.

다음 코드 샘플에서는 GeoJSON Point geometry를 만들어 GeoJSON 기능에 전달 하 고 `title` 해당 속성에 값을 추가 합니다. `title`속성은 지도의 기호 아이콘 위에 텍스트로 표시 됩니다.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

다음 스크린샷에서는 위의 코드 렌더링 기호 계층이 있는 아이콘 및 텍스트 레이블을 사용 하 여 point 기능을 보여 줍니다.

![점 기능에 대 한 아이콘 및 텍스트 레이블을 표시 하는 기호 계층을 사용 하 여 렌더링 된 점이 있는 지도](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> 기본적으로 기호 레이어는 겹치는 기호를 숨겨 기호 렌더링을 최적화 합니다. 확대 하는 동안 숨겨진 기호가 표시 됩니다. 이 기능을 사용 하지 않도록 설정 하 고 모든 기호를 항상 렌더링 하려면 `iconAllowOverlap` 및 `textAllowOverlap` 옵션을로 설정 `true` 합니다.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>기호 계층에 사용자 지정 아이콘 추가

기호 계층은 WebGL을 사용하여 렌더링됩니다. 아이콘 이미지와 같은 이러한 모든 리소스는 WebGL 컨텍스트에 로드해야 하기 때문입니다. 이 샘플에서는 지도 리소스에 사용자 지정 아이콘을 추가 하는 방법을 보여 줍니다. 이 아이콘은 지도에서 사용자 지정 기호를 사용 하 여 점 데이터를 렌더링 하는 데 사용 됩니다. 기호 계층의 `textField` 속성에는 식을 지정해야 합니다. 이 경우 온도 속성을 렌더링 하려고 합니다. 온도는 숫자 이므로 문자열로 변환 해야 합니다. 또한 "° F"를 추가 하려고 합니다. 식을 사용 하 여이 연결을 수행할 수 있습니다. `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

이 샘플에서는 다음 이미지가 응용 프로그램의 그릴 폴더에 로드 되었습니다.

| ![날씨 아이콘 비 샤워 이미지](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

다음 스크린샷에서는 위의 코드 렌더링 기호 계층이 있는 사용자 지정 아이콘 및 서식 있는 텍스트 레이블을 사용 하 여 point 기능을 보여 줍니다.

![지점 기능에 대 한 사용자 지정 아이콘 및 서식 있는 텍스트 레이블을 표시 하는 기호 계층을 사용 하 여 렌더링 된 점이 있는 지도](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> 기호 계층이 있는 텍스트만 렌더링 하려면 `iconImage` 아이콘 옵션의 속성을로 설정 하 여 아이콘을 숨길 수 있습니다 `"none"` .

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [기능 정보 표시](display-feature-information-android.md)
