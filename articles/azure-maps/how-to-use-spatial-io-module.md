---
title: Azure Maps 공간 IO 모듈을 사용 하는 방법 | Microsoft Azure 맵
description: Azure Maps 웹 SDK에서 제공 하는 공간 IO 모듈을 사용 하는 방법에 대해 알아봅니다. 이 모듈은 개발자가 공간 데이터를 Azure Maps 웹 sdk와 쉽게 통합할 수 있도록 강력한 기능을 제공 합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 61ea102d0e020f7890da1ae86cdfbb5c3db8f51b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335299"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Azure Maps 공간 IO 모듈을 사용 하는 방법

Azure Maps 웹 SDK는 공간 **IO 모듈**을 제공 합니다 .이 모듈은 공간 데이터를 JavaScript 또는 TypeScript를 사용 하 여 AZURE MAPS 웹 SDK와 통합 합니다. 이 모듈의 강력한 기능을 통해 개발자는 다음을 수행할 수 있습니다.

- [일반적인 공간 데이터 파일을 읽고 씁니다](spatial-io-read-write-spatial-data.md). 지원 되는 파일 형식에는 공간 정보가 있는 열을 포함 하는 KML, KMZ, .GPX), GeoRSS, GML, GeoJSON 및 CSV 파일이 포함 됩니다. 에서는 WKT (잘 알려진 텍스트)도 지원 합니다.
- [Open Geospatial Consortium (OGC) 서비스에 연결 하 고 Azure Maps 웹 SDK와 통합 합니다. WMS (웹 맵 서비스)와 WMTS (웹 맵 타일 서비스)를 맵의 계층으로 설정](spatial-io-add-ogc-map-layer.md)합니다.
- [웹 기능 서비스 (워크플로)에서 데이터를 쿼리](spatial-io-connect-wfs-service.md)합니다.
- [스타일 정보를 포함 하 고 최소 코드를 사용 하 여 자동으로 렌더링 하는 복잡 한 데이터 집합을 오버레이](spatial-io-add-simple-data-layer.md)합니다.
- 고속 [XML 및 구분 된 파일 판독기와 작성기 클래스를 활용](spatial-io-core-operations.md)합니다.

이 가이드에서는 웹 응용 프로그램에서 공간 IO 모듈을 통합 하 고 사용 하는 방법에 대해 알아봅니다.

이 비디오는 Azure Maps Web SDK의 공간 IO 모듈에 대 한 개요를 제공 합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="공간 데이터를 Azure Maps에 쉽게 통합-Microsoft Channel 9 비디오"></iframe>


> [!WARNING]
> 신뢰 하는 원본에서 가져온 데이터 및 서비스만 사용 합니다. 특히 다른 도메인에서 참조 하는 경우에만 사용 합니다. 공간 IO 모듈은 위험을 최소화 하는 단계를 수행 하지만, 가장 안전한 방법은 응용 프로그램에 danagerous 데이터를 사용할 수 없는 것입니다. 

## <a name="prerequisites"></a>사전 요구 사항

공간 IO 모듈을 사용 하려면 먼저 [Azure Maps 계정을](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-azure-maps-account) 만들고 [계정에 대 한 기본 구독 키를 가져와야](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)합니다.

## <a name="installing-the-spatial-io-module"></a>공간 IO 모듈 설치

다음 두 옵션 중 하나를 사용 하 여 Azure Maps 공간 IO 모듈을 로드할 수 있습니다.

* Azure Maps 공간 IO 모듈에 대해 전역적으로 호스트 된 Azure CDN입니다. 이 옵션의 경우 HTML 파일의 요소에 JavaScript에 대 한 참조를 추가 `<head>` 합니다.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* [Azure maps-공간-io](https://www.npmjs.com/package/azure-maps-spatial-io) 에 대 한 소스 코드를 로컬로 로드 한 다음 앱을 사용 하 여 호스트할 수 있습니다. 이 패키지에는 TypeScript 정의도 포함됩니다. 이 옵션의 경우 다음 명령을 사용 하 여 패키지를 설치 합니다.

    ```sh
    npm install azure-maps-spatial-io
    ```

    그런 다음 HTML 문서의 요소에 JavaScript에 대 한 참조를 추가 합니다 `<head>` .

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>공간 IO 모듈 사용

1. 새 HTML 파일 만들기

2. Azure Maps 웹 SDK를 로드 하 고 지도 컨트롤을 초기화 합니다. 자세한 내용은 [Azure Maps 지도 컨트롤](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) 가이드를 참조 하세요. 이 단계를 완료 하 고 나면 HTML 파일이 다음과 같이 표시 됩니다.

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Azure Maps 공간 IO 모듈을 로드 합니다. 이 연습에서는 Azure Maps 공간 IO 모듈에 대해 CDN을 사용 합니다. HTML 파일의 요소에 아래 참조를 추가 합니다 `<head>` .

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. 를 초기화 하 `datasource` 고 지도에 데이터 소스를 추가 합니다. 를 초기화 하 `layer` 고 지도 계층에 데이터 소스를 추가 합니다. 그런 다음 데이터 원본 및 계층을 모두 렌더링 합니다. 다음 단계에서 전체 코드를 볼 수 있도록 아래로 스크롤하면 먼저 데이터 소스와 계층 코드 조각을 배치할 가장 좋은 위치를 생각해 야 합니다. 지도를 프로그래밍 방식으로 조작 하기 전에 지도 리소스가 준비 될 때까지 기다려야 합니다.

    ```javascript
    var datasource, layer;
    ```

    및

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. HTML 코드를 모두 함께 배치 하면 다음 코드와 같이 표시 됩니다. 이 샘플에서는 URL에서 XML 파일을 읽는 방법을 보여 줍니다. 그런 다음 파일의 기능 데이터를 로드 하 여 맵에 표시 합니다. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. 를 `<Your Azure Maps Key>` 기본 키로 바꾸어야 합니다. HTML 파일을 열면 다음 이미지와 유사한 결과가 표시 됩니다.

    <center>

    ![공간 데이터 예](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>다음 단계

여기서 설명 하는 기능은 공간 IO 모듈에서 사용할 수 있는 여러 기능 중 하나일 뿐입니다. 공간 IO 모듈에서 다른 기능을 사용 하는 방법에 대 한 자세한 내용은 아래 가이드를 참조 하세요.

> [!div class="nextstepaction"]
> [단순 데이터 레이어 추가](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [공간 데이터 읽기 및 쓰기](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC 지도 계층 추가](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 작업 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)

Azure Maps 공간 IO 설명서를 참조 하세요.

> [!div class="nextstepaction"]
> [Azure Maps 공간 IO 패키지](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
