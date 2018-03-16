---
title: "Azure Location Based Services의 여러 경로 | Microsoft Docs"
description: "Azure Location Based Services를 사용하여 여러 여행 모드에 대한 경로 찾기"
services: location-based-services
keywords: 
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 986e8667ca421baa78647e77e43fef92d81a7982
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Azure Location Based Services를 사용하여 여러 여행 모드에 대한 경로 찾기

이 자습서에서는 Azure Location Based Services 계정과 Route Service SDK를 사용하여 여행 모드 우선 순위에 따라 관심 지점까지의 경로를 찾는 방법을 보여줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Route Service 쿼리 구성
> * 여행 모드에 따라 우선 순위가 지정된 경로 렌더링

## <a name="prerequisites"></a>필수 조건

계속 진행하기에 앞서 [Azure Location Based Services 계정 만들기](./tutorial-search-location.md#createaccount) 및 [계정에서 키 가져오기](./tutorial-search-location.md#getkey)를 수행해야 합니다. 또한 [Azure Location Based Services를 사용하여 주변 관심 지점 검색](./tutorial-search-location.md) 자습서에서 배운 내용에 따라 맵 컨트롤 및 Search Service API를 사용하는 방법과 [Azure Location Based Services를 사용해 관심 지점까지 라우팅](./tutorial-route-location.md) 자습서의 설명에 따라 Route Service API의 기본 사용법을 숙지해야 합니다.


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Route Service 쿼리 구성

Location Based Services의 맵 컨트롤 API가 포함된 정적 HTML 페이지를 만들려면 다음 단계를 사용합니다. 

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapTruckRoute.html**로 지정합니다. 
2. 다음 HTML 구성 요소를 파일에 추가합니다.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    
    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    HTML 헤더가 Azure Location Based Services 라이브러리의 CSS 및 JavaScript 파일에 대한 리소스 위치를 포함합니다. 또한 HTML 본문에 추가된 *스크립트* 세그먼트에는 Azure Map Control API에 액세스하는 인라인 JavaScript 코드가 포함됩니다.
3. 다음 JavaScript 코드를 HTML 파일의 *스크립트* 블록에 추가합니다. 자리 표시자 *<insert-key>*를 Location Based Services 계정의 기본 키로 바꿉니다.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas Map**은 시각적 및 대화형 웹 맵에 대한 컨트롤을 제공하고 Azure 맵 컨트롤 API의 구성 요소입니다.

4. 다음 JavaScript 코드를 *스크립트* 블록에 추가하여 트래픽 흐름 표시를 맵에 추가합니다.

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    이 코드는 트래픽 흐름을 자유 흐름에 대한 상대 도로 속도인 `relative`로 설정합니다. `absolute` 또는 자유 흐름과 다른 상대 속도를 표시하는 `relative-delay`로 설정할 수도 있습니다. 

5. 다음 JavaScript 코드를 추가하여 경로의 출발점과 도착점에 대한 핀을 만듭니다.

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    이 코드는 경로의 출발점과 도착점을 나타낼 두 개의 [GeoJSON 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만듭니다. 

6. 다음 JavaScript 코드를 추가하여 맵 컨트롤에 *linestrings* 계층을 추가하고, 운송 모드에 따라(예: _자동차_ 및 _트럭_) 경로를 표시합니다.

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

7. 다음 JavaScript 코드를 추가하여 맵에 출발점과 도착점을 추가합니다.

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    **map.setCameraBounds** API는 출발점과 도착점의 좌표에 따라 맵 창을 조정합니다. **map.addPins** API는 점을 시각적 구성 요소로 맵 컨트롤에 추가합니다.

8. **MapTruckRoute.html** 파일을 컴퓨터에 저장합니다. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>여행 모드에 따라 우선 순위가 지정된 경로 렌더링

이 섹션에서는 Azure Location Based Services의 Route Service API를 사용하여 주어진 출발점에서 목적지까지 운송 모드에 따라 경로를 찾는 방법을 보여줍니다. Route Service는 실시간 교통 상황을 고려하여 두 지점 간에 가장 빠르거나, 가장 짧거나, 경제적인 경로를 계획할 수 있도록 API를 제공합니다. 또한 사용자는 Azure의 광범위한 교통 기록 데이터베이스를 사용해 어떤 날짜 및 시간에 대한 경로 기간을 예측하여 미래의 경로를 계획할 수 있습니다. 

1. 이전 섹션에서 만든 **MapTruckRoute.html** 파일을 열고, 다음 JavaScript 코드를 *스크립트* 블록에 추가하고, Route Service를 사용하여 트럭의 경로를 가져옵니다.

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + LBSAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    이 코드 조각은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 생성하고, 들어오는 응답을 구문 분석하는 이벤트 처리기를 추가합니다. 응답이 성공하면 반환된 경로에 대한 좌표 배열을 만들고 맵의 `truckRouteLayerName` 레이어에 추가합니다. 
    
    또한 이 코드 조각은 계정 키에 대한 Route Service에 쿼리를 보내고, 지정된 출발점 및 도착점에 대한 경로를 가져옵니다. 다음과 같은 선택적 매개 변수는 대형 트럭의 경로를 표시하는 데 사용됩니다.
   - `travelMode=truck` 매개 변수는 운송 모드를 *truck*으로 지정합니다. 지원되는 다른 운송 모드로는 *taxi*, *bus*, *van*, *motorcycle* 및 기본값인 *car*가 있습니다.
   - `vehicleWidth`, `vehicleHeight` 및 `vehicleLength` 매개 변수는 차량의 크기를 미터 단위로 지정하며, 운송 모드가 *truck*인 경우에만 고려됩니다.
   - `vehicleLoadType`은 화물 트럭을 위험한 것으로 분류하고, 일부 도로에서 제한됩니다. 마찬가지로 현재는 *truck* 모드에서만 고려합니다.

2. 다음 JavaScript 코드를 추가하고 Route Service를 사용하여 자동차의 경로를 가져옵니다.

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + LBSAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    이 코드 조각은 또 다른 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 생성하고, 들어오는 응답을 구문 분석하는 이벤트 처리기를 추가합니다. 응답이 성공하면 반환된 경로에 대한 좌표 배열을 만들고 맵의 `carRouteLayerName` 레이어에 추가합니다. 
    
    또한 이 코드 조각은 계정 키에 대한 Route Service에 쿼리를 보내고, 지정된 출발점 및 도착점에 대한 경로를 가져옵니다. 다른 매개 변수가 사용되지 않으므로 기본 여행 모드인 *car*가 반환됩니다. 

3. **MapTruckRoute.html** 파일을 로컬로 저장한 다음 선택한 웹 브라우저에서 열고 결과를 확인합니다. Location Based Services의 API를 사용해 성공적으로 연결한 경우 다음과 유사한 맵이 표시됩니다. 

    ![Azure Route Service를 사용하여 우선 순위가 지정된 경로](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    트럭 경로는 파란색, 자동차 경로는 보라색입니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Route Service 쿼리 구성
> * 여행 모드에 따라 우선 순위가 지정된 경로 렌더링

**개념** 및 **방법** 문서를 계속 진행하여 Azure Location Based Services SDK에 대해 자세히 알아봅니다. 
