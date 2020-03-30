---
title: 맵에 컨트롤 추가 | 마이크로소프트 Azure 지도
description: 확대/축소 컨트롤, 피치 컨트롤, 회전 제어 및 스타일 선택기를 Microsoft Azure 지도의 맵에 추가하는 방법.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334556"
---
# <a name="add-controls-to-a-map"></a>맵에 컨트롤 추가

이 문서에서는 맵에 컨트롤을 추가하는 방법을 보여 주며, 이 문서에서는 컨트롤을 추가하는 방법을 보여 주며, 이 문서에서는 컨트롤을 추가하는 방법을 보여 주시면 됩니다. 또한 모든 컨트롤과 [스타일 선택기를](https://docs.microsoft.com/azure/azure-maps/choose-map-style)사용하여 맵을 만드는 방법에 대해서도 알아봅니다.

## <a name="add-zoom-control"></a>확대/축소 컨트롤 추가

확대/축소 컨트롤은 맵을 확대/축소하기 위한 단추를 추가합니다. 다음 코드 샘플은 [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 클래스의 인스턴스를 만들고 맵의 오른쪽 아래 모서리에 추가합니다.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='확대/축소 컨트롤 추가' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>확대/축소 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="add-pitch-control"></a>피치 컨트롤 추가

피치 컨트롤은 수평선을 기준으로 매핑피치를 기울이기 위한 버튼을 추가합니다. 다음 코드 샘플은 [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 클래스의 인스턴스를 만듭니다. 맵의 오른쪽 상단 모서리에 피치 컨트롤이 추가됩니다.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='피치 컨트롤 추가' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>피치 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="add-compass-control"></a>나침반 컨트롤 추가

나침반 컨트롤은 맵을 회전하기 위한 단추를 추가합니다. 다음 코드 샘플은 [나침반 제어](/javascript/api/azure-maps-control/atlas.control.compasscontrol) 클래스의 인스턴스를 만들고 맵의 왼쪽 아래 모서리를 추가합니다.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='회전 컨트롤 추가' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>회전 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="a-map-with-all-controls"></a>모든 컨트롤이 있는 지도

여러 컨트롤을 배열에 넣고 맵에 한 번에 추가하고 맵의 동일한 영역에 배치하여 개발을 간소화할 수 있습니다. 다음은 이 방법을 사용하여 맵에 표준 탐색 컨트롤을 추가합니다.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

다음 코드 샘플은 맵의 오른쪽 상단 모서리에 확대/축소, 나침반, 피치 및 스타일 선택기 컨트롤을 추가합니다. 자동으로 스택하는 방법을 확인합니다. 스크립트의 컨트롤 개체 순서에 따라 지도에서 해당 개체가 표시되는 순서가 결정됩니다. 맵에서 컨트롤의 순서를 변경하려면 배열에서 컨트롤의 순서를 변경할 수 있습니다.

<br/>

<iframe height='500' scrolling='no' title='모든 컨트롤이 있는 지도' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>모든 컨트롤이 있는 지도</a>를 참조하세요.
</iframe>

스타일 선택기 컨트롤은 [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 클래스에 의해 정의됩니다. 스타일 선택기 컨트롤 사용에 대한 자세한 내용은 [맵 스타일 선택을](choose-map-style.md)참조하십시오.

## <a name="customize-controls"></a>컨트롤 사용자 지정

다음은 컨트롤을 사용자 지정하기 위한 다양한 옵션을 테스트하는 도구입니다.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="탐색 제어 옵션" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>탐색 제어 옵션을</a> 참조하십시오.
</iframe>

사용자 지정된 탐색 컨트롤을 만들려면 `atlas.Control` 클래스에서 확장되는 클래스를 만들거나 HTML 요소를 만들어 맵 div 위에 배치합니다. 이 UI 컨트롤이 `setCamera` 맵 함수를 호출하여 맵을 이동하도록 합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [나침반 제어](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

전체 코드는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [핀 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [팝업 추가](./map-add-popup.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [거품 레이어 추가](map-add-bubble-layer.md)

