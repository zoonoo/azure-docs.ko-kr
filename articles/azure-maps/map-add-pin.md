---
title: Azure Maps를 사용하여 기호 및 표식 추가 | Microsoft Docs
description: Javascript 지도에 기호 및 표식을 추가하는 방법
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c56ac35f49c364b7b0f2ad26b82b178411419414
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282688"
---
# <a name="add-symbols-and-markers-to-a-map"></a>지도에 기호 및 표식 추가

이 문서에서는 데이터 원본을 사용하여 지도에 기호 및 표식을 추가하는 방법을 보여 줍니다.

## <a name="add-a-symbol-marker"></a>기호 표식 추가

<iframe height='500' scrolling='no' title='핀 고정 위치 전환' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>핀 고정 위치 전환</a>을 참조하세요.
</iframe>

위에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. 그런 다음, 점이 생성되어 데이터 원본에 추가됩니다. 점은 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 중 하나입니다.

세 번째 코드 블록은 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)를 만들고 마우스 클릭 시 shape 클래스 [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) 메서드를 사용하여 점의 좌표를 업데이트합니다.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다.  데이터 원본, 클릭 이벤트 수신기 및 기호 계층이 생성되고 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 점이 표시되도록 합니다.

## <a name="add-a-custom-symbol"></a>사용자 지정 기호 추가

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 클래스의 [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) 속성을 사용하여 지도에 [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)를 추가합니다. HtmlMarker가 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 표시되도록 합니다.

## <a name="add-bubble-markers"></a>거품형 표식 추가

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 위치 배열이 정의되고 [MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest) 개체가 생성됩니다. 그런 다음, [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 데이터 원본 개체가 생성되고 MultiPoint 개체가 데이터 원본에 추가됩니다.

[거품형 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)은 [데이터 원본](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 원으로 렌더링합니다. 마지막 코드 블록은 거품형 계층을 만들고 지도에 추가합니다. [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)에서 거품형 계층의 속성을 확인합니다.

MultiPoint 개체, 데이터 원본 및 거품형 계층이 생성되고 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 원이 표시되도록 합니다.

## <a name="add-bubble-markers-with-label"></a>레이블이 있는 거품형 표식 추가

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>를 참조하세요.
</iframe>

위 코드에서는 지도에 데이터를 시각화하고 레이블을 지정하는 방법을 보여 줍니다. 위에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 [point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 개체를 만듭니다. 그런 다음, [data source](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 데이터 원본 개체를 만들고 데이터 원본에 점을 추가합니다.

[거품형 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)은 [데이터 원본](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 원으로 렌더링합니다. 세 번째 코드 블록은 거품형 계층을 만들고 지도에 추가합니다. [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)에서 거품형 계층의 속성을 확인합니다.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다. 마지막 코드 블록은 거품형의 텍스트 레이블을 렌더링하는 기호 계층을 만들고 지도에 추가합니다. [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)에서 기호 계층의 속성을 참조하세요.

데이터 원본 및 계층이 생성되고 지도의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 지도가 완전히 로드된 후 데이터가 표시되도록 합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [팝업 추가](./map-add-popup.md)

> [!div class="nextstepaction"]
> [도형 추가](./map-add-shape.md)