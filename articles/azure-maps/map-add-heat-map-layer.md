---
title: Azure Maps에 열 지도 계층 추가 | Microsoft Docs
description: How to add a heat map layer to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f7115e7c8b95efd0e3bbc8a788528878c2d1f092
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484297"
---
# <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

열 지도(또는 요소 밀도 맵)는 여러 색을 사용하여 데이터 밀도를 나타내기 위해 사용하는 일종의 데이터 시각화입니다. 맵에 데이터 “핫 스폿”을 표시하기 위해 사용되는 경우가 많으며 큰 요소 데이터 세트를 렌더링하는 좋은 방법입니다.  For example, rendering tens of thousands of points within the map view as symbols, covers most of the map area and would result in many symbols overlapping each other, making it difficult to gain much insight into the data. 하지만, 이 동일한 데이터 세트를 열 지도로 시각화하면 가장 밀도가 높은 요소 데이터가 어디인지, 그리고 다른 영역에 대한 상대 밀도를 쉽게 파악할 수 있습니다. 열 지도를 사용하는 많은 시나리오가 있으며, 다음은 몇 가지 예입니다.

- 온도 데이터는 두 데이터 요소 간 온도에 대한 근사치를 제공하므로 일반적으로 열 지도로 렌더링됩니다.
- 노이즈 센서에 대한 데이터를 열 지도로 렌더링하면 센서가 있는 곳의 노이즈 강도를 보여줄 뿐만 아니라 장거리 열 손실에 대한 분석 정보를 제공합니다. 한 사이트의 노이즈 수준은 높지 않을 수 있지만, 여러 센서의 노이즈 커버리지 영역이 겹치는 경우 이 겹치는 영역에 더 높은 노이즈 수준이 발생하여 열 지도에 표시될 수 있습니다.
- Visualizing a GPS trace that includes the speed as a weighted height map where the intensity of each data point is based on the speed is a great way to see where the vehicle was speeding.

> [!TIP]
> 기본적으로 열 지도 레이어는 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. To limit the layer so that it only renders point geometry features, set the `filter` property of the layer to `['==', ['geometry-type'], 'Point']` or `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` if you want to include MultiPoint features as well.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

요소의 데이터 원본을 열 지도로 렌더링하려면 데이터 원본을 `HeatMapLayer` 클래스 인스턴스로 전달하여 맵에 추가하세요(그림 참조).

In the following code, each heat point has a radius of 10 pixels at all zoom levels. When adding the heat map layer to the map, this sample inserts it below the label layer to create a better user experience as the labels are clearly visible above the heat map. The data in this sample is sourced from the [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) and represents significant earthquakes that have occurred in the last 30 days.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Below is the complete running code sample of the above functionality.

<br/>

<iframe height='500' scrolling='no' title='단순 열 지도 계층' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>단순 열 지도 계층</a>을 참조하세요.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>열 지도 계층 사용자 지정

이전 예제는 반경 및 불투명도 옵션을 설정하여 열 지도를 사용자 지정했습니다. 열 지도 계층은 사용자 지정에 대한 다양한 옵션을 제공합니다.

* `radius`: Defines a pixel radius in which to render each data point. 반경은 정수 또는 식으로 설정할 수 있습니다. Using an expression, it's possible to scale the radius based on the zoom level, that appears to represent a consistent spatial area on the map (for example, 5-mile radius).
* `color`: Specifies how the heat map is colorized. A color gradient is often used for heat maps and can be achieved with an `interpolate` expression. Using a `step` expression for colorizing the heat map breaks up the density visually into ranges that more so resembles a contour or radar style map. 이러한 색상표는 색을 최소에서 최대 밀도 값까지 정의합니다. 열 지도에 대한 색 값은 `heatmap-density` 값에서 식으로 지정됩니다. The color at index 0 in an interpolation expression or the default color of a step expression, defines the color of the area where there's no data and can be used to define a background color. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다. 색 식의 예가 있습니다.

| Interpolation Color Expression | 계단식 색 식 | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

- `opacity`: Specifies how opaque or transparent the heat map layer is.
- `intensity`: Applies a multiplier to the weight of each data point to increase the overall intensity of the heatmap and helps to make the small differences in the weight of data points become easier to visualize.
- `weight`: By default, all data points have a weight of 1, thus all data points are weighted equally. 가중치 옵션은 승수 역할을 하며 숫자 또는 식으로 설정할 수 있습니다. 가중치가 예를 들어, 숫자 2로 설정된 경우 맵의 각 데이터 요소가 두 배가 되므로, 밀도도 두 배가 됩니다. 숫자가 가중치 옵션을 설정하면 밀도 옵션을 사용하는 것과 비슷한 방식으로 열 지도를 렌더링합니다. However, if an expression is used, the weight of each data point can be based on the properties of each data point. 예를 들어 지진 데이터의 경우 각 데이터 요소가 지진을 나타냅니다. 각 지진 데이터 요소의 중요한 메트릭은 크기 값입니다. 지진은 항상 발생하지만, 대부분 진도가 낮으며 느껴지지 않습니다. Using the magnitude value in an expression to assign the weight to each data point will allow more significant earthquakes to be better represented within the heat map.
- 최소/최대 확대/축소, 표시 및 필터 등의 기본 계층 옵션 이외에도 데이터 원본을 업데이트하려는 경우에는 `source` 옵션, 데이터 원본이 벡터 타일 원본인 경우에는 `source-layer` 옵션도 있습니다.

다양한 열 지도 계층 옵션을 시험해 볼 수 있는 도구가 있습니다.

<br/>

<iframe height='700' scrolling='no' title='열 지도 계층 옵션' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>열 지도 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistent zoomable heat map

By default, the radii of data points rendered in the heat map layer have a fixed pixel radius for all zoom levels. As the map is zoomed, the data aggregates together and the heat map layer looks different. A `zoom` expression can be used to scale the radius for each zoom level such that each data point covers the same physical area of the map. This will make the heat map layer look more static and consistent. Each zoom level of the map has twice as many pixels vertically and horizontally as the previous zoom level. Scaling the radius such that it doubles with each zoom level will create a heat map that looks consistent on all zoom levels. This can be accomplished by using the `zoom` with a base 2 `exponential interpolation` expression as shown in the sample below. Zoom the map to see how the heat map scales with the zoom level.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistent zoomable heat map" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
See the Pen <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>Consistent zoomable heat map</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> 데이터 원본에서 클러스터링을 사용하도록 설정하면 서로 인접한 요소는 클러스터된 요소로 그룹화됩니다. 각 클러스터의 요소 수를 열 지도에 대한 가중치 식으로 사용하면 렌더링해야 할 요소 수를 크게 줄일 수 있습니다. The point count of a cluster is stored in a `point_count` property of the point feature as shown below. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 클러스터링 반경이 단 몇 개의 픽셀로 이루어진 경우에는 렌더링에 시각적 차이가 거의 없습니다. 반경이 클수록 각 클러스터로 더 많은 요소를 그룹화하고 열 지도의 성능이 개선되지만 차이가 더 많이 눈에 띄게 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Create a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)