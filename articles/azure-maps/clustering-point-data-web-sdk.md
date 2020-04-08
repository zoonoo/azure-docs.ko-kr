---
title: 지도의 클러스터링 포인트 데이터 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 포인트 데이터를 클러스터화하고 맵에 렌더링하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804574"
---
# <a name="clustering-point-data"></a>클러스터링 포인트 데이터

맵에서 많은 데이터 점을 시각화할 때 데이터 요소가 서로 겹칠 수 있습니다. 겹치면 맵을 읽을 수 없고 사용하기 어려워질 수 있습니다. 지점 데이터 클러스터링은 서로 가까이 있는 지점 데이터를 결합하여 맵에 단일 클러스터형 데이터 요소로 표시하는 프로세스입니다. 사용자가 맵을 확대하면 클러스터는 개별 데이터 요소로 분리됩니다. 많은 수의 데이터 포인트로 작업할 때 클러스터링 프로세스를 사용하여 사용자 환경을 개선합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>데이터 원본에서 클러스터링 사용

`cluster` 옵션을 true로 `DataSource` 설정하여 클래스에서 클러스터링을 활성화합니다. 근처의 `ClusterRadius` 점을 선택하도록 설정하고 클러스터로 결합합니다. `ClusterRadius` 값은 픽셀입니다. 클러스터링 논리를 비활성화할 확대/축소 수준을 지정하는 데 사용합니다. `clusterMaxZoom` 다음은 데이터 원본에서 클러스터링을 사용하도록 설정하는 방법의 예입니다.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> 두 데이터 요소가 지면에서 서로 가깝으면 사용자가 확대해도 클러스터가 분리되지 않을 수 있습니다. 이 문제를 해결하려면 클러스터링 논리를 `clusterMaxZoom` 사용하지 않도록 설정하고 모든 것을 표시하는 옵션을 설정할 수 있습니다.

다음은 클래스가 `DataSource` 클러스터링을 위해 제공하는 추가 메서드입니다.

| 방법 | 반환 형식 | Description |
|--------|-------------|-------------|
| get클러스터자식(클러스터Id: 번호) | 약속&lt;&lt;배열&lt;피쳐&gt; \| 형상, 모든 모양&gt;&gt; | 다음 확대/축소 수준에서 지정된 클러스터의 자식 요소를 검색합니다. 이러한 자식 요소는 도형과 하위 클러스터의 조합일 수 있습니다. 하위 클러스터는 ClusteredProperties와 일치하는 속성이 있는 기능입니다. |
| get클러스터확장확대Zoom(클러스터Id: 번호) | Promise&lt;number&gt; | 클러스터가 확장 또는 분리를 시작하는 확대/축소 수준을 계산합니다. |
| getClusterLeaves(클러스터Id: 번호, 제한: 수, 오프셋: 번호) | 약속&lt;&lt;배열&lt;피쳐&gt; \| 형상, 모든 모양&gt;&gt; | 클러스터의 모든 지점을 검색합니다. `limit`를 설정하여 지점의 하위 세트를 반환하고, `offset`을 사용하여 지점을 통해 페이지를 이동합니다. |

## <a name="display-clusters-using-a-bubble-layer"></a>거품 레이어를 사용하여 클러스터 표시

버블 레이어는 클러스터된 점을 렌더링하는 좋은 방법입니다. 식을 사용하여 반지름의 크기를 조정하고 클러스터의 점 수에 따라 색상을 변경합니다. 거품 레이어를 사용하여 클러스터를 표시하는 경우 별도의 레이어를 사용하여 클러스터되지 않은 데이터 포인트를 렌더링해야 합니다.

풍선 위에 클러스터 의 크기를 표시하려면 텍스트가 있는 기호 레이어를 사용하고 아이콘을 사용하지 마십시오.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 버블 레이어 클러스터링" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의한 펜 <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>기본 버블 레이어 클러스터링을</a> 참조하십시오.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>기호 레이어를 사용하여 클러스터 표시

데이터 포인트를 시각화할 때 기호 레이어는 서로 겹치는 기호를 자동으로 숨겨 사용자 인터페이스를 더 깨끗하게 합니다. 맵에 데이터 요소 밀도를 표시하려는 경우 이 기본 동작이 바람직하지 않을 수 있습니다. 그러나 이러한 설정은 변경할 수 있습니다. 모든 기호를 표시하려면 `allowOverlap` 기호 레이어 `iconOptions` 속성옵션을 `true`로 설정합니다. 

클러스터링을 사용하여 깔끔한 사용자 인터페이스를 유지하면서 데이터 요소 밀도를 표시합니다. 아래 샘플에서는 사용자 지정 기호를 추가하고 기호 레이어를 사용하여 클러스터 및 개별 데이터 포인트를 나타내는 방법을 보여 주며 있습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터된 기호 레이어" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>클러스터된 기호 레이어를</a> 참조하십시오.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>클러스터링 및 히트 맵 레이어

