---
title: 지도에 다각형 레이어 추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK의 맵에서 다각형 및 다중 다각형을 렌더링하는 방법을 배웁니다.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535055"
---
# <a name="add-a-polygon-layer-to-the-map"></a>맵에 다각형 레이어 추가

이 문서에서는 다각형 레이어를 `MultiPolygon` 사용하여 맵에서 형상 및 형상 영역을 `Polygon` 렌더링하는 방법을 보여 주어집니다. Azure Maps 웹 SDK는 [확장된 GeoJSON 스키마에](extend-geojson.md#circle)정의된 대로 원 형상 생성도 지원합니다. 이러한 원은 맵에서 렌더링할 때 다각형으로 변환됩니다. 아틀라스로 래핑하면 모든 피쳐 형상을 쉽게 업데이트할 수 [있습니다. 셰이프](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스입니다.

## <a name="use-a-polygon-layer"></a>다각형 레이어 사용 

다각형 레이어가 데이터 원본에 연결되고 맵에 로드되면 해당 `Polygon` 영역과 `MultiPolygon` 피처가 렌더링됩니다. 다각형을 만들려면 데이터 원본에 추가하고 [다각형 레이어](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 클래스를 사용하여 다각형 레이어로 렌더링합니다.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

다음은 위의 코드의 전체 및 실행 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='맵에 다각형 추가 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>맵에 다각형 추가</a>를 참조하세요.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>다각형 및 선 레이어 를 함께 사용

선 레이어는 다각형의 윤곽을 렌더링하는 데 사용됩니다. 다음 코드 샘플에서는 이전 예제와 같은 다각형을 렌더링하지만 이제 선 레이어를 추가합니다. 이 선 레이어는 데이터 원본에 연결된 두 번째 레이어입니다.  

<iframe height='500' scrolling='no' title='다각형을 추가하기 위한 다각형 및 선 계층' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>다각형을 추가하기 위한 다각형 및 선 계층</a>을 참조하세요.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>다각형을 패턴으로 채웁니다.

다각형을 색상으로 채우는 것 외에도 이미지 패턴을 사용하여 다각형을 채울 수 있습니다. 이미지 패턴을 맵 이미지 스프라이트 리소스에 로드한 다음 `fillPattern` 다각형 레이어의 속성으로 이 이미지를 참조합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 채우기 패턴" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>다각형 채우기 패턴을</a> 참조하십시오.
</iframe>


> [!TIP]
> Azure Maps 웹 SDK는 채우기 패턴으로 사용할 수 있는 몇 가지 사용자 지정 가능한 이미지 템플릿을 제공합니다. 자세한 내용은 이미지 템플릿 사용 방법 문서를 [참조하세요.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>다각형 계층 사용자 지정

다각형 계층에는 몇 가지 스타일 지정 옵션만 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>를 참조하세요.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>맵에 원 추가

Azure Maps는 [여기에](extend-geojson.md#circle)설명된 대로 원에 대한 정의를 제공하는 확장 버전의 GeoJSON 스키마를 사용합니다. `Point` 피쳐를 작성하여 맵에 원이 렌더링됩니다. 이 `Point` `subType` 속성에는 값이 있는 `"Circle"` 속성과 미터의 반지름을 나타내는 숫자가 있는 `radius` 속성이 있습니다. 

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

Azure Maps 웹 SDK는 `Point` 이러한 `Polygon` 기능을 기능으로 변환합니다. 그런 다음 다음 코드 샘플과 같이 다각형 및 선 레이어를 사용하여 맵에서 이러한 피쳐가 렌더링됩니다.

<br/>

<iframe height='500' scrolling='no' title='맵에 원 추가' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>맵에 원 추가</a>를 참조하세요.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>지오메트리를 쉽게 업데이트할 수 있습니다.

클래스는 [지오메트리](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) 또는 [피쳐를](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 래핑하고 이러한 피쳐를 쉽게 업데이트하고 유지 관리할 수 있도록 합니다. `Shape` 셰이프 변수를 인스턴스화하려면 형상 또는 속성 집합을 셰이프 생성자에게 전달합니다.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

다음 코드 샘플에서는 Circle GeoJSON 개체를 셰이프 클래스로 래핑하는 방법을 보여 주습니다. 반지름 값이 셰이프에서 변경되면 원이 맵에서 자동으로 렌더링됩니다.

<br/>

<iframe height='500' scrolling='no' title='도형 속성 업데이트' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>도형 속성 업데이트</a>를 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [다각형 레이어](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [다각형레이어옵션](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [팝업 추가](map-add-popup.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [이미지 템플릿을 사용하는 방법](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

추가 리소스:

> [!div class="nextstepaction"]
> [Azure 지도 GeoJSON 사양 확장](extend-geojson.md#circle)