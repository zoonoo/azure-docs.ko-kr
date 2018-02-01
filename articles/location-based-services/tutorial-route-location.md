---
title: "Azure Location Based Services를 사용해 경로 찾기 | Microsoft Docs"
description: "Azure Location Based Services를 사용해 관심 지점까지 라우팅"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7303347444952d9c09dc6c04eea5b962e18729b4
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Azure Location Based Services를 사용해 관심 지점까지 라우팅

이 자습서에서는 Azure Location Based Services 계정과 Route Service SDK를 사용하여 관심 지점까지의 경로를 찾는 방법을 보여줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 주소 좌표 가져오기
> * 관심 지점으로의 방향에 대한 Route Service 쿼리

## <a name="prerequisites"></a>필수 조건

계속 진행하기에 앞서 [Azure Location Based Services 계정 만들기](./tutorial-search-location.md#createaccount) 및 [계정에 대한 구독 키 가져오기](./tutorial-search-location.md#getkey)를 수행해야 합니다. [Azure Location Based Services를 사용하여 주변 관심 지점 검색](./tutorial-search-location.md) 자습서에서 설명된 대로 맵 컨트롤 및 Search Service API를 사용하는 방법을 확인할 수도 있습니다.


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>주소 좌표 가져오기

Location Based Services의 맵 컨트롤 API가 포함된 정적 HTML 페이지를 만들려면 다음 단계를 사용합니다. 

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapRoute.html**로 지정합니다. 
2. 다음 HTML 구성 요소를 파일에 추가합니다.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
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
    HTML 헤더가 Azure Location Based Services 라이브러리의 CSS 및 JavaScript 파일에 대한 리소스 위치를 포함하는 방법을 참고합니다. 또한 HTML 파일의 body에 있는 *스크립트* 세그먼트에는 Azure Location Based Services의 API에 액세스하는 인라인 JavaScript 코드가 포함됩니다.

3. 다음 JavaScript 코드를 HTML 파일의 *스크립트* 블록에 추가합니다. 스크립트의 Location Based Services 계정에서 기본 키를 사용합니다.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas Map**은 시각적 및 대화형 웹 맵에 대한 컨트롤을 제공하고 Azure 맵 컨트롤 API의 구성 요소입니다.

4. 다음 JavaScript 코드를 *스크립트* 블록에 추가합니다. 이렇게 하면 경로를 보여주기 위해 *linestrings*의 계층을 맵 컨트롤에 추가합니다.

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

5. 다음 JavaScript 코드를 추가하여 경로의 출발점과 종료점을 만듭니다.

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    이 코드는 경로의 출발점과 종료점을 나타낼 두 개의 [GeoJSON 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만듭니다. 종료점은 이전 자습서 [Azure Location Based Services를 사용하여 주변 관심 지점 검색](./tutorial-search-location.md)에서 검색한 *주유소* 한 곳의 위도/경도 조합입니다.

6. 다음 JavaScript 코드를 추가하여 맵에 출발점과 종료점에 대한 핀을 추가합니다.

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    API **map.setCameraBounds**는 시작점과 끝점의 좌표에 따라 맵 창을 조정합니다. API **map.addPins**는 점을 시각적 구성 요소로 맵 컨트롤에 추가합니다.

7. **MapRoute.html** 파일을 컴퓨터에 저장합니다. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>관심 지점으로의 방향에 대한 Route Service 쿼리

이 섹션에서는 Azure Location Based Services의 Route Service API를 사용하여 주어진 출발점에서 목적지까지 경로를 찾는 방법을 보여줍니다. Route Service는 실시간 교통 상황을 고려하여 두 지점 간에 가장 빠르거나, 가장 짧거나, 경제적인 경로를 계획할 수 있도록 API를 제공합니다. 또한 사용자는 Azure의 광범위한 교통 기록 데이터베이스를 사용해 어떤 날짜 및 시간에 대한 경로 기간을 예측하여 미래의 경로를 계획할 수 있습니다. 

1. 이전 섹션에서 만든 **MapRoute.html** 파일을 열고 다음 JavaScript 코드를 *스크립트* 블록에 추가하여 Route Service를 설명합니다.

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```
    이 코드 조각은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 생성하고, 들어오는 응답을 구문 분석하는 이벤트 처리기를 추가합니다. 성공적인 응답인 경우 반환된 첫 번째 경로의 직선 세그먼트에 대한 좌표의 배열을 생성합니다. 그런 다음 이 경로에 대한 이 좌표 집합을 맵의 *linestrings* 계층에 추가합니다.

2. 다음 코드를 *스크립트* 블록에 추가하여 XMLHttpRequest를 Azure Location Based Services의 Route Service로 보냅니다.

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    위의 요청은 사용자의 계정 키와 시작점과 끝점에 대한 좌표인 필수 매개 변수를 주어진 순서로 보여줍니다. 

3. **MapRoute.html** 파일을 로컬로 저장한 다음 선택한 웹 브라우저에서 열고 결과를 확인합니다. Location Based Services의 API를 사용해 성공적으로 연결한 경우 다음과 유사한 맵이 표시됩니다. 

    ![Azure 맵 컨트롤 및 Route Service](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 주소 좌표 가져오기
> * 관심 지점으로의 방향에 대한 Route Service 쿼리

교통 수단에 따라 관심 지점으로 가는 경로의 우선 순위를 지정하기 위해 Azure Location Based Services를 사용하는 방법에 대해 알아보려면 자습서 [Azure Location Based Services를 사용해 여러 교통 수단에 대한 경로 찾기](./tutorial-prioritized-routes.md)로 진행합니다. 
