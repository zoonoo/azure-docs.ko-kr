---
title: Azure 지도 경로 서비스에 대한 모범 사례 | 마이크로소프트 Azure 지도
description: Microsoft Azure 지도에서 경로 서비스를 사용하여 효율적으로 라우팅하는 방법에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335414"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure 지도 경로 서비스에 대한 모범 사례

Azure 지도 [경로 서비스의](https://docs.microsoft.com/rest/api/maps/route) 경로 경로 경로 및 경로 매트릭스 API를 사용하여 요청된 각 경로에 대한 예상 도착 시간(ETA)을 계산할 수 있습니다. 경로 API는 요청된 요일 및 시간대의 일반적인 도로 속도와 같은 실시간 교통 정보 및 과거 교통 데이터와 같은 요소를 고려합니다. API는 시간 또는 거리에 따라 한 번에 또는 최적화된 순서로 여러 대상에서 사용할 수 있는 가장 짧거나 빠른 경로를 반환합니다. 또한 보행자, 자전거 타는 사람 및 트럭과 같은 상용 차에 대한 전문 경로 및 세부 정보를 요청할 수 있습니다. 이 문서에서는 Azure Maps [경로 서비스라고](https://docs.microsoft.com/rest/api/maps/route)하는 모범 사례를 공유하고 다음 방법을 알아봅니다.

* 경로 방향 API와 행렬 라우팅 API 중에서 선택
* 실시간 및 과거 교통 데이터를 기반으로 기록 및 예측 된 이동 시간 요청
* 전체 경로 및 경로의 각 다리에 대한 시간 및 거리와 같은 경로 세부 정보를 요청합니다.
* 트럭과 같은 상용차 경로 요청
* 교통 체증 및 통행료 정보 와 같은 경로를 따라 교통 정보 요청
* 하나 이상의 경유지(웨이포인트)로 구성된 경로 요청
* 하나 이상의 경유지 경로를 최적화하여 각 정류장을 방문하는 최상의 순서(웨이포인트)를 얻습니다.
* 지원 지점을 사용하여 대체 경로를 최적화합니다. 예를 들어, 전기 자동차 충전소를 통과하는 대체 경로를 제공합니다.
* Azure 지도 웹 SDK에서 [경로 서비스](https://docs.microsoft.com/rest/api/maps/route) 사용

## <a name="prerequisites"></a>사전 요구 사항

Azure Maps API를 호출하려면 Azure Maps 계정과 키가 필요합니다. 자세한 내용은 [계정 만들기](quick-demo-map-app.md#create-an-account-with-azure-maps) 및 기본 [키 받기를](quick-demo-map-app.md#get-the-primary-key-for-your-account)참조하십시오. 기본 키는 기본 구독 키 또는 구독 키라고도 합니다.

Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리를](./how-to-manage-authentication.md)참조하십시오. 경로 서비스의 적용 범위에 대한 자세한 내용은 [라우팅 범위를](routing-coverage.md)참조하십시오.

이 문서에서는 [Postman 앱을](https://www.postman.com/downloads/) 사용하여 REST 호출을 빌드하지만 API 개발 환경을 선택할 수 있습니다.

## <a name="choose-between-route-directions-and-matrix-routing"></a>경로 방향과 행렬 라우팅 중에서 선택

경로 방향 API는 경로 경로의 이동 시간 및 좌표를 포함한 지침을 반환합니다. 경로 매트릭스 API를 사용하면 출발지 및 대상 위치에 의해 정의된 경로 집합의 이동 시간과 거리를 계산할 수 있습니다. 지정된 모든 원본에 대해 Matrix API는 해당 원본에서 지정된 모든 대상까지의 라우팅 비용(이동 시간 및 거리)을 계산합니다. 이러한 모든 API를 사용하면 원하는 출발 시간, 도착 시간 및 자동차 나 트럭과 같은 차량 유형과 같은 매개 변수를 지정할 수 있습니다. 이들은 모두 실시간 또는 예측 트래픽 데이터를 적절히 사용하여 최적의 경로를 반환합니다.

시나리오가 다음과 같은 경우 경로 방향 API를 호출하는 것이 좋습니다.

* 두 개 이상의 알려진 위치 사이의 가장 짧은 또는 가장 빠른 운전 경로를 요청하여 배달 차량의 정확한 도착 시간을 얻으십시오.
* 경로 지오메트리를 포함한 자세한 경로 안내를 요청하여 지도에서 경로를 시각화합니다.
* 고객 위치 목록을 지정하면 각 고객 위치를 방문하고 원점에서 돌아갈 수 있는 가장 짧은 경로를 계산합니다. 이 시나리오는 일반적으로 출장 세일즈맨 문제로 알려져 있습니다. 한 번의 요청으로 최대 150개의 웨이포인트(정류장)를 통과할 수 있습니다.
* 단일 API 호출만 사용하여 경로 방향 배치 API에 쿼리 일괄 처리를 보냅니다.

시나리오가 다음과 같은 경우 행렬 라우팅 API를 호출하는 것이 좋습니다.

* 출발지와 목적지 세트 사이의 이동 시간 또는 거리를 계산합니다. 예를 들어 12명의 드라이버가 있으며 식당에서 음식 배달을 받기 위해 가장 가까운 드라이버를 찾아야 합니다.
* 실제 이동 거리 또는 시간으로 잠재적 경로를 정렬합니다. Matrix API는 각 출발지와 대상 조합에 대한 이동 시간과 거리만 반환합니다.
* 이동 시간 또는 거리를 기반으로 하는 클러스터 데이터입니다. 예를 들어 회사에 50명의 직원이 있으며 사무실에서 운전 시간 20분 이내에 거주하는 모든 직원을 찾을 수 있습니다.

다음은 경로 방향 및 매트릭스 API의 일부 기능을 표시하는 비교입니다.

| Azure 지도 API | 요청의 최대 쿼리 수 | 영역 피하기 | 트럭 및 전기 자동차 라우팅 | 웨이포인트 및 여행 세일즈맨 최적화 | 지원 포인트 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 경로 방향 가져오기 | 1 | | X | X | |
| 경로 경로 안내 게시 | 1 | X | X | X | X |
| 포스트 경로 방향 배치 | 700 | | X | X | |
| 경로 매트릭스 게시 | 700 | | X | | |

전기 자동차 라우팅 기능에 대한 자세한 내용은 Azure [노트북을 사용하여 전기 자동차를 파이썬으로 라우팅하는](tutorial-ev-routing.md)방법에 대한 자습서를 참조하십시오.

## <a name="request-historic-and-real-time-data"></a>기록 및 실시간 데이터 요청

기본적으로 경로 서비스는 주행 모드가 자동차이고 출발 시간이 지금이라고 가정합니다. 경로 계산 요청이 달리 지정하지 않는 한 실시간 교통 상황에 따라 경로를 반환합니다. '4:00 PM에서 6:00 PM 사이에는 좌회전이 허용되지 않습니다'와 같은 고정된 시간 달라 교통 제한이 캡처되며 라우팅 엔진에서 고려됩니다. 현재 실시간 트래픽을 무시하는 경로를 구체적으로 요청하지 않는 한 도로 폐쇄는 도로 공사와 마찬가지로 고려됩니다. 현재 트래픽을 무시하려면 `traffic` API `false` 요청에 설정합니다.

경로 계산 **travelTimeInSeconds** 값에는 트래픽으로 인한 지연이 포함됩니다. 현재 및 과거 이동 시간 데이터를 활용하여 출발 시간이 현재로 설정되어 있습니다. 나중에 출발 시간을 설정한 경우 API는 기록 데이터를 기반으로 예상 이동 시간을 반환합니다.

요청에 **computeTravelTimeFor=모든** 매개 변수를 포함하는 경우 응답의 요약 요소에는 과거 교통 조건을 포함한 다음과 같은 추가 필드가 있습니다.

| 요소 | 설명|
| :--- | :--- |
| 노트래픽트래블타임세컨즈 | 교통 상황(예: 혼잡)으로 인해 경로에 지연이 없는 것으로 계산된 예상 이동 시간 |
| 히스토릭트래픽트래블타임세컨즈 | 시간 종속 기록 교통 데이터를 사용하여 계산된 예상 이동 시간 |
| 라이브 트래픽인시즈트래블타임인초 | 실시간 속도 데이터를 사용하여 계산된 예상 이동 시간 |

다음 섹션에서는 설명된 매개 변수를 사용하여 라우트 API를 호출하는 방법을 보여 줍니다.

### <a name="sample-query"></a>샘플 쿼리

출발 시간 아래의 첫 번째 예에서 쓰기 시 미래로 설정됩니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

응답에는 아래와 같은 요약 요소가 포함되어 있습니다. 출발 시간이 미래로 설정되어 있기 때문에 **트래픽DelayInSeconds** 값은 0입니다. **travelTimeInSeconds** 값은 시간에 따라 달라지는 기록 트래픽 데이터를 사용하여 계산됩니다. 따라서 이 경우 **travelTimeInSeconds** 값은 **기록트래픽트래블타임인초** 값과 같습니다.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>샘플 쿼리

아래 두 번째 예에서는 실시간 라우팅 요청이 있으며, 여기서 출발 시간이 지금입니다. 기본값이므로 URL에 명시적으로 지정되지 않았습니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

응답에는 아래와 같은 요약이 포함되어 있습니다. 정체로 인해 **트래픽DelaysInSeconds** 값이 0보다 큽니다. 그것은 또한 역사적인보다 더 **큰트래픽트래블타임초**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>경로 및 다리 세부 정보 요청

기본적으로 Route 서비스는 좌표 배열을 반환합니다. 응답에는 명명된 `points`목록에서 경로를 구성하는 좌표가 포함됩니다. 경로 응답에는 경로 시작시점의 거리와 예상 경과 시간도 포함됩니다. 이러한 값은 전체 경로의 평균 속도를 계산하는 데 사용할 수 있습니다.

다음 이미지는 요소를 `points` 보여 주며, 이 에 대한 것입니다.

<center>

![점 목록](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

`point` 요소를 확장하여 경로의 좌표 목록을 볼 수 있습니다.

<center>

![점 목록](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

경로 방향 API는 **명령문자 입력** 매개 변수를 지정하여 사용할 수 있는 다양한 형식의 명령을 지원합니다. 쉬운 컴퓨터 처리를 위한 지침을 서식을 지정하려면 **지침을 사용하여Type=코딩된**. **사용 명령유형=태그가 지정되어** 사용자의 텍스트로 지침을 표시합니다. 또한 명령어의 일부 요소가 표시되고 명령어에 특수 서식이 표시되는 텍스트로 서식을 지정할 수 있습니다. 자세한 내용은 [지원되는 명령 유형 목록을](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)참조하십시오.

지침이 요청되면 응답은 `guidance`.라는 새 요소를 반환합니다. 이 `guidance` 요소에는 턴바이턴 길찾기와 요약된 지침이라는 두 가지 정보가 있습니다.

<center>

![지침 유형](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

요소는 `instructions` 여행에 대한 턴 바이 턴 방향을 보유하고 `instructionGroups` 있으며, 요약 된 지침이 있습니다. 각 지침 요약은 여러 도로를 커버할 수 있는 여정의 일부를 다룹니다. API는 경로의 섹션에 대한 세부 정보를 반환할 수 있습니다. 교통 체증의 좌표 범위 또는 현재 교통 속도와 같은.

<center>

![턴 지침으로 돌리기](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![요약된 지침](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>상용차 노선 요청

Azure Maps 라우팅 API는 상용 트럭 라우팅을 포함하는 상용차량 라우팅을 지원합니다. API는 지정된 제한을 고려합니다. 등, 차량의 높이 와 무게, 그리고 차량이 위험화물을 운반하는 경우. 예를 들어, 차량이 인화성 물질을 운반하는 경우 라우팅 엔진은 주거 지역 근처의 특정 터널을 피합니다.

### <a name="sample-query"></a>샘플 쿼리

아래 샘플 요청은 상업용 트럭의 경로를 쿼리합니다. 트럭은 1급 유해 폐기물을 운반하고 있습니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Route API는 트럭의 치수와 유해 폐기물을 수용하는 방향을 반환합니다. 요소를 확장하여 경로 지침을 `guidance` 읽을 수 있습니다.

<center>

![클래스 1 헤이즈폐기물트럭](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>샘플 쿼리

위의 쿼리에서 US Hazmat 클래스를 변경하면 이 변경 사항을 수용할 수 있는 다른 경로가 생성됩니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

아래 응답은 클래스 1 유해 물질보다 덜 위험한 클래스 9 유해 물질을 운반하는 트럭에 대한 것입니다. `guidance` 방향을 읽도록 요소를 확장하면 방향이 동일하지 않다는 것을 알 수 있습니다. 클래스 1 유해 물질을 운반하는 트럭에 대한 더 많은 경로 지침이 있습니다.

<center>

![9급 헤이즈폐기물트럭](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>경로를 따라 교통 정보 요청

개발자는 Azure Maps 경로 방향 API를 통해 요청에 매개 변수를 `sectionType` 포함하여 각 섹션 유형에 대한 세부 정보를 요청할 수 있습니다. 예를 들어 각 교통 체증 구간에 대한 속도 정보를 요청할 수 있습니다. 요청할 수 있는 다양한 세부 정보에 대해 알아보려면 [섹션유형 키의 값 목록을](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) 참조하십시오.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 `sectionType` `traffic`을 로 설정합니다. 시애틀에서 샌디에이고까지의 교통 정보가 포함된 섹션을 요청합니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

응답에는 지정된 좌표를 따라 트래픽에 적합한 섹션이 포함되어 있습니다.

<center>

![트래픽 섹션](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

이 옵션은 아래 이미지와 같이 맵을 렌더링할 때 섹션에 색상을 매를 사용하는 데 사용할 수 있습니다. 

<center>

![트래픽 섹션](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>멀티 스톱 경로 계산 및 최적화

Azure Maps는 현재 두 가지 형태의 경로 최적화를 제공합니다.

* 웨이포인트 순서를 변경하지 않고 요청된 경로 유형을 기반으로 하는 최적화입니다. [지원되는 경로 유형은 여기에서](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype) 확인할 수 있습니다.

* 여행 세일즈맨 최적화, 각 정류장을 방문하는 가장 좋은 순서를 얻기 위해 웨이 포인트의 순서를 변경

다중 스톱 라우팅의 경우 단일 경로 요청에 최대 150개의 웨이포인트를 지정할 수 있습니다. 시작 및 끝 좌표 위치는 왕복의 경우와 마찬가지로 동일할 수 있습니다. 그러나 경로를 계산하려면 하나 이상의 추가 웨이포인트를 제공해야 합니다. 웨이포인트는 원점과 대상 좌표 사이에 있는 쿼리에 추가할 수 있습니다.

지정된 웨이포인트를 방문하기 위해 최상의 순서를 최적화하려면 **computeBestOrder=true를**지정해야 합니다. 이 시나리오를 출장 세일즈맨 최적화 문제라고도 합니다.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 `computeBestOrder` 매개 변수를 로 설정하여 6개의 웨이포인트에 `false`대한 경로를 요청합니다. 매개 변수의 기본값이기도 `computeBestOrder` 합니다.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

응답은 140,851 미터의 경로 길이를 설명하고 해당 경로를 이동하는 데 9,991 초가 걸릴 것이라고 설명합니다.

<center>

![최적화되지 않은 응답](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

아래 이미지는 이 쿼리에서 발생하는 경로를 보여 줍니다. 이 경로는 하나의 가능한 경로입니다. 시간이나 거리에 따라 최적의 경로가 아닙니다.

<center>

![최적화되지 않은 이미지](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

이 경로 웨이포인트 순서는 0, 1, 2, 3, 4, 5 및 6입니다.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 위의 샘플과 같이 동일한 6개의 웨이포인트에 대한 경로를 요청합니다. 이번에는 매개 `computeBestOrder` 변수가 `true` (여행 세일즈맨 최적화)로 설정됩니다.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

응답은 91,814미터의 경로 길이와 해당 경로를 이동하는 데 7,797초가 걸린다는 것을 설명합니다. API가 최적화된 경로를 반환했기 때문에 이동 거리와 이동 시간이 모두 낮습니다.

<center>

![최적화되지 않은 응답](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

아래 이미지는 이 쿼리에서 발생하는 경로를 보여 줍니다.

<center>

![최적화되지 않은 이미지](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

최적 경로에는 0, 5, 1, 2, 4, 3 및 6의 웨이포인트 순서가 있습니다.

>[!TIP]
> 라우팅 서비스에서 최적화된 웨이포인트 주문 정보는 인덱스 집합을 제공합니다. 원지와 대상 인덱스는 제외됩니다. 원한을 고려하려면 이러한 값을 1씩 증분해야 합니다. 그런 다음 목적지를 끝까지 추가하여 전체 정렬 된 웨이 포인트 목록을 가져옵니다.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>지지점을 사용하여 대체 경로 계산 및 바이어스

참조 경로에 대해 0개 이상의 대체 경로를 계산하기 위해 경로를 재구성하려는 상황이 있을 수 있습니다. 예를 들어 소매점을 통과하는 대체 경로를 고객에게 표시할 수 있습니다. 이 경우 지원 점을 사용하여 위치를 편향해야 합니다. 위치를 편향하는 단계는 다음과 같습니다.

1. 있는 경로를 계산하고 경로 응답에서 경로를 가져옵니다.
2. 경로 경로를 사용하여 경로 경로를 따라 또는 근처에서 원하는 위치를 찾습니다. 예를 들어 Azure Maps [관심 지점 API를](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) 사용하거나 데이터베이스에서 자신의 데이터를 쿼리할 수 있습니다.  
3. 경로 시작 지점으로부터의 거리에 따라 위치 주문
4. 이러한 위치를 새 경로 요청의 지원 지점으로 추가하여 [경로 방향 게시 길 API에](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)추가합니다. 지원 포인트에 대한 자세한 내용은 [경로 경로 방향 API 문서 참조.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) 

[경로 이동 후 API를](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)호출할 때 지원 지점과 함께 최소 편차 시간 또는 거리 구속조건을 설정할 수 있습니다. 대체 경로를 제공하려는 경우 이러한 매개변수를 사용하지만 이동 시간을 제한하려는 경우도 있습니다. 이러한 구속조건을 사용하는 경우 대체 배관은 지정된 시간 또는 거리에 대한 원점에서 참조 경로를 따릅니다. 즉, 다른 배관은 지정된 제약 조건에 따라 참조 경로에서 분기됩니다.

아래 이미지는 시간과 거리에 대해 지정된 편차 제한이 있는 대체 경로를 렌더링하는 예입니다.

<center>

![대체 경로](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>웹 앱에서 라우팅 서비스 사용

Azure 지도 웹 SDK는 [서비스 모듈을](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest)제공합니다. 이 모듈은 자바스크립트 또는 TypeScript를 사용하여 웹 또는 Node.js 응용 프로그램에서 Azure Maps REST API를 쉽게 사용할 수 있는 도우미 라이브러리입니다. 서비스 모듈을 사용하여 맵에서 반환된 경로를 렌더링할 수 있습니다. 이 모듈은 GET 및 POST 요청과 함께 사용할 API를 자동으로 결정합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하십시오.

> [!div class="nextstepaction"]
> [Azure 지도 경로 서비스](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [서비스 모듈 사용 방법](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [지도에 경로 표시](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Azure 지도 NPM 패키지](https://www.npmjs.com/package/azure-maps-rest  )
