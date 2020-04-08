---
title: Azure 지도 공간 IO 모듈 사용 방법 | 마이크로소프트 Azure 지도
description: Azure 지도 웹 SDK에서 제공하는 공간 IO 모듈을 사용하는 방법에 대해 알아봅니다. 이 모듈은 개발자가 Azure Maps 웹 sdk와 공간 데이터를 쉽게 통합할 수 있도록 하는 강력한 기능을 제공합니다.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1de9dd9721700418b1aa9ba661fc070db1dbedcc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804642"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Azure 지도 공간 IO 모듈을 사용 하는 방법

Azure Maps 웹 SDK는 자바스크립트 또는 TypeScript를 사용하여 공간 데이터를 Azure Maps 웹 SDK와 통합하는 **공간 IO 모듈을**제공합니다. 이 모듈의 강력한 기능을 통해 개발자는 다음을 수행할 수 있습니다.

- [일반적인 공간 데이터 파일을 읽고 씁니다.](spatial-io-read-write-spatial-data.md) 지원되는 파일 형식에는 KML, KMZ, GPX, GeoRSS, GML, GeoJSON 및 공간 정보가 포함된 열이 포함된 CSV 파일이 포함됩니다. 또한 잘 알려진 텍스트(WKT)를 지원합니다.
- [OGC(개방형 지리 공간 컨소시엄) 서비스에 연결하고 Azure Maps 웹 SDK와 통합합니다. 오버레이 웹 맵 서비스(WMS) 및 웹 맵 타일 서비스(WMTS)를 맵의 레이어로](spatial-io-add-ogc-map-layer.md)사용합니다.
- [웹 기능 서비스(WFS)의 쿼리 데이터입니다.](spatial-io-connect-wfs-service.md)
- [스타일 정보를 포함하고 최소한의 코드를 사용하여 자동으로 렌더링되도록 하는 복잡한 데이터 집합을 오버레이합니다.](spatial-io-add-simple-data-layer.md)
- [고속 XML 및 구분된 파일 판독기 및 기록기 클래스를 활용합니다.](spatial-io-core-operations.md)

이 가이드에서는 웹 응용 프로그램에서 Spatial IO 모듈을 통합하고 사용하는 방법을 알아봅니다.

이 비디오에서는 Azure Maps Web SDK의 공간 IO 모듈에 대한 개요를 제공합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="쉽게 Azure지도에 공간 데이터를 통합 - 마이크로 소프트 채널 9 비디오"></iframe>


> [!WARNING]
> 특히 다른 도메인에서 참조하는 경우 신뢰할 수 있는 원본의 데이터 및 서비스만 사용합니다. 공간 IO 모듈은 위험을 최소화하기 위한 조치를 취하지만 가장 안전한 방법은 응용 프로그램에 대한 danagerous 데이터를 시작하기가 너무 허용되지 않습니다. 

## <a name="prerequisites"></a>사전 요구 사항

공간 IO 모듈을 사용하려면 Azure Maps [계정을 만들고](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) 계정에 [대한 기본 구독 키를 가져와야 합니다.](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)

## <a name="installing-the-spatial-io-module"></a>공간 IO 모듈 설치

다음 두 옵션 중 하나를 사용하여 Azure Maps 공간 IO 모듈을 로드할 수 있습니다.

* Azure Maps 공간 IO 모듈에 대해 전 세계적으로 호스팅되는 Azure CDN입니다. 이 옵션의 경우 HTML 파일의 요소에 `<head>` JavaScript에 대한 참조를 추가합니다.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* [azure-maps-공간 io에](https://www.npmjs.com/package/azure-maps-spatial-io) 대 한 소스 코드를 로컬로 로드 하 고 다음 응용 프로그램으로 호스트 수 있습니다. 이 패키지에는 TypeScript 정의도 포함됩니다. 이 옵션의 경우 다음 명령을 사용하여 패키지를 설치합니다.

    ```sh
    npm install azure-maps-spatial-io
    ```

    그런 다음 HTML 문서의 요소에 `<head>` JavaScript에 대한 참조를 추가합니다.

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>공간 IO 모듈 사용

1. 새 HTML 파일 만들기

2. Azure 지도 웹 SDK를 로드하고 맵 컨트롤을 초기화합니다. 자세한 내용은 [Azure 지도 맵 제어](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) 가이드를 참조하십시오. 이 단계를 완료하면 HTML 파일은 다음과 같아야 합니다.

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

2. Azure 지도 공간 IO 모듈을 로드합니다. 이 연습에서는 Azure Maps 공간 IO 모듈에 CDN을 사용합니다. 아래 참조를 HTML `<head>` 파일의 요소에 추가합니다.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. `datasource`을 초기화하고 데이터 원본을 맵에 추가합니다. `layer`을 초기화하고 데이터 원본을 맵 레이어에 추가합니다. 그런 다음 데이터 원본과 레이어를 모두 렌더링합니다. 아래로 스크롤하여 다음 단계에서 전체 코드를 보려면 데이터 원본 및 레이어 코드 조각을 넣을 수 있는 최적의 위치에 대해 생각해 보십시오. 맵을 프로그래밍 방식으로 조작하기 전에 맵 리소스가 준비될 때까지 기다려야 합니다.

    ```javascript
    var datasource, layer;
    ```

    and

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. 이 모든 것을 종합하면 HTML 코드는 다음 코드와 같아야 합니다. 이 샘플에서는 URL에서 XML 파일을 읽는 방법을 보여 줍니다. 그런 다음 맵에 파일의 피처 데이터를 로드하고 표시합니다. 

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

5. 기본 키로 교체해야 `<Your Azure Maps Key>` 합니다. HTML 파일을 열면 다음 이미지와 유사한 결과가 표시됩니다.

    <center>

    ![공간 데이터 예제](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>다음 단계

여기에서 설명한 기능은 공간 IO 모듈에서 사용할 수 있는 많은 기능 중 하나일 뿐입니다. 공간 IO 모듈에서 다른 기능을 사용하는 방법을 알아보려면 아래 가이드를 읽으십시오.

> [!div class="nextstepaction"]
> [간단한 데이터 계층 추가](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [공간 데이터 읽기 및 쓰기](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC 맵 레이어 추가](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 운영 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)

Azure 지도 공간 IO 설명서를 참조하십시오.

> [!div class="nextstepaction"]
> [Azure 지도 공간 IO 패키지](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
