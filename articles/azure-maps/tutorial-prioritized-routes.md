---
title: '자습서: Microsoft Azure Maps를 사용하여 특정 이동 모드에 대한 경로 찾기 및 표시'
description: Azure Maps를 사용하여 특정 이동 모드에 대한 경로를 찾고 표시하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: a5b8dba920db0227c400e62ef7ddaf718d27c78a
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085062"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>자습서: Azure Maps를 사용하여 다양한 이동 모드에 대한 경로 찾기 및 표시

이 자습서에서는 Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route) 및 [지도 컨트롤](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)을 사용하여 개인 차량과 `USHazmatClass2` 화물 유형의 상용 차량(트럭) 모두에 대한 경로 방향을 표시하는 방법을 보여 줍니다. 또한 지도에서 실시간 교통 데이터를 시각화하는 방법도 안내합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 웹 페이지에서 지도 컨트롤 만들기 및 표시
> * 지도에서 실시간 교통 데이터 렌더링
> * 지도에서 개인 및 상용 차량 경로 요청 및 표시

## <a name="prerequisites"></a>필수 조건

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).

3. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

[여기](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)에서 샘플의 전체 소스 코드를 가져올 수 있습니다. 라이브 샘플은 [여기](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)에서 확인할 수 있습니다.

## <a name="create-a-new-web-page-using-the-map-control-api"></a>지도 컨트롤 API를 사용하여 새 웹 페이지 만들기

다음 단계에서는 웹 페이지에서 지도 컨트롤을 만들고 표시하는 방법을 보여 줍니다.

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapTruckRoute.html**로 지정합니다.
2. 다음 HTML 태그를 복사하여 파일에 붙여넣습니다.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

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

            #myMap {
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

     HTML 헤더에는 Azure 맵 컨트롤 라이브러리에서 호스팅하는 CSS 및 JavaScript 리소스 파일이 포함됩니다. 본문의 `onload` 이벤트는 `GetMap` 함수를 호출합니다. 다음 단계에서는 지도 컨트롤 초기화 코드를 추가하겠습니다.

3. 다음 JavaScript 코드를 `GetMap` 함수에 추가합니다. 문자열 `<Your Azure Maps Key>`를 Maps 계정에서 복사한 기본 키로 바꿉니다.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

4. 파일을 저장하고 브라우저에서 엽니다. 간단하게 표시됩니다.

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="지도 컨트롤의 기본 지도 렌더링":::

## <a name="render-real-time-traffic-data-on-a-map"></a>지도에서 실시간 교통 데이터 렌더링

1. 다음 JavaScript 코드를 `GetMap` 함수에 추가합니다. 이 코드는 지도 컨트롤의 `ready` 이벤트 처리기를 구현합니다. 이 자습서의 나머지 코드는 `ready` 이벤트 처리기 내에 배치됩니다.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    지도 컨트롤의 `ready` 이벤트 처리기에서 지도의 교통 흐름 설정은 자유 흐름을 기준으로 하는 도로의 속도인 `relative`로 설정됩니다. 자세한 교통 옵션은 [TrafficOptions 인터페이스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions?view=azure-maps-typescript-latest&preserve-view=false)를 참조하세요.

2. **MapTruckRoute.html** 파일을 저장하고, 브라우저에서 페이지를 새로 고칩니다. 로스앤젤레스와 같은 도시를 확대하면 현재 교통 데이터와 함께 거리가 표시됩니다.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="지도 컨트롤의 기본 지도 렌더링":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>경로 표시 렌더링 정의

이 자습서에서는 두 개의 경로를 계산하여 지도에 렌더링합니다. 첫 번째 경로는 개인 차량(자동차)에 대해 계산됩니다. 두 번째 경로는 결과 간의 차이를 보여 주기 위해 상용 차량(트럭)에 대해 계산됩니다. 렌더링되면 지도에서 경로의 출발 지점 및 도착 지점에 대한 기호 아이콘과 각 라우팅 경로에 대한 서로 다른 색의 경로 선 기하 도형을 표시합니다. 선 계층을 추가하는 방법에 대한 자세한 내용은 [지도에 선 계층 추가](map-add-line-layer.md)를 참조하세요. 기호 계층에 대한 자세한 내용은 [지도에 기호 계층 추가](map-add-pin.md)를 참조하세요.

