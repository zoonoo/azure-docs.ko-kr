---
title: 맵에 트래픽 표시 | Microsoft Azure Maps
description: 지도에 트래픽 데이터를 추가 하는 방법을 알아보세요. 흐름 데이터에 대해 알아보고 Azure Maps 웹 SDK를 사용 하 여 지도에 인시던트 데이터 및 흐름 데이터를 추가 하는 방법을 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 584db064bf6f6a6b99e6e2d09f6046912cfcd1bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335248"
---
# <a name="show-traffic-on-the-map"></a>맵에 트래픽 표시

Azure Maps에서 사용할 수 있는 트래픽 데이터에는 두 가지 유형이 있습니다.

- 인시던트 데이터 - 건설, 도로 폐쇄 및 사고와 같은 사항에 대한 점 및 선 기반 데이터로 구성됩니다.
- 흐름 데이터 - 도로의 트래픽 흐름에 대한 메트릭을 제공합니다. 트래픽 흐름 데이터를 사용하여 도로 색을 조정하는 경우가 많습니다. 이 색은 속도 제한 또는 다른 메트릭을 기준으로 흐름을 저하시키는 트래픽 양을 기반으로 합니다. Azure Maps의 트래픽 흐름 데이터에는 세 가지 측정 메트릭이 있습니다.
    - `relative` - 도로의 자유 흐름 속도를 기준으로 합니다.
    - `absolute` - 도로에 있는 모든 자동차의 절대 속도입니다.
    - `relative-delay` - 평균 예상 지연 시간보다 느린 영역을 표시합니다.

다음 코드에서는 맵에 트래픽 데이터를 표시하는 방법을 보여줍니다.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='맵에 트래픽 표시' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>맵에 트래픽 표시</a>를 참조하세요.
</iframe>

## <a name="traffic-overlay-options"></a>트래픽 오버레이 옵션

다음 도구를 사용하면 다양한 트래픽 오버레이 설정 사이를 전환하여 렌더링이 어떻게 변경되는지 확인할 수 있습니다. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="트래픽 오버레이 옵션" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>트래픽 오버레이 옵션</a>을 참조하세요.
</iframe>


## <a name="add-traffic-controls"></a>트래픽 컨트롤 추가

지도에 추가할 수 있는 두 가지 트래픽 컨트롤이 있습니다. 첫 번째 컨트롤인는 `TrafficControl` 트래픽을 설정 하거나 해제 하는 데 사용할 수 있는 토글 단추를 추가 합니다. 이 컨트롤에 대 한 옵션을 사용 하 여 트래픽을 표시할 때 트래픽 설정이 사용 되는 시기를 지정할 수 있습니다. 기본적으로이 컨트롤은 상대 트래픽 흐름 및 인시던트 데이터를 표시 하지만 원하는 경우 절대 트래픽 흐름을 표시 하 고 인시던트를 표시 하지 않도록 변경할 수 있습니다. 두 번째 컨트롤인는 `TrafficLegendControl` 지도에 트래픽 흐름 범례를 추가 하 여 사용자가 강조 표시 된 색 코드의 의미를 이해 하는 데 도움을 줍니다. 이 컨트롤은 지도에 트래픽 흐름 데이터가 표시 되는 경우에만 표시 되 고 다른 모든 시간에는 숨겨집니다.

다음 코드에서는 지도에 트래픽 컨트롤을 추가 하는 방법을 보여 줍니다.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="트래픽 제어" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>트래픽 제어</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>


## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

다음 문서를 참조하여 사용자 환경을 개선할 수 있습니다.

> [!div class="nextstepaction"]
> [마우스 이벤트와 맵의 상호 작용](map-events.md)

> [!div class="nextstepaction"]
> [액세스할 수 있는 맵 작성](map-accessibility.md)

> [!div class="nextstepaction"]
> [코드 샘플 페이지](https://aka.ms/AzureMapsSamples)
