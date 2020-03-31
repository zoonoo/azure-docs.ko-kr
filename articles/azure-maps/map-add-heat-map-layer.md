---
title: 지도에 히트 맵 레이어 추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 열지도 레이어를 맵에 추가하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4a853871ef5f66881235e5a6ffec0886b81f5a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208542"
---
# <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

지점 밀도 맵이라고도 하는 열 지도는 데이터 시각화의 한 형식입니다. 다양한 색상을 사용하여 데이터의 밀도를 나타내고 맵에 데이터 "핫 스폿"을 표시하는 데 사용됩니다. 히트 맵은 많은 수의 포인트가 있는 데이터 집합을 렌더링하는 좋은 방법입니다. 

수만 개의 포인트를 기호로 렌더링하면 맵 영역의 대부분을 덮을 수 있습니다. 이 경우 많은 기호가 겹칠 수 있습니다. 데이터를 더 잘 이해하기 어렵게 만듭니다. 그러나 히트 맵과 동일한 데이터 집합을 시각화하면 각 데이터 요소의 밀도와 상대 밀도를 쉽게 확인할 수 있습니다.

다음과 같은 다양한 시나리오에서 히트 맵을 사용할 수 있습니다.

- **온도 데이터**: 두 데이터 점 사이의 온도에 대한 근사치를 제공합니다.
- **노이즈 센서 데이터**: 센서가 있는 소음의 강도뿐만 아니라 멀리 떨어져 있는 소산에 대한 통찰력을 제공할 수도 있습니다. 한 사이트의 소음 수준이 높지 않을 수 있습니다. 여러 센서의 노이즈 커버리지 영역이 겹치는 경우 이 중첩 영역이 더 높은 소음 수준을 경험할 수 있습니다. 따라서 중첩된 영역은 히트 맵에 표시됩니다.
- **GPS 추적**: 각 데이터 포인트의 강도가 속도를 기반으로 하는 가중치 기반 높이 맵으로 속도를 포함합니다. 예를 들어 이 기능은 차량이 과속하는 위치를 확인할 수 있는 방법을 제공합니다.

