---
title: Azure Maps에 열 지도 계층 추가 | Microsoft Docs
description: Javascript 맵에 열 지도 계층을 추가하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ec1343f85216171adac22f873f9be2e72bb4c282
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893320"
---
# <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

열 지도(또는 요소 밀도 맵)는 여러 색을 사용하여 데이터 밀도를 나타내기 위해 사용하는 일종의 데이터 시각화입니다. 맵에 데이터 “핫 스폿”을 표시하기 위해 사용되는 경우가 많으며 큰 요소 데이터 세트를 렌더링하는 좋은 방법입니다.  예를 들어 기호로 맵 보기 내에 있는 수만 개의 요소를 렌더링하면 대부분의 맵 영역을 가리게 되고, 그결과 많은 기호가 다른 기호에 의해 가려져 데이터를 파악하기 어려워집니다. 하지만, 이 동일한 데이터 세트를 열 지도로 시각화하면 가장 밀도가 높은 요소 데이터가 어디인지, 그리고 다른 영역에 대한 상대 밀도를 쉽게 파악할 수 있습니다. 열 지도를 사용하는 많은 시나리오가 있으며, 다음은 몇 가지 예입니다.

* 온도 데이터는 두 데이터 요소 간 온도에 대한 근사치를 제공하므로 일반적으로 열 지도로 렌더링됩니다.
* 노이즈 센서에 대한 데이터를 열 지도로 렌더링하면 센서가 있는 곳의 노이즈 무결성을 보여줄 뿐만 아니라 장거리 열 손실에 대한 분석 정보를 제공합니다. 한 사이트의 노이즈 수준은 높지 않을 수 있지만, 여러 센서의 노이즈 커버리지 영역이 겹치는 경우 이 겹치는 영역에 더 높은 노이즈 수준이 발생하여 열 지도에 표시될 수 있습니다.
* 속도를 포함한 GPS 추적을 각 데이터 요소의 강도가 속도에 기반하는 가중치 높이 맵으로 시각화하면 차량이 가속하는 곳을 빠르게 파악할 수 있습니다.

> [!TIP]
> 기본적으로 거품형 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 계층을 요소 도형만 렌더링하도록 제한하려면 계층의 `filter` 속성을 `['==', '$type', 'Point']`로 설정합니다.

## <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

요소의 데이터 원본을 열 지도로 렌더링하려면 데이터 원본을 HeatMapLayer 클래스 인스턴스로 전달하여 맵에 추가하면 됩니다(그림 참조).

<br/>

<iframe height='500' scrolling='no' title='단순 열 지도 계층' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>단순 열 지도 계층</a>을 참조하세요.
</iframe>

이 샘플에서는 각 열 요소의 반경은 모든 확대/축소 수준에서 10픽셀입니다. 맵에 열 지도 계층 추가 시 이 샘플에서는 레이블 계층 아래에 넣습니다. 그러면 레이블이 열 지도 위에 선명하게 표시되므로 향상된 사용자 경험이 제공됩니다. 이 샘플의 데이터는 [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/)(USGS 지진 위험 프로그램)에서 제공되며 지난 30일 간 발생한 의미 있는 지진을 나타내는 요소로 구성되어 있습니다.

## <a name="customizing-the-heat-map-layer"></a>열 지도 계층 사용자 지정

이전 예제는 반경 및 불투명도 옵션을 설정하여 열 지도를 사용자 지정했습니다. 열 지도 계층은 사용자 지정에 대한 다양한 옵션을 제공합니다.

* `radius`: 각 데이터 요소를 렌더링할 픽셀 반경을 정의합니다. 반경은 정수 또는 식으로 설정할 수 있습니다. 확대/축소 수준에 기반하여 반경을 설정하는 식으로 반경을 설정하면 맵에서 반경이 일관된 공간 영역을 나타내는 열 지도가 표현될 수 있습니다.
* `color`: 열 지도에 색이 지정되는 방식을 지정합니다. 그라데이션 색상표는 열 지도에 자주 사용되지만, 열 지도를 더 외곽선 데이터처럼 보이게 하고 싶다면 계단식 색상표도 유용합니다. 이러한 색상표는 색을 최소에서 최대 밀도 값까지 정의합니다. 열 지도에 대한 색 값은 `heatmap-density` 값에서 식으로 지정됩니다. 그라데이션 식에서 인덱스 0의 색 또는 단계 색의 기본 색은 데이터 또는 배경색이 없는 영역의 색을 정의합니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다. 색 식의 예가 있습니다.

| 그라데이션 색 식 | 계단식 색 식 | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`: 열 지도 계층의 불투명도 또는 투명도를 지정합니다.
* `intensity`: 각 데이터 요소의 가중치에 승수를 적용하여 열 지도의 전체 강도를 높입니다. 데이터 요소의 가중치에서 작은 차이를 쉽게 시각화하는 데 도움이 됩니다.
* `weight`: 기본적으로 모든 데이터 요소의 가중치는 1이므로, 모든 데이터 요소의 가중치는 동일합니다. 가중치 옵션은 승수 역할을 하며 숫자 또는 식으로 설정할 수 있습니다. 숫자가 예를 들어, 숫자 2로 설정된 경우 맵의 각 데이터 요소가 두 배가 되므로, 밀도도 두 배가 됩니다. 숫자가 가중치 옵션을 설정하면 밀도 옵션을 사용하는 것과 비슷한 방식으로 열 지도를 렌더링합니다. 그러나 식을 사용하는 경우 각 데이터 요소의 가중치는 기준이 다를 수 있으며 요소의 속성에 있는 일부 메트릭에 기반할 수 있습니다. 지진 데이터를 예로 들면, 각 데이터 요소는 지진을 나타내며 각 지진의 중요한 메트릭은 진도입니다. 지진은 항상 발생하지만, 대부분 진도가 낮으며 느껴지지 않습니다. 식에서 진도 값을 사용하여 가중치 옵션을 할당하면 의미 있는 지진이 늘어날수록 열 지도 내에 더 잘 표현할 수 있습니다.
* 최소/최대 확대/축소, 표시 및 필터 등의 기본 계층 옵션 이외에도 데이터 원본을 업데이트하려는 경우에는 `source` 옵션, 데이터 원본이 벡터 타일 원본인 경우에는 `source-layer` 옵션도 있습니다.

다양한 열 지도 계층 옵션을 시험해 볼 수 있는 도구가 있습니다.

<br/>

<iframe height='700' scrolling='no' title='열 지도 계층 옵션' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>열 지도 계층 옵션</a>을 참조하세요.
</iframe>

> [!TIP]
> 데이터 원본에서 클러스터링을 사용하도록 설정하면 서로 인접한 요소는 클러스터된 요소로 그룹화됩니다. 각 클러스터의 요소 수를 열 지도에 대한 가중치 식으로 사용하면 렌더링해야 할 요소 수를 크게 줄일 수 있습니다. 한 클러스터의 요소 수는 요소 기능의 point_count 속성에 저장됩니다(아래 그림 참조). 
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
> [기호 계층 추가](./map-add-pin.md)

