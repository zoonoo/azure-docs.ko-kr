---
title: 맵에 선 레이어 추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 선 레이어를 추가하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c473be25907eb3a761fbccd598bb9b732e5be5b9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802351"
---
# <a name="add-a-line-layer-to-the-map"></a>맵에 선 레이어 추가

선 레이어를 사용하여 `LineString` 렌더링하고 `MultiLineString` 피처를 맵의 경로 또는 경로로 사용할 수 있습니다. 선 레이어를 사용하여 윤곽선 및 `Polygon` `MultiPolygon` 피처를 렌더링할 수도 있습니다. 데이터 원본이 선 레이어에 연결되어 렌더링할 데이터를 제공합니다. 

> [!TIP]
> 기본적으로 선 레이어는 데이터 원본의 선뿐만 아니라 다각형의 좌표를 렌더링합니다. LineString 피처만 렌더링하도록 레이어를 제한하려면 `filter` 레이어의 속성을 `['==', ['geometry-type'], 'LineString']` `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` 설정하거나 MultiLineString 피처도 포함하려는 경우

다음 코드는 줄을 만드는 방법을 보여 주며, 이 코드는 줄을 만드는 방법을 보여 주며, 데이터 원본에 선을 추가한 다음 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 클래스를 사용하여 선 레이어로 렌더링합니다.

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

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='맵에 선 추가' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>맵에 선 추가</a>를 참조하세요.
</iframe>

선 레이어 [옵션을](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 사용하여 선 레이어의 스타일을 정할 수 있으며 [데이터 기반 스타일 표현식을 사용할](data-driven-style-expressions-web-sdk.md)수 있습니다.

## <a name="add-symbols-along-a-line"></a>선을 따라 기호 추가

이 샘플에서는 맵의 선을 따라 화살표 아이콘을 추가하는 방법을 보여 주습니다. 심볼 레이어를 사용하는 경우 "배치" 옵션을 "선"으로 설정합니다. 이 옵션은 선을 따라 기호를 렌더링하고 아이콘을 회전합니다(0도 = 오른쪽).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="선을 따라 화살표 표시" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 선을 따라 펜 <a href='https://codepen.io/azuremaps/pen/drBJwX/'>표시 화살표를</a> 참조하십시오.
</iframe>

> [!TIP]
> Azure Maps 웹 SDK는 심볼 레이어와 함께 사용할 수 있는 몇 가지 사용자 지정 가능한 이미지 템플릿을 제공합니다. 자세한 내용은 이미지 템플릿 사용 방법 문서를 [참조하세요.](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>선에 스트로크 그라데이션 추가

선에 단일 스트로크 색상을 적용할 수 있습니다. 한 선 세그먼트에서 다음 선 세그먼트로의 전환을 표시하기 위해 색상 그라데이션으로 선을 채울 수도 있습니다. 예를 들어 선 그라데이션을 사용하여 시간 및 거리에 대한 변경 사항 또는 연결된 객체 선에서 다른 온도를 나타낼 수 있습니다. 이 피쳐를 선에 적용하려면 데이터 원본에 `lineMetrics` true로 설정된 옵션이 있어야 하며 색상 그라데이션 `strokeColor` 표현식을 선 옵션에 전달할 수 있습니다. 스트로크 그라데이션 표현식은 `['line-progress']` 계산된 줄 메트릭을 식에 노출시키는 데이터 식을 참조해야 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="스트로크 그라데이션이 있는 선" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 맵 ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>스트로크 그라데이션이 있는</a> 펜 선을 참조하십시오.
</iframe>

## <a name="customize-a-line-layer"></a>선 계층 사용자 지정

선 레이어에는 여러 가지 스타일 지정 옵션이 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='선 계층 옵션' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>선 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [라인 레이어](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [라인 레이어 옵션](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

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
> [다각형 계층 추가](map-add-shape.md)
