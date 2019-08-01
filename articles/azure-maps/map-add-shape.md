---
title: Azure Maps로 도형 추가 | Microsoft Docs
description: Javascript 맵에 도형을 추가하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0696eba4f3cca7beedc2efcda0182ab82b3d69d9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638701"
---
# <a name="add-a-shape-to-a-map"></a>맵에 도형 추가

이 문서에서는 선 및 다각형 계층을 사용 하 여 지도에 기 하 도형을 렌더링 하는 방법을 보여 줍니다. Azure Maps 웹 SDK는 [확장 GeoJSON 스키마](extend-geojson.md#circle)에 정의 된 원 기 하 도형 만들기도 지원 합니다. 모든 기능 기 하 도형은 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스로 래핑된 경우에도 쉽게 업데이트할 수 있습니다.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>맵에 선 추가

`LineString`및 `MultiLineString` 기능은 지도의 경로 및 윤곽선을 나타내는 데 사용 됩니다.

### <a name="add-a-line"></a>선 추가

<iframe height='500' scrolling='no' title='맵에 선 추가' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>맵에 선 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) 개체가 생성되고 데이터 원본에 추가되었습니다.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 선 개체를 렌더링합니다. 마지막 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요. 데이터 원본 및 선 계층이 생성 되 고 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 내에서 맵에 추가 되어 맵이 완전히 로드 된 후에도 줄이 표시 됩니다.

### <a name="add-symbols-along-a-line"></a>줄을 따라 기호 추가

이 샘플에서는 지도의 선을 따라 화살표 아이콘을 추가 하는 방법을 보여 줍니다. 기호 계층을 사용 하는 경우 "배치" 옵션을 "줄"로 설정 합니다. 이렇게 하면 선이 따라 기호가 렌더링 되 고 아이콘이 (0도 = 오른쪽)로 회전 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="선을 따라 화살표 표시" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 <a href='https://codepen.io/azuremaps/pen/drBJwX/'>따라 펜 표시 화살표</a> 를 참조 하세요.
</iframe>

### <a name="line-stroke-gradient"></a>선에 선 그라데이션 추가

선에 단일 스트로크 색을 적용 하는 것 외에도 색 그라데이션을 사용 하 여 선을 채워서 한 줄 세그먼트에서 다음 줄 세그먼트로의 전환을 표시할 수 있습니다. 예를 들어 선 그라데이션을 사용 하 여 시간 및 거리에 따른 변경 내용 또는 연결 된 개체 라인에서 서로 다른 온도를 나타낼 수 있습니다. 줄에이 기능을 적용 하려면 데이터 원본에 `lineMetrics` 옵션을 true로 설정 해야 합니다. 그런 다음 색 그라데이션 식을 줄의 `strokeColor` 옵션에 전달할 수 있습니다. 스트로크 그라데이션 식은 계산 된 선 메트릭을 식 `['line-progress']` 에 노출 하는 데이터 식을 참조 해야 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="스트로크 그라데이션을 사용 하는 선" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>획 그라데이션을 사용 하</a> 는 펜 선 Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>()를 참조 하세요.
</iframe>

### <a name="customize-a-line-layer"></a>선 계층 사용자 지정

선 계층 여러 스타일 지정 옵션입니다. 시도해볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='선 계층 옵션' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>선 계층 옵션</a>을 참조하세요.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>지도에 다각형 추가

`Polygon`및 `MultiPolygon` 기능은 지도의 영역을 나타내는 데 사용 되는 경우가 많습니다. 

### <a name="use-a-polygon-layer"></a>다각형 계층 사용 

다각형 계층은 다각형의 영역을 렌더링 합니다. 

<iframe height='500' scrolling='no' title='맵에 다각형 추가 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>맵에 다각형 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [다각형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)은 좌표의 배열에서 생성되어 데이터 원본에 추가됩니다. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 마지막 코드 블록은 다각형 계층을 만들고 지도에 추가합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본 및 다각형 계층이 생성 되 고 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 내에 추가 되어 맵이 완전히 로드 된 후 다각형이 표시 되도록 합니다.

### <a name="use-a-polygon-and-line-layer-together"></a>다각형 및 선 계층을 함께 사용

선 계층은 다각형의 윤곽선을 렌더링 하는 데 사용할 수 있습니다. 

