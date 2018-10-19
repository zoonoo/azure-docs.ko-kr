---
title: Azure Maps로 방향 표시 | Microsoft Docs
description: Javascript 맵에서 두 위치 간의 방향을 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729063"
---
# <a name="show-directions-from-a-to-b"></a>A에서 B로의 방향 표시

이 문서에서는 경로 요청을 만들고 맵에 경로를 표시하는 방법을 설명합니다.

그렇게 하려면 두 가지 방법이 있습니다. 첫 번째 방법은 서비스 모듈을 통해 [Azure Maps 경로 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 쿼리하는 것입니다. 두 번째 방법은 API에 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 하는 것입니다. 두 방법 모두 아래에 설명되어 있습니다.

## <a name="query-the-route-via-service-module"></a>서비스 모듈을 통해 경로 쿼리

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/RBZbep/'>맵에서 A에서 B로의 방향 표시(서비스 모듈)</a>를 참조하세요.
</iframe>

첫 번째 코드 블록은 Map 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록의 줄은 서비스 클라이언트를 인스턴스화합니다.

세 번째 코드 블록은 맵에서 [줄 문자열 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)을 초기화합니다.

네 번째 코드 블록은 경로의 시작점과 끝점을 나타내기 위해 맵에 핀을 만들고 추가합니다. [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) 사용 방법에 대한 지침은 [지도에 핀 추가](map-add-pin.md)를 참조하세요.

다음 코드 블록은 map 클래스의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 함수를 사용하여 경로의 시작점과 끝점을 기반으로 맵의 경계 상자를 설정합니다.

여섯 번째 코드 블록은 경로 쿼리를 생성합니다.

마지막 코드 블록은 시작 및 대상 지점 간의 경로를 가져오도록 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 메서드를 통해 Azure Maps 라우팅 서비스를 쿼리합니다. 그런 다음, 응답이 [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) 메서드를 사용하여 GeoJSON 형식으로 구문 분석됩니다. 모든 선을 맵에 추가하여 경로를 렌더링합니다. 자세한 내용은 [맵에 선 추가](./map-add-shape.md#addALine)를 참조하세요.

## <a name="query-the-route-via-xmlhttprequest"></a>XMLHttpRequest를 통해 경로 쿼리

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>A에서 B로의 방향 표시</a>를 참조하세요.
</iframe>

첫 번째 코드 블록은 Map 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 경로의 시작점과 끝점을 나타내기 위해 맵에 핀을 만들고 추가합니다. [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) 사용 방법에 대한 지침은 [지도에 핀 추가](map-add-pin.md)를 참조하세요.

세 번째 코드 블록은 Map 클래스의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 함수를 사용하여 경로의 시작점과 끝점을 기반으로 맵의 경계 상자를 설정합니다.

네 번째 코드 블록은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 [Azure Maps 경로 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)로 보냅니다.

코드의 마지막 블록은 수신 응답을 구문 분석합니다. 성공적인 응답을 위해 각 중간 지점에 대한 위도 및 경도 정보를 수집합니다. 각 중간 지점을 후속 중간 지점에 연결하여 선 배열을 만듭니다. 모든 선을 맵에 추가하여 경로를 렌더링합니다. 지침은 [맵에 선 추가](./map-add-shape.md#addALine)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [맵에 트래픽 표시](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [맵 - 마우스 이벤트 상호 작용](./map-events.md)
