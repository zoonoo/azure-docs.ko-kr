---
title: 맵에 다각형 돌출 레이어 추가 | 마이크로소프트 Azure 지도
description: Microsoft Azure 지도 웹 SDK에 다각형 돌출 레이어를 추가하는 방법.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334554"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>맵에 다각형 돌출 레이어 추가

이 문서에서는 다각형 돌출 레이어를 사용하여 형상의 `Polygon` `MultiPolygon` 영역을 렌더링하고 형상형상을 돌출 된 모양으로 렌더링하는 방법을 보여 주어집니다. Azure Maps 웹 SDK는 [확장된 GeoJSON 스키마에](extend-geojson.md#circle)정의된 대로 원 형상 렌더링을 지원합니다. 이러한 원은 맵에서 렌더링할 때 다각형으로 변환할 수 있습니다. 모든 피쳐 형상은 아틀라스로 래핑할 때 쉽게 업데이트할 수 [있습니다. 셰이프](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스입니다.

## <a name="use-a-polygon-extrusion-layer"></a>다각형 압출층 사용

[다각형 돌출 레이어를](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) 데이터 원본에 연결합니다. 그런 다음 맵에 로드했습니다. 다각형 돌출 레이어는 a `Polygon` 및 `MultiPolygon` 피처의 영역을 돌출 모양으로 렌더링합니다. 다각형 압출 층의 및 `height` `base` 특성은 돌출된 모양의 지면과 높이로부터의 기본 거리를 **미터로**정의합니다. 다음 코드에서는 다각형을 만들고, 데이터 원본에 추가하고, 다각형 돌출 레이어 클래스를 사용하여 렌더링하는 방법을 보여 주어집니다.

> [!Note]
> 다각형 돌출 레이어에 정의된 값은 `base` `height`의 값보다 적거나 같아야 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="압출 다각형" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의한 펜 <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>돌출 다각형을</a> 참조하십시오.</iframe>


## <a name="add-data-driven-polygons"></a>데이터 기반 다각형 추가

다각형 돌출 레이어를 사용하여 등변선 맵을 렌더링할 수 있습니다. 돌출층의 `height` 및 `fillColor` 특성을 `Polygon` 및 `MultiPolygon` 피쳐 형상에서 통계 변수의 측정으로 설정합니다. 다음 코드 샘플은 상태별 인구 밀도의 측정을 기반으로 미국의 압출 된 등가플지도를 보여줍니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="돌출된 코로플레스 지도" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 돌출 식 <a href='https://codepen.io/azuremaps/pen/eYYYNox'>초비장 맵을</a> 참조하십시오.
</iframe>

## <a name="add-a-circle-to-the-map"></a>맵에 원 추가

Azure Maps는 [여기에](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)설명된 대로 원에 대한 정의를 제공하는 GeoJSON 스키마의 확장 버전을 사용합니다. 돌출된 원은 `point` `subType` `Circle` 속성의 피쳐와 `Radius` **미터의**반지름을 나타내는 번호가 매겨진 속성이 있는 피쳐를 작성하여 맵에서 렌더링할 수 있습니다. 예를 들어:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps 웹 SDK는 `Point` 이러한 `Polygon` 기능을 후드 아래의 피처로 변환합니다. 이러한 `Point` 피쳐는 다음 코드 샘플과 같이 다각형 돌출 레이어를 사용하여 맵에서 렌더링할 수 있습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="무인 항공기 영공 다각형" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>드론 영공 다각형을</a> 참조하십시오.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>다각형 압출층 사용자 지정

다각형 돌출 층에는 여러 가지 스타일 옵션이 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='푸그브르지' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>코드펜에서</a>Azure 지도 ()별<a href='https://codepen.io/azuremaps'>@azuremaps</a>펜 <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ를</a> 참조하십시오.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [다각형 압출층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

추가 리소스:

> [!div class="nextstepaction"]
> [Azure 지도 GeoJSON 사양 확장](extend-geojson.md#circle)
