---
title: 맵에 심볼 레이어 추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 심볼 레이어를 사용하여 기호를 사용자 지정하고 Microsoft Azure Maps Web SDK를 사용하여 맵에 기호를 추가하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209701"
---
# <a name="add-a-symbol-layer-to-a-map"></a>맵에 기호 계층 추가

심볼을 데이터 원본에 연결하고 이를 사용하여 지정된 지점에서 아이콘이나 텍스트를 렌더링합니다. 

기호 계층은 WebGL을 사용하여 렌더링됩니다. 기호 레이어를 사용하여 맵에서 큰 점 컬렉션을 렌더링합니다. HTML 마커에 비해 심볼 레이어는 맵에서 많은 수의 포인트 데이터를 렌더링하여 성능이 향상됩니다. 그러나 기호 계층은 스타일 지정을 위해 기존 CSS 및 HTML 요소를 지원하지 않습니다.  

> [!TIP]
> 기본적으로 기호 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 점 형상 피쳐만 렌더링하도록 레이어를 제한하려면 `filter` 레이어의 속성을 `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` 설정하거나 원하는 경우 MultiPoint 피처도 포함할 수 있습니다.

맵 이미지 스프라이트 관리자는 심볼 레이어에서 사용하는 사용자 지정 이미지를 로드합니다. 다음 이미지 형식을 지원합니다.

- JPEG
- PNG
- SVG
- BMP
- GIF(애니메이션 없음)

## <a name="add-a-symbol-layer"></a>기호 계층 추가

맵에 심볼 레이어를 추가하려면 몇 단계를 수행해야 합니다. 먼저 데이터 원본을 만들고 맵에 추가합니다. 심볼 레이어를 작성합니다. 그런 다음 데이터 원본을 기호 계층으로 전달하여 데이터 원본에서 데이터를 검색합니다. 마지막으로 데이터 원본에 데이터를 추가하여 렌더링할 것이 있습니다. 

아래 코드는 로드한 후 맵에 추가해야 하는 내용을 보여 줍니다. 이 샘플은 기호 레이어를 사용하여 맵에서 단일 점을 렌더링합니다. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

맵에 추가할 수 있는 네 가지 유형의 포인트 데이터가 있습니다.

- GeoJSON 포인트 지오메트리 - 이 오브젝트에는 점의 좌표만 포함되어 있고 다른 것은 없습니다. 도우미 `atlas.data.Point` 클래스를 사용하여 이러한 개체를 쉽게 만들 수 있습니다.
- GeoJSON 멀티포인트 지오메트리 - 이 오브젝트에는 여러 점의 좌표가 포함되어 있습니다. 도우미 `atlas.data.MultiPoint` 클래스를 사용하여 이러한 개체를 쉽게 만들 수 있습니다.
- GeoJSON 기능 - 이 개체는 모든 GeoJSON 형상과 지오메트리와 관련된 메타데이터를 포함하는 속성 집합으로 구성됩니다. 도우미 `atlas.data.Feature` 클래스를 사용하여 이러한 개체를 쉽게 만들 수 있습니다.
- `atlas.Shape`클래스는 GeoJSON 기능과 유사합니다. 둘 다 GeoJSON 형상과 형상에 연결된 메타데이터를 포함하는 속성 집합으로 구성됩니다. GeoJSON 오브젝트가 데이터 원본에 추가되면 레이어에서 쉽게 렌더링할 수 있습니다. 그러나 해당 GeoJSON 개체의 좌표 속성이 업데이트되는 경우 데이터 원본과 맵은 변경되지 않습니다. JSON 개체에 업데이트를 트리거하는 메커니즘이 없기 때문입니다. 셰이프 클래스는 포함된 데이터를 업데이트하는 함수를 제공합니다. 변경이 완료되면 데이터 원본과 맵이 자동으로 알림 및 업데이트됩니다. 

다음 코드 샘플에서는 GeoJSON Point 지오메트리를 `atlas.Shape` 만들고 이를 클래스로 전달하여 쉽게 업데이트할 수 있습니다. 맵의 중심은 처음에 기호를 렌더링하는 데 사용됩니다. 클릭 이벤트가 맵에 추가되어 마우스가 발사될 때 셰이프 `setCoordinates` 함수와 함께 마우스좌표가 사용됩니다. 마우스 좌표는 클릭 이벤트 시점에 기록됩니다. 그런 다음 `setCoordinates` 맵에서 기호의 위치를 업데이트합니다.

<br/>

<iframe height='500' scrolling='no' title='핀 고정 위치 전환' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>핀 고정 위치 전환</a>을 참조하세요.
</iframe>

> [!TIP]
> 기본적으로 심볼 레이어는 겹치는 기호를 숨겨 기호 렌더링을 최적화합니다. 확대하면 숨겨진 기호가 표시됩니다. 이 기능을 사용하지 않도록 설정하고 모든 기호를 `allowOverlap` 항상 렌더링하려면 `iconOptions` `true`옵션의 속성을 로 설정합니다.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>기호 계층에 사용자 지정 아이콘 추가

기호 계층은 WebGL을 사용하여 렌더링됩니다. 아이콘 이미지와 같은 이러한 모든 리소스는 WebGL 컨텍스트에 로드해야 하기 때문입니다. 이 샘플에서는 맵 리소스에 사용자 지정 아이콘을 추가하는 방법을 보여 주며 있습니다. 그런 다음 이 아이콘을 사용하여 맵에 사용자 지정 기호가 있는 포인트 데이터를 렌더링합니다. 기호 계층의 `textField` 속성에는 식을 지정해야 합니다. 이 경우 온도 속성을 렌더링하려고 합니다. 온도는 숫자이므로 문자열로 변환해야 합니다. 또한 우리는 그것에 "° F"를 추가 할 수 있습니다. 식을 사용하여 이 연결 작업을 수행할 수 있습니다. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='사용자 지정 기호 이미지 아이콘' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>사용자 지정 기호 이미지 아이콘</a>을 참조하세요.
</iframe>

> [!TIP]
> Azure Maps 웹 SDK는 심볼 레이어와 함께 사용할 수 있는 몇 가지 사용자 지정 가능한 이미지 템플릿을 제공합니다. 자세한 내용은 이미지 템플릿 사용 방법 문서를 [참조하세요.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>기호 계층 사용자 지정 

기호 계층에는 사용할 수 있는 많은 스타일 지정 옵션이 있습니다. 이러한 다양한 스타일 지정 테스트하는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='기호 계층 옵션' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/PxVXje/'>기호 계층 옵션</a>을 참조하세요.
</iframe>

> [!TIP]
> 기호 레이어가 있는 텍스트만 렌더링하려는 경우 아이콘 옵션의 `image` 속성을 `'none'`로 설정하여 아이콘을 숨길 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [텍스트 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

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

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [거품 레이어 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](map-add-bubble-layer.md)
