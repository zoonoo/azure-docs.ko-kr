---
title: Azure Maps의 클러스터링 지점 데이터 | Microsoft Docs
description: 웹 SDK에서 point 데이터를 클러스터링 하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 69e95a9e6c76da5d502314a7190e99fc10e968f7
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639072"
---
# <a name="clustering-point-data"></a>클러스터링 지점 데이터

지도에서 많은 데이터 요소를 시각화할 때 점수가 서로 겹치면 지도는 복잡 하 게 보이고 보기 및 사용 하기 어려워집니다. Point 데이터의 클러스터링은 이러한 사용자 환경을 개선 하는 데 사용할 수 있습니다. 클러스터링 지점 데이터는 서로 가까이 있는 점 데이터를 결합 하 여 단일 클러스터형 데이터 요소로 맵에 표시 하는 프로세스입니다. 사용자가 지도를 확대 하면 클러스터는 개별 데이터 요소를 분리 합니다.

## <a name="enabling-clustering-on-a-data-source"></a>데이터 원본에서 클러스터링 사용

`cluster` 옵션을 true로 설정 하 여 `DataSource` 클래스에서 클러스터링을 쉽게 사용할 수 있습니다. 또한를 사용 하 여, 클러스터로 결합할 근처 요소를 선택할 수 있는 픽셀 반지름은를 `clusterRadius` 사용 하 여 설정할 수 있으며, 확대/축소 수준을 지정 하 여 `clusterMaxZoom` 옵션을 통해 클러스터링 논리를 사용 하지 않도록 설정할 수 있습니다. 데이터 원본에서 클러스터링을 사용 하도록 설정 하는 방법의 예는 다음과 같습니다.

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
> 두 데이터 요소를 함께 사용 하는 경우에는 사용자가 얼마나 가까이 있는지에 관계 없이 클러스터가 분리 되지 않습니다. 이를 해결 하려면 확대/축소 수준 `clusterMaxZoom` 에서 지정 하는 데이터 원본 옵션을 설정 하 여 클러스터링 논리를 사용 하지 않고 모든 항목을 표시 하면 됩니다.

또한 `DataSource` 클래스에는 클러스터링과 관련 된 다음 메서드가 있습니다.

| 메서드 | 반환 형식 | Description |
|--------|-------------|-------------|
| getClusterChildren (clusterId: number) | &lt;배열&gt; 기능 기 하 도형, 모든\| 모양&lt;&lt;&gt;&gt; | 다음 확대/축소 수준에서 지정 된 클러스터의 자식을 검색 합니다. 이러한 자식은 도형과 하위 클러스터의 조합일 수 있습니다. 하위 클러스터는 ClusteredProperties와 일치 하는 속성이 있는 기능입니다. |
| getClusterExpansionZoom (clusterId: number) | 약속&lt;번호&gt; | 클러스터가 확장 또는 분리를 시작 하는 확대/축소 수준을 계산 합니다. |
| getClusterLeaves (clusterId: number, limit: number, offset: number) | &lt;배열&gt; 기능 기 하 도형, 모든\| 모양&lt;&lt;&gt;&gt; | 클러스터에 있는 모든 요소를 검색 합니다. 를 설정 `offset` 하 여 점의 하위 집합을 반환 하 고를 사용 하 여 요소를 통해 페이지를 이동 합니다. `limit` |

## <a name="display-clusters-using-a-bubble-layer"></a>거품형 계층을 사용 하 여 클러스터 표시

거품형 계층은 radius를 쉽게 확장 하 고 식을 사용 하 여 클러스터의 요소 수에 따라 색을 변경할 수 있는 방식으로 클러스터링 된 요소를 렌더링 하는 좋은 방법입니다. 거품형 계층을 사용 하 여 클러스터를 표시 하는 경우 비클러스터형 데이터 요소를 렌더링 하는 데 별도의 계층도 사용 해야 합니다. 또한 거품 위에 클러스터 크기를 표시할 수 있는 것이 좋습니다. 텍스트를 포함 하 고 아이콘이 없는 기호 계층을 사용 하 여이 동작을 수행할 수 있습니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 거품형 계층 클러스터링" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>기본 거품형 계층 클러스터링</a> 을 참조 하세요.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>기호 계층을 사용 하 여 클러스터 표시

