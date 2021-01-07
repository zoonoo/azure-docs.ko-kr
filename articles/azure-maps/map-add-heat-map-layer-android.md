---
title: Android maps에 열 지도 계층 추가 | Microsoft Azure 맵
description: 열 지도를 만드는 방법에 대해 알아봅니다. Azure MapsAndroid SDK를 사용 하 여 지도에 열 지도 계층을 추가 하는 방법을 참조 하세요. 열 지도 계층을 사용자 지정 하는 방법을 알아보세요.
author: rbrundritt
ms.author: richbrun
ms.date: 12/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 4de59bd0b2a9dc9b11acf55a59b82724d2c7b862
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681782"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>열 지도 계층 추가 (Android SDK)

지점 밀도 맵이라고도 하는 열 지도는 데이터 시각화의 한 형식입니다. 색 범위를 사용 하 여 데이터 밀도를 나타내는 데 사용 되며 지도에 "핫 스폿" 데이터를 표시 합니다. 열 맵은 많은 수의 점이 있는 데이터 집합을 렌더링 하는 좋은 방법입니다. 

수십 개의 요소를 기호로 렌더링 하면 대부분의 맵 영역을 처리할 수 있습니다. 이 경우 많은 기호가 겹칠 수 있습니다. 데이터를 보다 잘 이해 하기 어렵게 만듭니다. 그러나이 데이터 집합을 열 맵으로 시각화 하면 각 데이터 요소의 밀도 및 상대 밀도를 쉽게 확인할 수 있습니다.

다음과 같은 다양 한 시나리오에서 열 지도를 사용할 수 있습니다.

- **온도 데이터**: 두 데이터 요소 사이의 온도에 대 한 근사치을 제공 합니다.
- **노이즈 센서에 대 한 데이터**: 센서가 있는 노이즈의 강도 뿐만 아니라 거리를 통한 분산에 대 한 통찰력을 제공할 수도 있습니다. 한 사이트의 노이즈 수준이 높지 않을 수 있습니다. 여러 센서의 노이즈 범위 영역이 겹치면 겹치는 영역에서 더 높은 의미의 소음을 겪을 수 있습니다. 따라서 겹쳐진 영역은 열 지도에 표시 됩니다.
- **GPS 추적**: 속도를 가중치 높이 맵으로 포함 합니다. 여기서 각 데이터 요소의 강도는 속도를 기반으로 합니다. 예를 들어이 기능을 통해 차량의 속도를 빠르게 확인할 수 있습니다.

> [!TIP]
> 열 지도 계층은 기본적으로 데이터 원본에 있는 모든 기 하 도형의 좌표를 렌더링 합니다. 점 기 하 도형 기능만 렌더링 하도록 계층을 제한 하려면 `filter` 계층의 옵션을로 설정 `eq(geometryType(), "Point")` 합니다. MultiPoint 기능도 포함 하려면 `filter` 계층의 옵션을로 설정 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` 합니다.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>전제 조건

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은 maps 이벤트 처리기에 삽입할 수 있습니다 `onReady` .

## <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

지점의 데이터 소스를 열 맵으로 렌더링 하려면 데이터 소스를 클래스의 인스턴스로 전달 하 `HeatMapLayer` 고 맵에 추가 합니다.

다음 코드 샘플에서는 지난 주에 지진의 GeoJSON 피드를 로드 하 고이를 열 맵으로 렌더링 합니다. 각 데이터 요소는 모든 확대/축소 수준에서 10 픽셀의 반지름으로 렌더링 됩니다. 더 나은 사용자 환경을 보장 하기 위해 열 맵은 레이블 계층 아래에 있으므로 레이블이 명확 하 게 표시 됩니다. 이 샘플의 데이터는 [USGS 지진 위험 프로그램](https://earthquake.usgs.gov/)에서 가져온 것입니다. 이 샘플은 [데이터 원본 만들기](create-data-source-android-sdk.md) 문서에 제공 된 데이터 가져오기 유틸리티 코드 블록을 사용 하 여 웹에서 GeoJSON 데이터를 로드 합니다.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

다음 스크린샷에서는 위의 코드를 사용 하 여 열 지도를 로드 하는 맵을 보여 줍니다.

![최근 지진의 열 지도 계층을 포함 하는 지도](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>열 지도 계층 사용자 지정

이전 예제는 반경 및 불투명도 옵션을 설정하여 열 지도를 사용자 지정했습니다. 열 지도 계층은 다음을 비롯 한 사용자 지정을 위한 몇 가지 옵션을 제공 합니다.

- `heatmapRadius`: 각 데이터 요소를 렌더링할 픽셀 반지름을 정의 합니다. 반지름을 고정 숫자나 식으로 설정할 수 있습니다. 식을 사용 하 여 확대/축소 수준에 따라 반지름의 크기를 조정 하 고 지도의 일관 된 공간 영역을 나타낼 수 있습니다 (예: 5 마일 반지름).
- `heatmapColor`: 열 지도의 색이 지정 되는 방법을 지정 합니다. 색 그라데이션은 열 지도의 일반적인 기능입니다. 식을 사용 하 여 효과를 달성할 수 있습니다 `interpolate` . 열 지도를 색으로 표시 하는 식을 사용 하 여 `step` 밀도를 윤곽선 또는 방사형 스타일 맵과 비슷한 범위로 시각적으로 나눌 수도 있습니다. 이러한 색상표는 색을 최소에서 최대 밀도 값까지 정의합니다.

  열 맵의 색 값을 값에 대 한 식으로 지정 `heatmapDensity` 합니다. "보간" 식의 인덱스 0에 데이터가 정의 되어 있지 않은 영역의 색 또는 "단계별" 식의 기본 색입니다. 배경색을 정의 하는 데이 값을 사용할 수 있습니다. 일반적으로이 값은 투명 또는 반투명 검정으로 설정 됩니다. 

  색 식의 예는 다음과 같습니다.

  | 보간 색 식 | 단계별 색 식 |
  |--------------------------------|--------------------------|
  | 보간<br/>&nbsp;&nbsp;&nbsp;&nbsp;선형 (), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0, color (Color. TRANSPARENT)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.01, color (전경색)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.5, color (parseColor ("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (1, color (parseColor ("#00c3ff")))<br/>)` | 이동<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;색 (색 투명),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.01, color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.25, color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.5, color (Color. GREEN)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.5, color (노랑을)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (1, color (COLOR.RED))<br/>) |