1. 지도 컨트롤의 `ready` 이벤트 처리기에서 다음 코드를 추가합니다.

    ```JavaScript

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a layer for rendering the route lines and have it render under the map labels.
    map.layers.add(new atlas.layer.LineLayer(datasource, null, {
        strokeColor: ['get', 'strokeColor'],
        strokeWidth: ['get', 'strokeWidth'],
        lineJoin: 'round',
        lineCap: 'round'
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
        filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
    }));

    ```

    지도 컨트롤의 `ready` 이벤트 처리기에서 처음부터 끝까지의 경로를 저장하기 위해 데이터 원본이 만들어집니다. [식](data-driven-style-expressions-web-sdk.md)은 경로 선 기능의 속성에서 선 두께 및 색을 검색하는 데 사용됩니다. 경로 선이 도로 레이블을 포함하지 않도록 하기 위해 두 번째 매개 변수를 `'labels'` 값으로 전달했습니다.

    다음으로, 기호 계층이 생성되어 데이터 원본에 연결됩니다. 이 레이어는 출발 지점 및 도착 지점이 렌더링되는 방법을 지정합니다. 각 지점 개체의 속성에서 아이콘 이미지 및 텍스트 레이블 정보를 검색하는 식이 추가되었습니다. 식에 대한 자세한 내용은 [데이터 기반 스타일 식](data-driven-style-expressions-web-sdk.md)을 참조하세요.