> [!TIP]
> 기본적으로 히트 맵 레이어는 데이터 원본에서 모든 형상의 좌표를 렌더링합니다. 점 형상 피처만 렌더링하도록 레이어를 제한하려면 `filter` 레이어의 속성을 `['==', ['geometry-type'], 'Point']`로 설정합니다. MultiPoint 피처도 포함하려면 레이어의 `filter` 속성을 로 설정합니다. `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

점의 데이터 원본을 히트 맵으로 렌더링하려면 데이터 원본을 `HeatMapLayer` 클래스의 인스턴스로 전달하고 맵에 추가합니다.

다음 코드에서 각 히트 포인트의 반지름은 모든 확대/축소 수준에서 10픽셀입니다. 더 나은 사용자 환경을 보장하기 위해 히트 맵은 레이블 레이어 아래에 있습니다. 라벨은 선명하고 잘 보입니다. 이 샘플의 데이터는 [USGS 지진 위험 프로그램에서](https://earthquake.usgs.gov/)나온 것입니다. 그것은 지난 30 일 동안 발생 한 중요 한 지진에 대 한.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

다음은 이전 코드의 전체 실행 중인 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='단순 열 지도 계층' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>단순 열 지도 계층</a>을 참조하세요.
</iframe>

## <a name="customize-the-heat-map-layer"></a>히트 맵 레이어 사용자 지정

이전 예제는 반경 및 불투명도 옵션을 설정하여 열 지도를 사용자 지정했습니다. 히트 맵 레이어는 다음과 같은 사용자 지정을 위한 몇 가지 옵션을 제공합니다.

* `radius`: 각 데이터 포인트를 렌더링할 픽셀 반지름을 정의합니다. 반경을 고정 숫자또는 식으로 설정할 수 있습니다. 표현식을 사용하여 확대/축소 수준에 따라 반경의 배율을 조정하고 맵에서 일관된 공간 영역(예: 5마일 반지름)을 나타낼 수 있습니다.
* `color`: 히트 맵의 색상 지정입니다. 색상 그라데이션은 열 맵의 일반적인 특징입니다. `interpolate` 식을 사용하면 효과를 얻을 수 있습니다. 또한 히트 맵의 `step` 색상을 지정하고 밀도를 등고선 또는 레이더 스타일 맵과 유사한 범위로 시각적으로 나누는 표현식을 사용할 수도 있습니다. 이러한 색상표는 색을 최소에서 최대 밀도 값까지 정의합니다. 

  히트 맵의 색상 값을 `heatmap-density` 값의 표현식으로 지정합니다. 데이터가 없는 영역의 색상은 "보간" 식의 인덱스 0 또는 "단계별" 식의 기본 색상에 정의되어 있습니다. 이 값을 사용하여 배경색을 정의할 수 있습니다. 이 값은 투명또는 반투명 검정으로 설정된 경우가 많습니다. 
   
  다음은 색상 표현식의 예입니다.

  | 보간 색상 표현 | 스들어드 컬러 표현 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, '녹색',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, '옐로우',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, '빨강'<br/>\] |   

- `opacity`: 히트 맵 레이어가 얼마나 불투명하거나 투명한지 지정합니다.
- `intensity`: 히트맵의 전체 강도를 높이기 위해 각 데이터 포인트의 가중치에 승수를 적용합니다. 데이터 포인트의 가중치에 차이가 발생하여 시각화가 더 쉬워집니다.
- `weight`: 기본적으로 모든 데이터 포인트의 가중치는 1이며 가중치가 동일하게 가중됩니다. 가중치 옵션은 승수역할을 하며 숫자 또는 식으로 설정할 수 있습니다. 숫자가 가중치로 설정된 경우 각 데이터 포인트를 맵에 두 번 배치하는 것과 같은 값입니다. 예를 들어 가중치가 2이면 밀도가 두 배가 됩니다. 숫자가 가중치 옵션을 설정하면 밀도 옵션을 사용하는 것과 비슷한 방식으로 열 지도를 렌더링합니다. 

  그러나 식을 사용하는 경우 각 데이터 포인트의 가중치는 각 데이터 포인트의 속성을 기반으로 할 수 있습니다. 예를 들어 각 데이터 포인트가 지진을 나타낸다고 가정합니다. 크기 값은 각 지진 데이터 포인트에 대한 중요한 메트릭입니다. 지진은 항상 발생하지만, 대부분은 낮은 규모를 가지고 있으며, 발견되지 않습니다. 식의 크기 값을 사용하여 각 데이터 점에 가중치를 할당합니다. 크기 값을 사용하여 가중치를 할당하면 히트 맵 내에서 지진의 중요성을 더 잘 표현할 수 있습니다.
- `source`및 `source-layer`: 데이터 원본을 업데이트할 수 있습니다.

다양한 히트 맵 레이어 옵션을 테스트하는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='열 지도 계층 옵션' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>열 지도 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>일관된 확대/열 지도

기본적으로 히트 맵 레이어에서 렌더링된 데이터 점의 반지름은 모든 확대/축소 수준에 대해 고정된 픽셀 반지름을 가며 있습니다. 맵을 확대/축소하면 데이터가 함께 집계되고 히트 맵 레이어가 다르게 보입니다. 

식을 `zoom` 사용하여 각 데이터 포인트가 맵의 동일한 물리적 영역을 덮도록 각 확대/축소 수준에 대한 반지름의 크기를 조정합니다. 이 식은 히트 맵 레이어를 보다 정적이고 일관되게 보이게 합니다. 맵의 각 확대/축소 수준은 이전 확대/축소 수준보다 수직 및 수평으로 두 배 더 많은 픽셀을 가짐을 가짐입니다. 

각 확대/축소 수준으로 두 배가 되도록 반경의 배율을 조정하면 모든 확대/축소 수준에서 일관되게 보이는 히트 맵이 생성됩니다. 이 배율 조정을 적용하려면 `exponential interpolation` 다음 샘플과 같이 기본 2 식과 함께 사용하십시오. `zoom` 지도를 확대/축소하여 히트 맵이 확대/축소 수준으로 배율 조정되는 방식을 확인합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="일관된 확대/열 지도" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>일관된 확대 식 열 지도를</a> 참조하십시오.
</iframe>

> [!TIP]
> 데이터 원본에서 클러스터링을 사용하도록 설정하면 서로 가까운 점이 클러스터된 점으로 그룹화됩니다. 각 클러스터의 포인트 수를 히트 맵의 가중치 표현식으로 사용할 수 있습니다. 이렇게 하면 렌더링할 점 의 수가 크게 줄어들 수 있습니다. 클러스터의 포인트 수는 점 피쳐의 `point_count` 속성에 저장됩니다. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 클러스터링 반경이 몇 픽셀에 불과한 경우 렌더링에 약간의 시각적 차이가 있습니다. 반지름이 클수록 각 클러스터에 더 많은 점이 그룹화되고 히트맵의 성능이 향상됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
