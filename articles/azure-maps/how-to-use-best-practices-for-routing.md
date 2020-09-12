---
title: Microsoft Azure Maps의 Azure Maps Route Service에 대 한 모범 사례
description: Microsoft Azure 맵에서 Route Service를 사용 하 여 차량을 라우팅하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b957453758b9b8e34989877516a9083f06a85ed8
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400786"
---
# <a name="best-practices-for-azure-maps-route-service"></a>경로 서비스 Azure Maps에 대 한 모범 사례

Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route) 의 경로 방향 및 경로 행렬 api는 요청 된 각 경로에 대 한 etas (예상 도착 시간)를 계산 하는 데 사용할 수 있습니다. 경로 Api는 요청 된 요일 및 시간에 대 한 일반적인 이동 속도와 같이 실시간 트래픽 정보 및 기록 트래픽 데이터와 같은 요인을 고려 합니다. 이 API는 시간 또는 거리를 기준으로 차례대로 또는 최적화된 순서대로 한 번에 여러 대상에서 사용할 수 있는 최단 경로 또는 가장 빠른 경로를 반환합니다. 또한 사용자는 워커, bicyclists 및 상업용 차량 (예: 트럭)에 대 한 특수 경로 및 세부 정보를 요청할 수 있습니다. 이 문서에서는 Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route)를 호출 하는 모범 사례를 공유 하 고 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 경로 방향 Api와 Matrix 라우팅 API 중에서 선택
> * 실시간 및 과거 트래픽 데이터를 기반으로 기록 및 예측 된 이동 시간을 요청 합니다.
> * 경로의 전체 경로 및 각 레그에 대 한 시간 및 거리와 같은 경로 세부 정보를 요청 합니다.
> * 트럭과 같은 상업 차량에 대 한 경로 요청
> * 용지를 통한 트래픽 정보 (예: 용지 걸림 및 요금 정보) 요청
> * 하나 이상의 중지 (waypoints)로 구성 된 경로 요청
> * 하나 이상의 중지 경로를 최적화 하 여 각 중지 (이동 경로)를 방문 하는 가장 좋은 순서 가져오기
> * 지원 점수를 사용 하 여 대체 경로를 최적화 합니다. 예를 들어, 전기 차량 청구 스테이션을 통과 하는 대체 경로를 제공 합니다.
> * Azure Maps 웹 SDK를 사용 하 여 [Route Service](https://docs.microsoft.com/rest/api/maps/route) 사용

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Route Service의 적용 범위에 대 한 자세한 내용은 [라우팅 검사](routing-coverage.md)를 참조 하십시오.

이 문서에서는 [Postman 앱](https://www.postman.com/downloads/) 을 사용 하 여 REST 호출을 빌드 하지만 모든 API 개발 환경을 선택할 수 있습니다.

## <a name="choose-between-route-directions-and-matrix-routing"></a>경로 방향 및 행렬 라우팅 중에서 선택

경로 방향 Api는 경로 경로에 대 한 이동 시간 및 좌표를 포함 하 여 지침을 반환 합니다. 경로 행렬 API를 사용 하면 원본 및 대상 위치에서 정의한 경로 집합에 대 한 이동 시간과 거리를 계산할 수 있습니다. Matrix API는 지정 된 모든 원본에 대해 해당 원본에서 지정 된 모든 대상으로 라우팅하는 비용 (이동 시간 및 거리)을 계산 합니다. 이러한 모든 Api를 통해 원하는 출발 시간, 도착 시간 및 차량 유형 (예: 차량 또는 트럭)과 같은 매개 변수를 지정할 수 있습니다. 이러한 데이터는 모두 실시간 또는 예측 트래픽 데이터를 적절 하 게 사용 하 여 최적의 경로를 반환 합니다.

시나리오에서 다음을 수행 하는 경우 경로 방향 Api를 호출 하는 것이 좋습니다.

* 배달 차량에 대 한 정확한 도착 시간을 얻기 위해 둘 이상의 알려진 위치 사이에서 가장 짧은 또는 가장 빠른 구동 경로를 요청 합니다.
* 지도에서 경로를 시각화 하는 경로 기 하 도형을 비롯 한 자세한 경로 지침 요청
* 고객 위치 목록이 지정 된 경우 가능한 최단 경로를 계산 하 여 각 고객 위치를 방문 하 여 원본으로 돌아갑니다. 이 시나리오는 일반적으로 여행 외판원 문제 라고 합니다. 하나의 요청에서 최대 150 waypoints (중지)를 전달할 수 있습니다.
* 단일 API 호출만 사용 하 여 경로 방향 일괄 처리 API로 쿼리의 일괄 처리를 보냅니다.

시나리오에서 다음을 수행 하는 경우 행렬 라우팅 API를 호출 하는 것이 좋습니다.

* 원본 및 대상 집합 사이의 이동 시간 또는 거리를 계산 합니다. 예를 들어 12 개의 드라이버가 있고 식당에서 식량 배달를 선택할 수 있는 가장 가까운 드라이버를 찾아야 합니다.
* 잠재적 경로를 실제 이동 거리 또는 시간을 기준으로 정렬 합니다. Matrix API는 각 원본 및 대상 조합에 대해 이동 시간 및 거리를 반환 합니다.
* 이동 시간이 나 거리를 기준으로 데이터를 클러스터링 합니다. 예를 들어 회사의 직원이 50 개인 경우 사무실에서 20 분 이내에 거주 하는 모든 직원을 찾습니다.

경로 방향 및 행렬 Api의 일부 기능을 보여 주는 비교는 다음과 같습니다.

| Azure Maps API | 요청의 최대 쿼리 수 | 영역 방지 | 트럭 및 전기 차량 라우팅 | Waypoints 및 여행 외판원 최적화 | 지원 요소 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 경로 방향 가져오기 | 1 | | X | X | |
| 경로 게시 지침 | 1 | X | X | X | X |
| 사후 경로 방향 일괄 처리 | 700 | | X | X | |
| 경로 매트릭스 게시 | 700 | | X | | |

전기 차량 라우팅 기능에 대해 자세히 알아보려면 [Python과 Azure Notebooks를 사용 하 여 전기 차량의 경로](tutorial-ev-routing.md)를 설정 하는 방법에 대 한 자습서를 참조 하세요.

## <a name="request-historic-and-real-time-data"></a>기록 및 실시간 데이터 요청

기본적으로 경로 서비스는 이동 모드가 자동차 이며 출발 시간이 현재 임을 전제로 합니다. 경로 계산 요청에서 달리 지정 하지 않는 한 실시간 트래픽 상태를 기반으로 경로를 반환 합니다. 시간이 종속 된 트래픽 제한은 고정 되어 있습니다. 예를 들어 ' 4:00 PM에서 6:00 PM 사이에 허용 되지 않는 ' 왼쪽으로는 안 됩니다 '와 같이 라우팅 엔진이이를 고려 합니다. Roadworks와 같은도로 클로저는 현재 라이브 트래픽을 무시 하는 경로를 특별히 요청 하지 않는 한 고려 됩니다. 현재 트래픽을 무시 하려면 `traffic` `false` API 요청에서을로 설정 합니다.

경로 계산 **travelTimeInSeconds** 값에는 트래픽으로 인 한 지연이 포함 됩니다. 출발 시간이 now로 설정 된 경우 현재 및 기록 이동 시간 데이터를 활용 하 여 생성 됩니다. 출발 시간이 미래에 설정 된 경우 Api는 기록 데이터를 기반으로 예측 된 이동 시간을 반환 합니다.

요청에 **computeTravelTimeFor = all** 매개 변수를 포함 하는 경우 응답의 요약 요소에는 기록 트래픽 상태를 포함 하 여 다음과 같은 추가 필드가 포함 됩니다.

| 요소 | 설명|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | 트래픽 조건으로 인해 경로에 지연이 발생 하지 않는 경우 처럼 계산 된 예상 이동 시간 (예: 정체 때문) |
| historicTrafficTravelTimeInSeconds | 시간 종속 기록 트래픽 데이터를 사용 하 여 계산 된 예상 이동 시간 |
| liveTrafficIncidentsTravelTimeInSeconds | 실시간 속도 데이터를 사용 하 여 계산 된 예상 이동 시간 |

다음 섹션에서는 설명 된 매개 변수를 사용 하 여 경로 Api를 호출 하는 방법을 보여 줍니다.

### <a name="sample-query"></a>샘플 쿼리

아래의 첫 번째 예제에서 출발 시간은 작성 시 앞으로 설정 됩니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

응답에는 아래와 같은 요약 요소가 포함 됩니다. 출발 시간이 미래로 설정 되기 때문에 **trafficDelayInSeconds** 값은 0입니다. **TravelTimeInSeconds** 값은 시간 종속 기록 트래픽 데이터를 사용 하 여 계산 됩니다. 따라서이 경우 **travelTimeInSeconds** 값은 **historicTrafficTravelTimeInSeconds** 값과 같습니다.

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

아래의 두 번째 예제에는 출발 시간이 현재 인 실시간 라우팅 요청이 있습니다. 기본값 이므로 URL에 명시적으로 지정 되지 않습니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

응답에는 아래와 같이 요약이 포함 되어 있습니다. 정체 때문에 **trafficDelaysInSeconds** 값이 0 보다 큽니다. **HistoricTrafficTravelTimeInSeconds**보다도 큽니다.

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

## <a name="request-route-and-leg-details"></a>요청 경로 및 레그 세부 정보

기본적으로 경로 서비스는 좌표 배열을 반환 합니다. 응답에는 라는 목록에서 경로를 구성 하는 좌표가 포함 됩니다 `points` . 또한 경로 응답에는 경로 시작과 예상 경과 시간 사이의 거리가 포함 됩니다. 이러한 값을 사용 하 여 전체 경로의 평균 속도를 계산할 수 있습니다.

다음 이미지는 요소를 보여 줍니다 `points` .

![요소 요소](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

요소를 확장 `point` 하 여 경로에 대 한 좌표 목록을 표시 합니다.

![확장 된 요소 요소](media/how-to-use-best-practices-for-routing/points-list-img.png)

경로 방향 Api는 **instructionsType** 매개 변수를 지정 하 여 사용할 수 있는 다양 한 형식의 명령을 지원 합니다. 컴퓨터를 쉽게 처리할 수 있도록 지침을 지정 하려면 **instructionsType = 코딩**을 사용 합니다. **InstructionsType = 태그** 를 사용 하 여 사용자에 게 지침을 텍스트로 표시 합니다. 또한 지침의 일부 요소가 표시 되는 텍스트로 표시 되 고 명령이 특수 한 서식으로 표시 될 수 있습니다. 자세한 내용은 [지원 되는 명령 유형 목록을](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)참조 하세요.

지침이 요청 되 면 응답은 라는 새 요소를 반환 합니다 `guidance` . `guidance`요소는 두 가지 정보, 즉 단계별 지침과 요약 된 지침을 포함 합니다.

![명령 유형](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

`instructions`요소는 여행에 대 한 턴을 설정 하 고에 요약 된 지침을 포함 합니다 `instructionGroups` . 각 명령 요약은 여러 면을 처리할 수 있는 왕복 세그먼트를 포함 합니다. Api는 경로의 섹션에 대 한 세부 정보를 반환할 수 있습니다. 예를 들어 신호등의 좌표 범위 또는 현재 트래픽 속도입니다.

![명령 설정](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![요약 된 지침](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>상업 차량에 대 한 경로 요청

Azure Maps 라우팅 Api는 상업적 트럭 라우팅을 지원 하 고 상용 차량 라우팅을 지원 합니다. Api는 지정 된 제한을 고려 합니다. 차량의 높이 및 무게와 차량에서 위험한 화물을 운반 하는 경우입니다. 예를 들어 차량에서 flammable를 운반 하는 경우 라우팅 엔진은 상설 영역 근처의 특정 터널을 방지 합니다.

### <a name="sample-query"></a>샘플 쿼리

아래 샘플 요청은 상업적 트럭에 대 한 경로를 쿼리 합니다. 트럭은 클래스 1의 유해 폐기물 물질을 운반 합니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

경로 API는 트럭 및 위험한 폐기물의 크기를 수용 하는 방향을 반환 합니다. 요소를 확장 하 여 경로 지침을 읽을 수 있습니다 `guidance` .

![클래스 1 hazwaste를 사용 하는 트럭](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>샘플 쿼리

위의 쿼리에서 US Hazmat 클래스를 변경 하면이 변경 내용을 수용 하기 위해 다른 경로가 생성 됩니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

아래 응답은 클래스 9 유해 자료를 운반 하는 트럭에 대 한 것입니다 .이는 클래스 1의 위험 재질 보다 위험 수준이 낮습니다. 요소를 확장 `guidance` 하 여 방향을 읽으면 방향이 동일 하지 않습니다. 트럭 운반 클래스 1 유해 자료에 대 한 경로 지침이 더 있습니다.



![클래스 9를 사용 하는 트럭 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>경로를 따라 트래픽 정보 요청

개발자는 Azure Maps 경로 방향 Api를 사용 하 여 `sectionType` 요청에 매개 변수를 포함 하 여 각 섹션 유형에 대 한 세부 정보를 요청할 수 있습니다. 예를 들어 각 트래픽 걸림 세그먼트에 대 한 속도 정보를 요청할 수 있습니다. 요청할 수 있는 다양 한 세부 정보에 대 한 자세한 내용은 [섹션 유형 키의 값 목록을](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) 참조 하세요.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 `sectionType` 를로 설정 합니다 `traffic` . 시애틀에서 San Diego 트래픽 정보를 포함 하는 섹션을 요청 합니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

응답에는 지정 된 좌표를 따라 트래픽에 적합 한 섹션이 포함 되어 있습니다.

![트래픽 섹션](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

이 옵션은 아래 이미지와 같이 지도를 렌더링할 때 섹션의 색을 지정할 때 사용할 수 있습니다. 

![지도에 렌더링 된 색이 지정 된 섹션](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>다중 중지 경로 계산 및 최적화

현재 Azure Maps는 다음과 같은 두 가지 형식의 경로 최적화를 제공 합니다.

* Waypoints의 순서를 변경 하지 않고 요청 된 경로 유형을 기반으로 하는 최적화입니다. [여기에서 지원 되는 경로 유형을](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype) 찾을 수 있습니다.

* 외판원 최적화를 통해 waypoints의 순서를 변경 하 여 각 중지를 방문 하는 가장 좋은 주문을 얻습니다.

다중 중지 라우팅의 경우 단일 경로 요청에 최대 150 waypoints을 지정할 수 있습니다. 라운드트립의 경우와 마찬가지로 시작 좌표와 끝 좌표 위치가 동일할 수 있습니다. 하지만 경로 계산을 수행 하려면 하나 이상의 추가 중간 경로를 제공 해야 합니다. Waypoints은 원본 좌표와 대상 좌표 사이에서 쿼리에 추가 될 수 있습니다.

지정 된 waypoints를 방문 하는 가장 좋은 순서를 최적화 하려면 **Ebestorder = true**를 지정 해야 합니다. 이 시나리오를 여행 외판원 최적화 문제 라고도 합니다.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 매개 변수를로 설정 하 여 6 개의 waypoints 경로를 요청 합니다 `computeBestOrder` `false` . 매개 변수의 기본값 이기도 합니다 `computeBestOrder` .

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

응답은 140851 미터의 경로 길이를 설명 하 고 해당 경로를 이동 하는 데 9991 초가 소요 될 수 있습니다.

![최적화 되지 않은 응답](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

아래 이미지는이 쿼리에서 생성 된 경로를 보여 줍니다. 이 경로는 가능한 한 경로입니다. 시간이 나 거리를 기준으로 하는 최적의 경로가 아닙니다.

![최적화 되지 않은 이미지](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



이 경로 중간 경로 순서는 0, 1, 2, 3, 4, 5 및 6입니다.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 위의 샘플과 같이 동일한 6 개의 waypoints 경로를 요청 합니다. 이번에는 `computeBestOrder` 매개 변수가로 설정 `true` 됩니다 (여행 외판원 최적화).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

응답은 91814 미터의 경로 길이를 설명 하 고 해당 경로를 이동 하는 데 7797 초가 소요 될 수 있습니다. API가 최적화 된 경로를 반환 하기 때문에 이동 거리와 이동 시간은 둘 다 더 낮습니다.

![최적화 된 응답](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

아래 이미지는이 쿼리에서 생성 된 경로를 보여 줍니다.

![최적화 된 이미지](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

최적의 경로에는 0, 5, 1, 2, 4, 3, 6의 중간 경로 순서가 있습니다.

>[!TIP]
> 라우팅 서비스에서 최적화 된 이동 경로 주문 정보는 일련의 인덱스를 제공 합니다. 이러한 인덱스는 원본 및 대상 인덱스를 제외 합니다. 원본을 고려 하 여 이러한 값을 1 씩 증가 시켜야 합니다. 그런 다음 대상을 끝에 추가 하 여 전체 주문 된 이동 경로 목록을 가져옵니다.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>지원 점수를 사용 하 여 대체 경로 계산 및 바이어스

경로를 다시 생성 하 여 참조 경로에 대해 0 개 이상의 대체 경로를 계산 하려는 경우가 있을 수 있습니다. 예를 들어 소매 스토어를 통과 하는 고객 대체 경로를 표시 하려고 할 수 있습니다. 이 경우 지원 점수를 사용 하 여 위치를 보정 해야 합니다. 위치를 보정 하는 단계는 다음과 같습니다.

1. 경로를 있는 그대로 계산 하 고 경로 응답에서 경로를 가져옵니다.
2. 경로 경로를 사용 하 여 경로 경로를 따라 원하는 위치를 찾습니다. 예를 들어 Azure Maps [관련 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) 를 사용 하거나 데이터베이스에서 사용자 고유의 데이터를 쿼리할 수 있습니다.  
3. 경로의 시작 부분 으로부터의 거리를 기준으로 위치 정렬
4. 이러한 위치를 새 경로 요청에 지원 지점으로 추가 하 여 [사후 경로 방향 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)에 추가 합니다. 지원 요소에 대 한 자세한 내용은 [경로 방향 게시 API 설명서](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)를 참조 하세요. 

[사후 경로 방향 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)를 호출할 때 지원 지점과 함께 최소 편차 시간 또는 거리 제약 조건을 설정할 수 있습니다. 대체 경로를 제공 하려는 경우에는이 매개 변수를 사용 합니다. 또한 이동 시간을 제한 하려고 합니다. 이러한 제약 조건을 사용 하면 대체 경로가 지정 된 시간 또는 거리에 대 한 원본 지점에서 참조 경로를 따릅니다. 즉, 다른 경로는 지정 된 제약 조건에 따라 참조 경로에서 분기 됩니다.

다음 이미지는 시간 및 거리에 대해 지정 된 편차 제한을 사용 하 여 대체 경로를 렌더링 하는 예입니다.

![대체 경로](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>웹 앱에서 라우팅 서비스 사용

Azure Maps 웹 SDK는 [서비스 모듈](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest)을 제공 합니다. 이 모듈은 JavaScript 또는 TypeScript를 사용 하 여 웹 또는 Node.js 응용 프로그램에서 Azure Maps REST Api를 쉽게 사용할 수 있도록 하는 도우미 라이브러리입니다. 서비스 모듈은 맵에 반환 된 경로를 렌더링 하는 데 사용할 수 있습니다. 모듈은 GET 및 POST 요청에 사용할 API를 자동으로 결정 합니다.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [Azure Maps 경로 서비스](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [서비스 모듈을 사용 하는 방법](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [지도에 경로 표시](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Azure Maps NPM 패키지](https://www.npmjs.com/package/azure-maps-rest  )