<iframe height='500' scrolling='no' title='다각형을 추가하기 위한 다각형 및 선 계층' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>다각형을 추가하기 위한 다각형 및 선 계층</a>을 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [다각형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)은 좌표의 배열에서 생성되어 데이터 원본에 추가됩니다. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 선 배열입니다. [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요. 세 번째 코드 블록은 다각형 및 선 계층을 만듭니다.

마지막 코드 블록은 다각형 및 선 계층을 지도에 추가합니다. 데이터 원본 및 레이어가 생성 되어 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 내에 추가 되어 맵이 완전히 로드 된 후 다각형이 표시 되도록 합니다.

> [!TIP]
> 기본적으로 선 계층은 다각형의 좌표와 데이터 원본의 줄을 렌더링 합니다. LineString 기능만 렌더링 하도록 계층을 제한 하려면 계층의 `filter` 속성을 `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` 로 `['==', ['geometry-type'], 'LineString']` 설정 하 고 MultiLineString 기능을 포함 하려는 경우로 설정 합니다.

### <a name="fill-a-polygon-with-a-pattern"></a>패턴을 사용 하 여 다각형 채우기

다각형을 색으로 채우는 것 외에도 이미지 패턴을 사용할 수 있습니다. 지도 이미지 스프라이트 리소스에 이미지 패턴을 로드 한 다음 다각형 계층의 `fillPattern` 속성을 사용 하 여이 이미지를 참조 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 채우기 패턴" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon 채우기 패턴</a> 을 참조 하세요.
</iframe>

### <a name="customize-a-polygon-layer"></a>다각형 계층 사용자 지정

다각형 계층에는 몇 가지 스타일 지정 옵션만 있습니다. 시도해볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>를 참조하세요.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>지도에 원 추가

Azure Maps에는 [여기](extend-geojson.md#circle)에 나와 있는 것 처럼 원에 대 한 정의를 제공 하는 GeoJSON 스키마의 확장 된 버전이 사용 됩니다. `Point` `subType` 값 이인`"Circle"` 속성이 있는 기능을 만들고 미터 단위를 나타내는 숫자가 있는 속성을사용하여지도에서원을렌더링할수있습니다.`radius` 예를 들어:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps 웹 SDK는 이러한 `Point` 기능을 내부적으로 기능으로 `Polygon` 변환 하 고 여기에 표시 된 것 처럼 다각형 및 선 계층을 사용 하 여 지도에서 렌더링할 수 있습니다.

<iframe height='500' scrolling='no' title='맵에 원 추가' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>맵에 원 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. 원은 [점의](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) `subType` [기능이](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 며 속성은로 `"Circle"` 설정 되 고 속성 값은 `radius` 미터 단위로 설정 됩니다. 가 인 `subType` `"Circle"` Point 기능이 데이터 원본에 추가 되 면 지도 내의 원형 다각형으로 변환 됩니다.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 마지막 코드 블록은 다각형 계층을 만들고 지도에 추가합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본 및 다각형 계층이 생성 되 고 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 내에 추가 되어 맵이 완전히 로드 된 후 원이 표시 되도록 합니다.

## <a name="make-a-geometry-easy-to-update"></a>업데이트 하기 쉬운 기 하 도형 만들기

클래스 `Shape` 는 [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) 또는 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 을 래핑하고 쉽게 업데이트 하 고 유지 관리할 수 있도록 합니다.
`new Shape(data: Feature<data.Geometry, any>)`는 도형 개체를 생성하고 지정된 기능으로 초기화합니다.

<br/>

<iframe height='500' scrolling='no' title='도형 속성 업데이트' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>도형 속성 업데이트</a>를 참조하세요.
</iframe>

위에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

점은 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 클래스의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 중 하나입니다. 두 번째 코드 블록은 HTML 슬라이더 요소의 반경 값을 초기화한 다음, 점 개체를 생성하고 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스 개체에 래핑합니다.

세 번째 코드 블록은 HTML 범위 슬라이더 요소에서 값을 받고 shape 클래스 [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 메서드를 사용하여 반경 값을 변경하는 함수를 만듭니다.

네 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. 그런 다음, 점이 데이터 원본에 추가됩니다.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 세 번째 코드 블록은 다각형 계층을 만듭니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본, 클릭 이벤트 처리기 및 다각형 계층이 생성 되 고 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 내의 맵에 추가 되어 맵이 완전히 로드 된 후 해당 지점이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
