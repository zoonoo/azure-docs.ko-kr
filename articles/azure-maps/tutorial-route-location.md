---
title: Azure Maps로 경로 찾기 | Microsoft Docs
description: Azure Maps를 사용하여 관심 지점으로 라우팅
author: walsehgal
ms.author: v-musehg
ms.date: 10/22/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b6ce89d141af434d4f40e9079b39e4d7eed114df
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420917"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Azure Maps를 사용하여 관심 지점으로 라우팅

이 자습서에서는 Azure Maps 계정과 Route Service SDK를 사용하여 관심 지점까지의 경로를 찾는 방법을 보여줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주소 좌표 설정
> * 관심 지점으로의 방향에 대한 Route Service 쿼리

## <a name="prerequisites"></a>필수 조건

계속 진행하기 전에 이전 자습서의 단계에 따라 [Azure Maps 계정 만들기](./tutorial-search-location.md#createaccount), [계정에 대한 구독 키 가져오기](./tutorial-search-location.md#getkey)를 수행합니다.

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>새 지도 만들기

다음 단계에서는 지도 컨트롤 API가 포함된 정적 HTML 페이지를 만드는 방법을 보여줍니다.

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapRoute.html**로 지정합니다.
2. 다음 HTML 구성 요소를 파일에 추가합니다.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
        
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>
        
        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
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
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    HTML 헤더는 Azure 맵 컨트롤 라이브러리에서 호스팅하는 CSS 및 JavaScript 리소스 파일을 포함합니다. 페이지의 본문의 `onload` 이벤트는 페이지 본문이 로드될 때 `GetMap` 함수를 호출합니다. 이 함수에는 Azure Maps API에 액세스하는 인라인 JavaScript 코드가 포함됩니다. 

3. 다음 JavaScript 코드를 `GetMap` 함수에 추가합니다. **\<Your Azure Maps Key\>** 문자열을 Maps 계정에서 복사한 기본 키로 바꿉니다.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    `atlas.Map`은 시각적 및 대화형 웹 맵에 대한 컨트롤을 제공하고 Azure 맵 컨트롤 API의 구성 요소입니다.

4. 파일을 저장하고 브라우저에서 엽니다. 이 시점에서 더 자세히 개발할 수 있는 기본 지도가 있습니다.

   ![기본 지도 보기](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>경로가 렌더링되는 방식 정의

이 자습서에서는 경로의 시작 및 끝에 대한 기호 아이콘과 경로에 대한 선을 사용하여 간단한 경로를 렌더링합니다.

1. GetMap 함수에서 맵을 초기화한 후, 다음 JavaScript 코드를 추가합니다.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    로드 이벤트는 맵에 추가되고, 맵 리소스가 완전히 로드될 때 실행됩니다. 맵 로드 이벤트 처리기에서, 경로 선과 시작 및 끝 지점을 저장하는 데이터 원본이 만들어집니다. 선 레이어를 만들어 데이터 원본에 연결하여 경로 선이 렌더링되는 방식을 정의합니다. 너비가 5픽셀이고 선 이음 및 끝 단면이 둥근 멋진 파란색 음영이 경로 선에 렌더링됩니다. 이 레이어만 GeoJSON LineString 데이터를 렌더링하도록 보장하기 위한 필터가 추가됩니다. 맵에 레이어를 추가하면 값이 `'labels'`인 두 번째 매개 변수가 전달됩니다. 이 매개 변수는 맵 레이블 아래의 이 레이어를 렌더링하도록 지정합니다. 이렇게 하면 경로 선이 도로 레이블을 가리지 않습니다. 기호 레이어가 생성되어 데이터 원본에 연결됩니다. 이 레이어는 시작 및 끝 지점이 렌더링되는 방식을 지정합니다. 이 예에서는 지점 개체의 속성에서 아이콘 이미지 및 텍스트 레이블 정보를 검색하는 식이 추가되었습니다. 
    
2. 이 자습서에서는 Microsoft를 시작점으로 설정하고, 시애틀의 주유소를 대상 지점으로 설정합니다. 맵 로드 이벤트 처리기에서 다음 코드를 추가합니다.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    이 코드는 경로의 출발점과 도착점을 나타낼 두 개의 [GeoJSON 지점 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만듭니다. `title` 및 `icon` 속성이 각 지점에 추가됩니다.
    
3. 다음으로, JavaScript 코드를 추가하여 맵에 출발점과 종료점에 대한 핀을 추가합니다.

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);
    
    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    시작 지점과 끝 지점이 데이터 원본에 추가됩니다. `atlas.data.BoundingBox.fromData` 함수를 사용하여 시작 및 끝 지점의 경계 상자가 계산됩니다. 이 경계 상자는 **map.setCamera** 함수를 사용하여 시작 및 끝 지점에 맵 카메라 보기를 설정하는 데 사용됩니다. 기호 아이콘의 픽셀 크기를 보정하기 위해 안쪽 여백이 추가됩니다.

3. **MapRoute.html** 파일을 저장하고, 브라우저를 새로 고칩니다. 이제 지도의 중심에 시애틀이 표시되며, 출발점을 표시하는 둥근 파란색 핀과 도착점을 표시하는 파란색 핀을 볼 수 있습니다.

   ![시작점과 끝점이 표시된 지도 보기](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>방향 가져오기

이 섹션에서는 Maps의 경로 서비스 API를 사용하여 주어진 출발점에서 목적지까지 경로를 찾는 방법을 보여줍니다. 경로 서비스는 두 위치 간의 *최소 시간*, *최단 거리*, *최적* 또는 *모험* 경로를 계획할 수 있는 API를 제공합니다. 또한 사용자는 Azure의 광범위한 교통 기록 데이터베이스를 사용해 어떤 날짜 및 시간에 대한 경로 기간을 예측하여 미래의 경로를 계획할 수 있습니다. 자세한 내용은 [경로 방향 가져오기](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 참조하세요. 다음 기능은 모두 맵 로드 후에 지도가 완전히 로드되도록 **map load eventListener 내**에 추가해야 합니다.

1. 맵 로드 이벤트 처리기에서 다음 Javascript 코드를 추가하여 서비스 클라이언트를 인스턴스화합니다.

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```

2. 다음 블록의 코드를 추가하여 경로 쿼리 문자열을 생성합니다.
    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. 경로를 가져오려면 다음 블록의 코드를 스크립트에 추가합니다. [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 메서드를 통해 Azure Maps 라우팅 서비스를 쿼리한 다음, [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)를 사용하여 GeoJSON 형식으로 응답을 구문 분석합니다. 그런 다음, 데이터 원본에 대한 응답으로 맵에 자동으로 렌더링하는 경로 선을 추가합니다.

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
    });
    ```

5. **MapRoute.html** 파일을 저장하고, 웹 브라우저를 새로 고칩니다. Maps API와 성공적으로 연결되면 다음과 비슷한 지도가 표시됩니다.

    ![Azure 맵 컨트롤 및 Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주소 좌표 설정
> * 관심 지점으로의 방향에 대한 경로 서비스 쿼리

이 자습서에서 사용할 코드 샘플에 액세스하려면 다음을 참조하세요.

> [Azure Maps로 경로 찾기](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[여기서 실제 샘플 살펴보기](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

다음 자습서에는 이동 모드 또는 화물 유형과 같은 제한을 적용하여 경로 쿼리를 만든 다음, 같은 지도에 여러 경로를 표시하는 방법을 보여줍니다.

> [!div class="nextstepaction"]
> [여러 이동 모드에 대한 경로 찾기](./tutorial-prioritized-routes.md)