기호 계층을 사용 하 여 요소 데이터를 시각화 하는 경우에는 기본적으로 서로 겹치는 기호를 자동으로 숨겨서 클리너 환경을 만들기 때문에 지도의 데이터 요소 밀도를 확인 하려는 경우에는 원하는 환경이 아닐 수 있습니다. 기호 계층 `iconOptions` 속성의 `true` 옵션을로 설정 하면이 환경이 비활성화 되지만 모든 기호가 표시 됩니다. `allowOverlap` 클러스터링을 사용 하면 훌륭한 클린 사용자 환경을 만드는 동안 모든 데이터의 밀도를 확인할 수 있습니다. 이 샘플에서는 사용자 지정 기호를 사용 하 여 클러스터와 개별 데이터 요소를 나타냅니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터형 기호 계층" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>클러스터링 기호 계층</a> 을 참조 하세요.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>클러스터링 및 열 지도 계층

열 맵은 지도의 데이터 밀도를 표시 하는 좋은 방법입니다. 이 시각화는 자체에서 많은 수의 데이터 요소를 처리할 수 있지만 데이터 요소가 클러스터링 되 고 클러스터 크기가 열 지도의 가중치로 사용 되는 경우에도 더 많은 데이터를 처리할 수 있습니다. 이를 위해 열 지도 계층의 `['get', 'point_count']` 옵션을로설정합니다.`weight` 클러스터 반지름이 작은 경우 열 맵은 비클러스터형 데이터 요소를 사용 하 여 열 맵과 거의 동일 하 게 보이지만 훨씬 더 잘 수행 됩니다. 그러나 클러스터 반지름이 작을수록 열 맵은 더 정확 하 게 향상 되지만 성능 혜택은 줄어듭니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 가중치가 적용 되는 열 지도" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>클러스터 가중치가 매겨진 열 지도</a> 를 참조 하세요.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>클러스터 된 데이터 요소의 마우스 이벤트

클러스터 된 데이터 요소를 포함 하는 계층에서 마우스 이벤트가 발생 하면 클러스터형 데이터 요소가 이벤트에 GeoJSON point 기능 개체로 반환 됩니다. 이 지점 기능에는 다음과 같은 속성이 있습니다.

| 속성 이름 | 형식 | Description |
|---------------|------|-------------|
| cluster | boolean | 기능이 클러스터를 나타내는지 여부를 나타냅니다. |
| cluster_id | string | DataSource `getClusterExpansionZoom`, `getClusterChildren`및 메서드와함께사용할수있는클러스터의고유ID입니다.`getClusterLeaves` |
| point_count | number | 클러스터에 포함 된 지점의 수입니다. |
| point_count_abbreviated | string | Long 인 경우 값을 `point_count` 줄여서 표시 하는 문자열입니다. (예를 들어 4000은 4K가 됨) |

이 예에서는 클러스터 요소를 렌더링 하 고,이를 트리거하고 계산 하 고, `getClusterExpansionZoom` `DataSource` 클래스 의메서드를사용하여클러스터를분리하는다음확대/축소수준으로지도를확대/축소할때이벤트를추가하는거품형계층을사용합니다.`cluster_id` 클릭 한 클러스터형 데이터 요소의 속성입니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/moZWeV/'>클러스터 getClusterExpansionZoom</a> 를 참조 하세요.
</iframe>

## <a name="display-cluster-area"></a>클러스터 영역 표시 

클러스터가 나타내는 지점 데이터는 영역에 분산 됩니다. 이 샘플에서 마우스가 클러스터를 마우스로 가리킬 때 포함 된 개별 데이터 요소 (리프)는 볼록 선체를 계산 하 고 지도에 표시 하 여 영역을 표시 하는 데 사용 됩니다. 클러스터에 포함 된 모든 요소는 메서드를 `getClusterLeaves` 사용 하 여 데이터 원본에서 검색할 수 있습니다. 볼록 선체는 탄력적 밴드와 같은 일련의 점을 래핑하고 메서드를 `atlas.math.getConvexHull` 사용 하 여 계산할 수 있는 다각형입니다.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 영역 볼록 선체" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>클러스터 영역 볼록 선체</a> 를 참조 하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [DataSource 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas. math 네임 스페이스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [열 지도 계층 추가](map-add-heat-map-layer.md)