- `heatmapOpacity`: 열 지도 계층이 불투명 하거나 투명 하 게 지정 되는 방식을 지정 합니다.
- `heatmapIntensity`: 열 지도의 전체 강도를 높이기 위해 각 데이터 요소의 가중치에 승수를 적용 합니다. 이로 인해 데이터 요소의 가중치가 다르므로 시각화를 쉽게 만들 수 있습니다.
- `heatmapWeight`: 기본적으로 모든 데이터 요소의 가중치는 1 이며 동일 하 게 가중치가 적용 됩니다. Weight 옵션은 승수 역할을 하며 숫자 또는 식으로 설정할 수 있습니다. 숫자가 가중치로 설정 된 경우 맵에 각 데이터 요소를 두 번 배치 하는 것과 동일 합니다. 예를 들어 가중치가 인 경우 `2` 밀도는 double입니다. 숫자가 가중치 옵션을 설정하면 밀도 옵션을 사용하는 것과 비슷한 방식으로 열 지도를 렌더링합니다.

  그러나 식을 사용 하는 경우 각 데이터 요소의 가중치는 각 데이터 요소의 속성을 기반으로 할 수 있습니다. 예를 들어 각 데이터 요소가 지진을 나타내는 경우 크기 값은 각 지진 데이터 요소에 대 한 중요 한 메트릭입니다. 지진은 항상 발생 하지만 대부분 낮은 크기를 가지 며,이는 발견 되지 않습니다. 식에서 크기 값을 사용 하 여 각 데이터 요소에 가중치를 할당 합니다. 크기 값을 사용 하 여 무게를 할당 하면 열 지도 내에서 지진의 의미를 보다 잘 표현할 수 있습니다.
- `minZoom` 및 `maxZoom` : 레이어가 표시 되어야 하는 확대/축소 수준 범위입니다.
- `filter`: 소스에서 검색 되 고 계층에서 렌더링 되는를 제한 하는 데 사용 되는 필터 식입니다.
- `sourceLayer`: 레이어에 연결 된 데이터 원본이 벡터 타일 원본인 경우 벡터 타일 내의 원본 계층을 지정 해야 합니다.
- `visible`: 계층을 숨기 거 나 표시 합니다.

다음은 liner 보간 식을 사용 하 여 부드러운 색 그라데이션을 만드는 열 지도의 예입니다. `mag`데이터에 정의 된 속성은 지 수 보간을 사용 하 여 각 데이터 요소의 가중치 또는 관련성을 설정 하는 데 사용 됩니다.

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

다음 스크린샷은 이전 열 지도 예제의 동일한 데이터를 사용 하는 위의 사용자 지정 열 지도 계층을 보여 줍니다.

![최근 지진의 사용자 지정 열 지도 계층으로 매핑](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>일관 된 확대/열 지도

기본적으로 열 지도 계층에 렌더링 되는 데이터 요소의 반지름에는 모든 확대/축소 수준에 대 한 고정 픽셀 반지름이 있습니다. 지도를 확대할 때 데이터가 함께 집계 되 고 열 지도 계층이 다르게 보입니다. 다음 비디오는 지도를 확대할 때 픽셀 반지름을 유지 하는 열 지도의 기본 동작을 보여 줍니다.

![일관 된 픽셀 크기를 보여 주는 열 지도 계층을 사용한 지도 확대/축소를 보여 주는 애니메이션](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

각 `zoom` 데이터 요소가 지도의 동일한 실제 영역을 포함 하도록 각 확대/축소 수준에 대 한 반지름의 크기를 조정 하려면 식을 사용 합니다. 이 식은 열 지도 계층을 더 정적이 고 일관 되 게 보이게 합니다. 지도의 각 확대/축소 수준에는 이전 확대/축소 수준과 가로 및 세로로 두 배의 픽셀이 있습니다.

각 확대/축소 수준에서 두 배가 되도록 반지름의 크기를 조정 하면 모든 확대/축소 수준에서 일치 하는 열 지도를 만듭니다. 이러한 크기 조정을 적용 하려면 `zoom` `exponential interpolation` 다음 샘플과 같이 계산 된 최대 확대/축소 수준에 대 한 픽셀 반지름이 설정 된 기본 2 식에를 사용 `2 * Math.pow(2, minZoom - maxZoom)` 합니다. 지도를 확대 하 여 확대/축소 수준으로 열 지도의 크기를 조정 하는 방법을 확인 합니다.

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

다음 비디오에서는 확대/축소 수준에서 일관 된 열 지도 렌더링을 만들기 위해 지도를 확대 하는 동안 반지름을 확장 하는 위의 코드를 실행 하는 맵을 보여 줍니다.

![일관 된 지리 공간적 크기를 보여 주는 열 지도 계층을 사용한 지도 확대/축소를 보여 주는 애니메이션](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-android-sdk.md)
