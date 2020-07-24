---
title: 맵에 트래픽 표시 | Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 트래픽 데이터를 표시하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3beafb28f67f5f1e1584d23a3d9603add2d09bee
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87130174"
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

<iframe height='500' scrolling='no' title='맵에 트래픽 표시' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>맵에 트래픽 표시</a>를 참조하세요.
</iframe>

## <a name="traffic-overlay-options"></a>트래픽 오버레이 옵션

다음 도구를 사용하면 다양한 트래픽 오버레이 설정 사이를 전환하여 렌더링이 어떻게 변경되는지 확인할 수 있습니다. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="트래픽 오버레이 옵션" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>트래픽 오버레이 옵션</a>을 참조하세요.
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
