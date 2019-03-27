---
title: Azure Maps로 도형 추가 | Microsoft Docs
description: Javascript 맵에 도형을 추가하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7598bbc879351752580247e46bc986ee84fa0d56
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497247"
---
# <a name="add-a-shape-to-a-map"></a>맵에 도형 추가

이 문서에서는 지도에 [도형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)을 추가하고 지도에 있는 기존 도형의 속성을 업데이트하는 방법을 보여 줍니다.

<a id="addALine"></a>

## <a name="add-a-line"></a>선 추가

<iframe height='500' scrolling='no' title='맵에 선 추가' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>맵에 선 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) 개체가 생성되고 데이터 원본에 추가되었습니다.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 선 개체를 렌더링합니다. 마지막 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요. 데이터 원본 및 선 계층이 생성되고 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 선이 표시되도록 합니다.

## <a name="customize-a-line-layer"></a>선 계층 사용자 지정

선 계층 여러 스타일 지정 옵션입니다. 시도해볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='선 계층 옵션' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>선 계층 옵션</a>을 참조하세요.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>원 추가

<iframe height='500' scrolling='no' title='맵에 원 추가' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>맵에 원 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. 원은 [지점](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)이며 “circle”로 설정된 `subType` 속성 및 `radius` 속성 값 미터가 있습니다. 원의 하위 형식을 사용한 지점 기능이 데이터 원본에 추가되면 맵 내의 순환 다각형으로 변환됩니다.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 마지막 코드 블록은 다각형 계층을 만들고 지도에 추가합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본 및 다각형 계층이 생성되고 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 원이 표시되도록 합니다.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>다각형 추가

지도에 다각형을 추가할 수 있는 방법에는 두 가지가 있습니다. 두 가지 모두 다음 예제에서 설명합니다.

### <a name="use-polygon-layer"></a>다각형 계층 사용 

<iframe height='500' scrolling='no' title='맵에 다각형 추가 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>맵에 다각형 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [다각형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)은 좌표의 배열에서 생성되어 데이터 원본에 추가됩니다. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 마지막 코드 블록은 다각형 계층을 만들고 지도에 추가합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본 및 다각형 계층이 생성되고 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 다각형이 표시되도록 합니다.

### <a name="use-polygon-and-line-layer"></a>다각형 및 선 계층 사용

<iframe height='500' scrolling='no' title='다각형을 추가하기 위한 다각형 및 선 계층' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>다각형을 추가하기 위한 다각형 및 선 계층</a>을 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [다각형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)은 좌표의 배열에서 생성되어 데이터 원본에 추가됩니다. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 선 배열입니다. [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요. 세 번째 코드 블록은 다각형 및 선 계층을 만듭니다.

마지막 코드 블록은 다각형 및 선 계층을 지도에 추가합니다. 데이터 원본 및 계층이 생성되고 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 다각형이 표시되도록 합니다.

## <a name="customize-a-polygon-layer"></a>다각형 계층 사용자 지정

다각형 계층에는 몇 가지 스타일 지정 옵션만 있습니다. 시도해볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>를 참조하세요.
</iframe>

## <a name="update-a-shape"></a>도형 업데이트

Shape 클래스는 [기하 도형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) 또는 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)을 래핑하여 쉽게 업데이트하고 유지 관리할 수 있도록 합니다.
`new Shape(data: Feature<data.Geometry, any>)`는 도형 개체를 생성하고 지정된 기능으로 초기화합니다.

<br/>

<iframe height='500' scrolling='no' title='도형 속성 업데이트' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>도형 속성 업데이트</a>를 참조하세요.
</iframe>

위에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

점은 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 클래스의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 중 하나입니다. 두 번째 코드 블록은 HTML 슬라이더 요소의 반경 값을 초기화한 다음, 점 개체를 생성하고 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스 개체에 래핑합니다.

세 번째 코드 블록은 HTML 범위 슬라이더 요소에서 값을 받고 shape 클래스 [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 메서드를 사용하여 반경 값을 변경하는 함수를 만듭니다.

네 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. 그런 다음, 점이 데이터 원본에 추가됩니다.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 세 번째 코드 블록은 다각형 계층을 만듭니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본, 클릭 이벤트 수신기 및 다각형 계층이 생성되고 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 점이 표시되도록 합니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [HTML 표식](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [열 지도 계층 추가](./map-add-heat-map-layer.md)
