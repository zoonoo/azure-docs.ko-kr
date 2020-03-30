---
title: 맵에 이미지 레이어 추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 이미지를 오버레이하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209718"
---
# <a name="add-an-image-layer-to-a-map"></a>맵에 이미지 계층 추가

이 문서에서는 고정좌표 세트에 이미지를 오버레이하는 방법을 보여 주며, 이 문서에서는 이미지를 오버레이하는 방법을 보여 주시면 됩니다. 다음은 맵에 중첩될 수 있는 다양한 이미지 유형의 몇 가지 예입니다.

* 드론에서 촬영한 이미지
* 건물 평면도
* 기록 또는 기타 특수 지도 이미지
* 작업 사이트의 청사진
* 기상 레이더 이미지

> [!TIP]
> [ImageLayer는](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 맵에 이미지를 오버레이하는 쉬운 방법입니다. 브라우저에서 큰 이미지를 로드하는 데 어려움이 있을 수 있습니다. 이 경우 이미지를 타일로 분할하고 [타일 레이어로](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)맵에 로드하는 것이 좋습니다.

이미지 레이어는 다음 이미지 형식을 지원합니다.

- JPEG
- PNG
- BMP
- GIF(애니메이션 없음)

## <a name="add-an-image-layer"></a>이미지 계층 추가

다음 코드는 [1922년 뉴저지 주 뉴어크 지도의](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 이미지를 지도에 오버레이합니다. [ImageLayer는](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 이미지에 URL을 전달하여 만들어지며 형식의 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`네 모서리에 대해 좌표합니다.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

다음은 이전 코드의 전체 실행 중인 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='단순 이미지 계층' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Pen Simple 이미지 계층</a>을 참조하세요.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>KML 파일을 접지 오버레이로 가져오기

이 샘플에서는 KML 접지 오버레이 정보를 맵의 이미지 레이어로 추가하는 방법을 보여 줍니다. KML 접지 오버레이는 북쪽, 남쪽, 동쪽 및 서쪽 좌표와 시계 반대 방향으로 회전을 제공합니다. 그러나 이미지 레이어는 이미지의 각 모서리에 대한 좌표를 기대합니다. 이 샘플의 KML 접지 오버레이는 샤르트르 대성당을 위한 것이며 [위키미디어에서](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)공급됩니다.

코드는 `getCoordinatesFromEdges` [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 클래스의 정적 함수를 사용합니다. KML 접지 오버레이의 북쪽, 남쪽, 동쪽, 서쪽 및 회전 정보를 사용하여 이미지의 네 모서리를 계산합니다.

<br/>

<iframe height='500' scrolling='no' title='이미지 계층으로 KML 지면 오버레이' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>이미지 계층으로 KML 지면 오버레이</a>를 참조하세요.
</iframe>

## <a name="customize-an-image-layer"></a>이미지 계층 사용자 지정

이미지 레이어에는 많은 스타일 지정 옵션이 있습니다. 여기에 그들을 밖으로 시도 하는 도구입니다.

<br/>

<iframe height='700' scrolling='no' title='이미지 계층 옵션' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>이미지 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [타일 계층 추가](./map-add-tile-layer.md)