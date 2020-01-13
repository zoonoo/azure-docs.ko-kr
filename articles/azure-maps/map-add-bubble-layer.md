---
title: 지도에 거품형 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 지도에 거품형 계층을 추가 하는 방법에 대해 설명 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0088cced84da08828d02d3a0f83846babf286b71
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911280"
---
# <a name="add-a-bubble-layer-to-a-map"></a>맵에 거품형 계층 추가

이 문서에서는 데이터 원본의 점 데이터를 지도에서 거품형 계층으로 렌더링 하는 방법을 보여 줍니다. 거품형 계층은 고정된 픽셀 반경을 사용하여 요소를 맵에 원으로 렌더링합니다. 

> [!TIP]
> 기본적으로 거품형 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. Point geometry 기능만 렌더링 하도록 계층을 제한 하려면 계층의 `filter` 속성을 `['==', ['geometry-type'], 'Point']`로 설정 하거나 MultiPoint 기능을 포함 하려는 경우 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` 합니다.

## <a name="add-a-bubble-layer"></a>거품형 계층 추가

다음 코드는 데이터 소스에 점의 배열을 로드 하 고이를 [거품형 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)에 연결 합니다. 거품형 계층에는 각 거품의 반지름을 5 픽셀, 흰색 채우기 색, 파란색 스트로크 색 및 6 픽셀의 스트로크 너비로 렌더링 하는 옵션이 제공 됩니다. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

다음은 위의 기능을 실행 하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>를 참조하세요.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>거품형 계층을 사용하여 레이블 표시

다음 코드에서는 거품형 계층을 사용 하 여 지도에서 점을 렌더링 하 고 기호 계층을 사용 하 여 레이블을 렌더링 하는 방법을 보여 줍니다. 기호 계층의 아이콘을 숨기려면 아이콘 옵션의 `image` 속성을 `'none'`로 설정 합니다.

<br/>

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>를 참조하세요.
</iframe>

## <a name="customize-a-bubble-layer"></a>거품형 계층 사용자 지정

거품형 계층에는 몇 가지 스타일 지정 옵션만 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='거품형 계층 옵션' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>거품형 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)