2. 출발 지점을 시애틀 소재의 Fabrikam이라는 가상 회사로 설정하고, 도착 지점을 Microsoft 본사로 설정합니다.  지도 컨트롤의 `ready` 이벤트 처리기에서 다음 코드를 추가합니다.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    이 코드는 데이터 원본에 추가되는 출발점과 도착점을 나타낼 두 개의 [GeoJSON 지점 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만듭니다.

    마지막 코드 블록은 출발점과 도착점의 위도 및 경도를 사용하여 카메라 보기를 설정합니다. 시작 지점과 끝 지점이 데이터 원본에 추가됩니다. `atlas.data.BoundingBox.fromData` 함수를 사용하여 시작 및 끝 지점의 경계 상자가 계산됩니다. 이 경계 상자는 `map.setCamera` 함수를 사용하여 전체 경로에 대해 맵 카메라 보기를 설정하는 데 사용됩니다. 기호 아이콘의 픽셀 크기를 보정하기 위해 안쪽 여백이 추가됩니다. 지도 컨트롤의 setCamera 속성에 대한 자세한 내용은 [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false)를 참조하세요.

3. **TruckRoute.html**을 저장하고, 브라우저를 새로 고칩니다. 이제 지도 중심이 시애틀로 이동됩니다. 물방울 모양 파란색 핀은 시작점을 표시합니다. 둥근 파란색 핀은 도착점을 표시합니다.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="지도 컨트롤의 기본 지도 렌더링":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>지도에서 개인 및 상용 차량 경로 요청 및 표시

이 섹션에서는 사용자의 운송 방식에 따라 Azure Maps Route Service를 사용하여 한 지점에서 다른 지점으로의 방향을 가져오는 방법을 보여 줍니다. 트럭과 자동차의 두 가지 운송 모드를 사용합니다.

>[!TIP]
>Route Service는 거리, 교통 조건 및 사용되는 운송 모드를 기준으로 하여 *최소 시간*, *최단 거리*, *최적* 또는 *모험* 경로를 계획할 수 있는 API를 제공합니다. 이 서비스를 통해 사용자는 과거 교통 조건을 기준으로 하여 향후 경로를 계획할 수도 있습니다. 사용자는 지정된 시간 동안 경로 기간의 예측을 확인할 수 있습니다. 자세한 내용은 [경로 방향 API 가져오기](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 참조하세요.

1. `GetMap` 함수에서 컨트롤의 `ready` 이벤트 처리기 내에 다음을 JavaScript 코드에 추가합니다.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`은 구독 키를 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 `SubscriptionKeyCredentialPolicy`를 만듭니다. `atlas.service.MapsURL.newPipeline()`은 `SubscriptionKeyCredential` 정책을 인식하고 [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline) 인스턴스를 만듭니다. `routeURL`은 Azure Maps [경로](https://docs.microsoft.com/rest/api/maps/route) 작업에 대한 URL을 나타냅니다.

2. 자격 증명 및 URL이 설정되면 출발 지점에서 도착 지점까지의 트럭 경로를 생성하는 다음 JavaScript 코드를 추가합니다. 이 경로는 `USHazmatClass2` 분류 화물을 운송하는 트럭에 대해 만들어지고 표시됩니다.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    위의 코드는 [Azure Maps 경로 방향 API](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-)를 통해 Azure Maps Route Service를 쿼리합니다. 그런 후 `geojson.getFeatures()` 메서드를 사용하여 추출한 응답의 GeoJSON 기능 컬렉션에서 경로 선이 추출됩니다. 마지막으로, 경로 선이 데이터 원본에 추가됩니다. 트럭 경로 계산이 자동차 경로 계산보다 느린 경우가 많으므로 트럭 경로가 데이터 원본의 다른 선보다 먼저 렌더링되도록 0의 인덱스에 추가됩니다. 승용차 경로 다음에 트럭 경로 선이 데이터 원본에 추가되면 그 위에 렌더링됩니다. 파란색의 스트로크 색과 9픽셀의 스트로크 너비의 두 가지 속성이 트럭 경로 선에 추가됩니다.

    >[!TIP]
    > Azure Maps 경로 방향 API에 대해 가능한 모든 옵션과 값을 알아보려면 [경로 방향 게시에 대한 URI 매개 변수](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#uri-parameters)를 참조하세요.

3. 이제 자동차 경로를 생성하는 다음 JavaScript 코드를 추가합니다.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    위의 코드는 [Azure Maps 경로 방향 API](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) 메서드를 통해 Azure Maps Route Service를 쿼리합니다. 그런 후 `geojson.getFeatures()` 메서드를 사용하여 추출한 응답의 GeoJSON 기능 컬렉션에서 경로 선이 추출됩니다. 마지막으로, 경로 선이 데이터 원본에 추가됩니다. 자주색의 스트로크 색과 5픽셀의 스트로크 너비의 두 가지 속성이 트럭 경로 선에 추가됩니다.

4. **TruckRoute.html** 파일을 저장하고, 웹 브라우저를 새로 고칩니다. 이제 지도에 트럭 경로와 자동차 경로가 표시됩니다.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="지도 컨트롤의 기본 지도 렌더링":::

    트럭 경로는 굵은 파란색 선으로 표시됩니다. 자동차 경로는 가는 자주색 선으로 표시됩니다. 자동차 경로는 I-90을 통해 워싱턴 호수를 가로질러 주거 지역 아래의 터널을 통과합니다. 터널은 주거 지역에 가깝기 때문에 위험한 폐기 화물은 제한됩니다. `USHazmatClass2` 화물 유형이 지정된 트럭 경로는 다른 고속 도로를 사용하도록 안내됩니다.

    [여기](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)에서 샘플의 전체 소스 코드를 가져올 수 있습니다. 라이브 샘플은 [여기](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 자습서에서는 Azure Maps를 사용하여 간단한 저장소 로케이터를 만드는 과정을 보여 줍니다.

> [!div class="nextstepaction"]
> [Azure Maps를 사용하여 저장소 로케이터 만들기](./tutorial-create-store-locator.md)