히트 맵은 맵에 데이터의 밀도를 표시하는 좋은 방법입니다. 이 시각화 방법은 자체적으로 많은 수의 데이터 포인트를 처리할 수 있습니다. 데이터 요소가 클러스터되고 클러스터 크기가 히트 맵의 가중치로 사용되는 경우 히트 맵은 더 많은 데이터를 처리할 수 있습니다. 이 옵션을 얻으려면 히트 `weight` 맵 레이어의 옵션을 `['get', 'point_count']`로 설정합니다. 클러스터 반지름이 작으면 히트 맵은 클러스터되지 않은 데이터 포인트를 사용하는 히트 맵과 거의 동일하게 보이지만 훨씬 더 잘 수행됩니다. 그러나 클러스터 반경이 작을수록 히트 맵이 더 정확하지만 성능상의 이점이 적습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 가중 히트 맵" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>클러스터 가중치 열 맵을</a> 참조하십시오.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>클러스터된 데이터 포인트의 마우스 이벤트

클러스터된 데이터 요소가 포함된 레이어에서 마우스 이벤트가 발생하면 클러스터된 데이터 요소가 GeoJSON 점 피쳐 개체로 이벤트로 돌아갑니다. 이 점 피쳐에는 다음과 같은 속성이 있습니다.

| 속성 이름             | Type    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 기능이 클러스터를 표시하는지 여부를 나타냅니다. |
| `cluster_id`              | 문자열  | DataSource `getClusterExpansionZoom`, `getClusterChildren` 및 `getClusterLeaves` 메서드에 사용할 수 있는 클러스터의 고유 ID입니다. |
| `point_count`             | number  | 클러스터에 포함된 지점의 수입니다.  |
| `point_count_abbreviated` | 문자열  | 값이 길면 `point_count` 값을 축약하는 문자열입니다. (예: 4,000은 4K)  |

이 예제는 클러스터 포인트를 렌더링하고 클릭 이벤트를 추가하는 거품 레이어를 수행합니다. 클릭 이벤트가 트리거되면 코드는 맵을 다음 확대/축소 수준으로 계산하고 확대하여 클러스터가 분리됩니다. 이 기능은 `getClusterExpansionZoom` `DataSource` 클래스의 메서드와 클릭한 `cluster_id` 클러스터된 데이터 포인트의 속성을 사용하여 구현됩니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 get클러스터 확장Zoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>코드펜에서</a>펜 <a href='https://codepen.io/azuremaps/pen/moZWeV/'>클러스터 getCluster확장확대확대를</a> Azure 지도 ()로<a href='https://codepen.io/azuremaps'>@azuremaps</a>확인합니다.
</iframe>

## <a name="display-cluster-area"></a>클러스터 영역 표시 

클러스터가 나타내는 점 데이터가 영역에 분산되어 있습니다. 이 샘플에서는 마우스가 클러스터 위로 마우스를 가져가면 두 가지 주요 동작이 발생합니다. 먼저 클러스터에 포함된 개별 데이터 요소가 볼록 선체를 계산하는 데 사용됩니다. 그런 다음 볼록한 선체가 맵에 표시되어 영역을 표시합니다.  볼록 선체는 탄성 밴드와 같은 점 집합을 래핑하고 `atlas.math.getConvexHull` 이 방법을 사용하여 계산할 수 있는 다각형입니다. 클러스터에 포함된 모든 포인트는 `getClusterLeaves` 메서드를 사용하여 데이터 원본에서 검색할 수 있습니다.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 영역 볼록 선체" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>코드펜에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>클러스터 영역 볼록 선체를</a> 참조하십시오.
</iframe>

## <a name="aggregating-data-in-clusters"></a>클러스터의 데이터 집계

클러스터는 클러스터 내에 있는 점 수가 있는 기호를 사용하여 표시되는 경우가 많습니다. 그러나 경우에 따라 추가 메트릭을 사용하여 클러스터 스타일을 사용자 지정하는 것이 바람직합니다. 클러스터 집계를 사용하면 [집계 식](data-driven-style-expressions-web-sdk.md#aggregate-expression) 계산을 사용하여 사용자 지정 속성을 만들고 채울 수 있습니다.  클러스터 집계는 `clusterProperties` `DataSource`의 옵션에서 정의할 수 있습니다.

다음 샘플에서는 집계 식을 사용합니다. 코드는 클러스터의 각 데이터 요소의 엔터티 형식 속성을 기반으로 개수를 계산합니다. 사용자가 클러스터를 클릭하면 클러스터에 대한 추가 정보가 있는 팝업이 표시됩니다.

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 집계" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a>펜 <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>클러스터 집계를</a> 참조하십시오.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [DataSource 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [데이터 소스옵션 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.수학 네임스페이스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [거품 레이어 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [기호 레이어 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [열 지도 계층 추가](map-add-heat-map-layer.md)
