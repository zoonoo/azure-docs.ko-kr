---
title: 지도에 교통 표시 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 트래픽 데이터를 표시하는 방법을 알아봅니다.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534817"
---
# <a name="show-traffic-on-the-map"></a>맵에 트래픽 표시

Azure Maps에는 두 가지 유형의 트래픽 데이터를 사용할 수 있습니다.

- 사고 데이터 - 건설, 도로 폐쇄 및 사고와 같은 사물에 대한 포인트 및 라인 기반 데이터로 구성됩니다.
- 흐름 데이터 - 도로의 교통 흐름에 대한 메트릭을 제공합니다. 종종 교통 흐름 데이터는 도로의 색상을 채색하는 데 사용됩니다. 색상은 속도 제한 또는 다른 메트릭을 기준으로 트래픽이 흐름을 느리게 하는 정도를 기반으로 합니다. Azure Maps의 트래픽 흐름 데이터에는 세 가지 측정 메트릭이 있습니다.
    - `relative`- 도로의 자유 흐름 속도에 상대적입니다.
    - `absolute`- 도로에있는 모든 차량의 절대 속도입니다.
    - `relative-delay`- 평균 예상 지연보다 느린 영역을 표시합니다.

다음 코드는 맵에 교통 데이터를 표시하는 방법을 보여 주었습니다.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='맵에 트래픽 표시' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>맵에 트래픽 표시</a>를 참조하세요.
</iframe>

## <a name="traffic-overlay-options"></a>트래픽 오버레이 옵션

다음 도구를 사용하면 서로 다른 트래픽 오버레이 설정 간에 전환하여 렌더링이 어떻게 변경되는지 확인할 수 있습니다. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="트래픽 오버레이 옵션" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>트래픽 오버레이 옵션을</a> 참조하십시오.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [지도](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [트래픽 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

다음 문서를 참조하여 사용자 환경을 개선할 수 있습니다.

> [!div class="nextstepaction"]
> [마우스 이벤트와 맵의 상호 작용](map-events.md)

> [!div class="nextstepaction"]
> [액세스할 수 있는 맵 작성](map-accessibility.md)

> [!div class="nextstepaction"]
> [코드 샘플 페이지](https://aka.ms/AzureMapsSamples)
