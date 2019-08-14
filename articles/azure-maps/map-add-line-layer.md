---
title: Azure Maps에 선 계층 추가 | Microsoft Docs
description: Azure Maps 웹 SDK에 선 계층을 추가 하는 방법입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f07e36d82c9044a212cda8173df9fe0a9544393a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977327"
---
# <a name="add-a-line-layer-to-the-map"></a>지도에 선 계층 추가

선 계층은 지도에서 및 `LineString` `MultiLineString` 기능을 경로 또는 경로로 렌더링 하는 데 사용할 수 있습니다. 선 계층은 `Polygon` 및 `MultiPolygon` 기능의 윤곽선을 렌더링 하는 데 사용할 수도 있습니다. 데이터 원본은 선 계층에 연결 되어 렌더링할 데이터를 제공 합니다. 

> [!TIP]
> 기본적으로 선 계층은 다각형의 좌표와 데이터 원본의 줄을 렌더링 합니다. LineString 기능만 렌더링 하도록 계층을 제한 하려면 계층의 `filter` 속성을 `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` 로 `['==', ['geometry-type'], 'LineString']` 설정 하 고 MultiLineString 기능을 포함 하려는 경우로 설정 합니다.

다음 코드에서는 줄을 만들고 데이터 소스에 추가 하 고 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 클래스를 사용 하 여 선 계층으로 렌더링 하는 방법을 보여 줍니다.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

다음은 위의 기능을 실행 하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='맵에 선 추가' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>맵에 선 추가</a>를 참조하세요.
</iframe>

선 계층은 [Linelayeroptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 를 사용 하 여 스타일을 지정 하 고 [데이터 기반 스타일 식을 사용할](data-driven-style-expressions-web-sdk.md)수 있습니다.

## <a name="add-symbols-along-a-line"></a>줄을 따라 기호 추가

이 샘플에서는 지도의 선을 따라 화살표 아이콘을 추가 하는 방법을 보여 줍니다. 기호 계층을 사용 하는 경우 "배치" 옵션을 "줄"로 설정 합니다. 이렇게 하면 선이 따라 기호가 렌더링 되 고 아이콘이 (0도 = 오른쪽)로 회전 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="선을 따라 화살표 표시" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 <a href='https://codepen.io/azuremaps/pen/drBJwX/'>따라 펜 표시 화살표</a> 를 참조 하세요.
</iframe>

> [!TIP]
> Azure Maps 웹 SDK는 기호 계층과 함께 사용할 수 있는 몇 가지 사용자 지정 가능 이미지 템플릿을 제공 합니다. 자세한 내용은 [이미지 템플릿 사용 방법](how-to-use-image-templates-web-sdk.md) 문서를 참조 하세요.

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>선에 선 그라데이션 추가

선에 단일 스트로크 색을 적용 하는 것 외에도 색 그라데이션을 사용 하 여 선을 채워서 한 줄 세그먼트에서 다음 줄 세그먼트로의 전환을 표시할 수 있습니다. 예를 들어 선 그라데이션을 사용 하 여 시간 및 거리에 따른 변경 내용 또는 연결 된 개체 라인에서 서로 다른 온도를 나타낼 수 있습니다. 줄에이 기능을 적용 하려면 데이터 원본에 `lineMetrics` 옵션을 true로 설정 해야 합니다. 그런 다음 색 그라데이션 식을 줄의 `strokeColor` 옵션에 전달할 수 있습니다. 스트로크 그라데이션 식은 계산 된 선 메트릭을 식 `['line-progress']` 에 노출 하는 데이터 식을 참조 해야 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="스트로크 그라데이션을 사용 하는 선" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>획 그라데이션을 사용 하</a> 는 펜 선 Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>()를 참조 하세요.
</iframe>

## <a name="customize-a-line-layer"></a>선 계층 사용자 지정

선 계층 여러 스타일 지정 옵션입니다. 시도해볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='선 계층 옵션' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>선 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [팝업 추가](map-add-popup.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [이미지 템플릿을 사용 하는 방법](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)