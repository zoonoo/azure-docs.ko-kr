---
title: Azure Maps에 기호 계층 추가 | Microsoft Docs
description: Javascript 맵에 기호를 추가하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3bce4922a33648f5d7c0d211dba126f35603239b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849294"
---
# <a name="add-a-symbol-layer-to-a-map"></a>맵에 기호 계층 추가

이 문서에서는 맵에 기호 계층으로 데이터 원본에서 요소 데이터를 렌더링할 수 있는 방법을 보여줍니다. 기호 레이어는 WebGL을 사용 하 여 렌더링 되 고 HTML 표식 보다 훨씬 큰 수의 요소 집합을 지원 하지만 스타일 지정을 위해 기존 CSS 및 HTML 요소를 지원 하지 않습니다.  

> [!TIP]
> 기본적으로 기호 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 점 기 하 도형 기능만 렌더링 하도록 계층을 제한 하려면 계층의 `filter` 속성을로 `['==', ['geometry-type'], 'Point']` 설정 하 고, `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` 다중 포인트 기능을 포함 하려는 경우로 설정 합니다.

## <a name="add-a-symbol-layer"></a>기호 계층 추가

<iframe height='500' scrolling='no' title='핀 고정 위치 전환' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>핀 고정 위치 전환</a>을 참조하세요.
</iframe>

위에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [점](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 기 하 도형을 포함 하는 GeoJSON [기능은](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature) [셰이프](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스에 의해 래핑되어 더 쉽게 업데이트 하 고 데이터 원본에 추가할 수 있습니다.

세 번째 코드 블록은 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)를 만들고 마우스 클릭 시 shape 클래스 [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 메서드를 사용하여 점의 좌표를 업데이트합니다.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다.  데이터 원본, 클릭 이벤트 수신기 및 기호 계층이 만들어지고 `ready` [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내의 맵에 추가 되어 지도를 로드 하 고 액세스할 준비가 된 후 요소가 표시 됩니다.

> [!TIP]
> 기본적으로의 성능을 위해 기호 레이어는 겹치는 기호를 숨겨 기호 렌더링을 최적화 합니다. 확대 하면 숨겨진 기호가 표시 됩니다. 이 기능을 사용 하지 않도록 설정 하 고 모든 기호를 항상 렌더링 `allowOverlap` 하려면 `iconOptions` 옵션의 속성을 `true`로 설정 합니다.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>기호 계층에 사용자 지정 아이콘 추가

기호 계층은 WebGL을 사용하여 렌더링됩니다. 아이콘 이미지와 같은 이러한 모든 리소스는 WebGL 컨텍스트에 로드해야 하기 때문입니다. 이 샘플에서는 지도 리소스에 사용자 지정 아이콘을 추가한 다음이를 사용 하 여 지도에서 사용자 지정 기호를 사용 하 여 점 데이터를 렌더링 하는 방법을 보여 줍니다. 기호 계층의 `textField` 속성에는 식을 지정해야 합니다. 이 경우 온도 속성을 렌더링 하려고 하지만 숫자 이기 때문에 문자열로 변환 해야 합니다. 또한 "° F"를 추가 하려고 합니다. 식을 사용 하 여이 작업을 수행할 수 있습니다. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='사용자 지정 기호 이미지 아이콘' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>사용자 지정 기호 이미지 아이콘</a>을 참조하세요.
</iframe>

> [!TIP]
> Azure Maps 웹 SDK는 기호 계층과 함께 사용할 수 있는 몇 가지 사용자 지정 가능 이미지 템플릿을 제공 합니다. 자세한 내용은 [이미지 템플릿 사용 방법](how-to-use-image-templates-web-sdk.md) 문서를 참조 하세요.

## <a name="customize-a-symbol-layer"></a>기호 계층 사용자 지정 

기호 계층에는 사용할 수 있는 많은 스타일 지정 옵션이 있습니다. 이러한 다양한 스타일 지정 테스트하는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='기호 계층 옵션' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/PxVXje/'>기호 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [팝업 추가](map-add-popup.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [이미지 템플릿을 사용 하는 방법](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [도형 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](map-add-bubble-layer.md)
