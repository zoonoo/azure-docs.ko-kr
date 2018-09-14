---
title: Azure Maps로 방향 표시 | Microsoft Docs
description: Javascript 맵에서 두 위치 간의 방향을 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 80abd6db9888524aa6a66d9861d8dc2d05188e19
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781499"
---
# <a name="show-directions-from-a-to-b"></a>A에서 B로의 방향 표시 

이 문서에서는 경로 요청을 만들고 맵에 경로를 표시하는 방법을 설명합니다. 

## <a name="understand-the-code"></a>코드 이해

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/RBZbep/'>맵에서 A에서 B로의 방향 표시(서비스 모듈)</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록의 줄은 서비스 클라이언트를 인스턴스화합니다.

세 번째 코드 블록은 맵에서 [줄 문자열 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)을 초기화합니다.

네 번째 코드 블록은 경로의 시작점과 끝점을 나타내기 위해 맵에 핀을 만들고 추가합니다. 지침은 [지도에 핀 추가](map-add-pin.md)를 참조하세요.

다음 코드 블록은 map 클래스의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 함수를 사용하여 경로의 시작점과 끝점을 기반으로 맵의 경계 상자를 설정합니다.

여섯 번째 코드 블록은 경로 쿼리를 생성합니다.

마지막 코드 블록은 시작 및 대상 지점 간의 경로를 가져오도록 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 메서드를 통해 Azure Maps 라우팅 서비스를 쿼리합니다. 그런 다음, 응답이 [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) 메서드를 사용하여 GeoJSON 형식으로 구문 분석됩니다. 모든 선을 맵에 추가하여 경로를 렌더링합니다. 자세한 내용은 [맵에 선 추가](./map-add-shape.md#addALine)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [Line String Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요. 
* [맵에 트래픽 표시](./map-show-traffic.md)
* [맵 - 마우스 이벤트 상호 작용](./map-events.md)
