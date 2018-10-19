---
title: Azure Maps를 사용하여 검색 결과 표시 | Microsoft Docs
description: Azure Maps로 검색 요청을 수행한 후 Javascrip 지도에 결과를 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 78ffa42bcf57b7163afc13b2550abdbae240ef00
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729241"
---
# <a name="show-search-results-on-the-map"></a>지도에 검색 결과 표시

이 문서에서는 관심 있는 위치를 검색하고 지도에 검색 결과를 표시하는 방법을 보여줍니다.

원하는 위치를 검색하는 방법은 두 가지가 있습니다. 한 방법은 서비스 모듈을 사용하여 검색 요청을 하는 것입니다. 다른 방법은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 통해 [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)에 검색 요청을 하는 것입니다. 두 방법 모두 아래에 설명되어 있습니다.

## <a name="make-a-search-request-via-service-module"></a>서비스 모듈을 통해 검색 요청

<iframe height='500' scrolling='no' title='지도에 검색 결과 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>지도에 검색 결과 표시</a>(서비스 모듈)를 참조하세요.
</iframe>

첫 번째 코드 블록은 맵 개체를 생성하고 클라이언트 서비스를 초기화합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)를 사용하여 관심 지점을 검색합니다. 유사 항목 검색 API는 모든 조합의 유사 항목 입력을 처리할 수 있습니다. 그러면 유사 항목 검색 서비스의 응답은 [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) 메서드를 사용하여 GeoJSON 형식으로 구문 분석됩니다. 그런 후에 지도 위에 관심 지점을 나타내기 위해 지도에 핀이 추가됩니다.

마지막 코드 블록은 Map의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) 속성을 사용하여 지도에 대한 카메라 범위를 조정합니다.

## <a name="make-a-search-request-via-xmlhttprequest"></a>XMLHttpRequest를 통해 검색 요청

<iframe height='500' scrolling='no' title='지도에 검색 결과 표시' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>지도에 검색 결과 표시</a>를 참조하세요.
</iframe>

첫 번째 코드 블록은 Map 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 지도에 검색 결과 계층을 추가합니다. 검색 결과 계층은 지도에 핀으로 검색 결과를 표시합니다. 핀은 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)를 사용하여 추가됩니다.

세 번째 코드 블록은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)로 보내 관심 지점을 검색합니다. 유사 항목 검색 API는 모든 조합의 유사 항목 입력을 처리할 수 있습니다.

마지막 코드 블록은 응답을 구문 분석하고 Map의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest)를 사용하여 지도에 대한 카메라 범위를 조정하여 결과 핀을 렌더링합니다.

## <a name="next-steps"></a>다음 단계

**유상 항목 검색**에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [좌표에서 정보 가져오기](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A에서 B로의 방향 표시](./map-route.md)
