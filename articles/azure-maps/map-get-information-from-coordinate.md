---
title: 맵에 좌표 관련 정보 표시 | Microsoft Azure Maps
description: 사용자가 좌표를 선택할 경우 맵에서 주소에 대한 정보를 표시하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: eaf67fac19ab1e4bef5ebb1e791b7f40ae46cbf2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282860"
---
# <a name="get-information-from-a-coordinate"></a>좌표에서 정보 가져오기

이 문서에서는 클릭한 팝업 위치의 주소를 표시하는 역방향 주소 검색을 수행하는 방법을 보여줍니다.

역방향 주소 검색을 수행하는 두 가지 방법이 있습니다. 한 가지 방법은 서비스 모듈을 통해 [Azure Maps 역방향 주소 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)를 쿼리하는 것입니다. 다른 방법은 [Fetch API](https://fetch.spec.whatwg.org/)를 사용하여 [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)를 요청하여 주소를 찾는 것입니다. 두 가지 방법 모두 아래에서 조사합니다.

## <a name="make-a-reverse-search-request-via-service-module"></a>서비스 모듈을 통해 역방향 검색 요청

<iframe height='500' scrolling='no' title='좌표에서 정보 가져오기(서비스 모듈)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>좌표에서 정보 가져오기(서비스 모듈)</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 블록은 맵 개체를 생성하고 액세스 토큰을 사용하도록 인증 메커니즘을 설정합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 액세스 토큰을 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 `TokenCredential`을 만듭니다. 그런 다음, `TokenCredential`을 `atlas.service.MapsURL.newPipeline()`에 전달하고 [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) 인스턴스를 만듭니다. `searchURL`은 Azure Maps [검색](https://docs.microsoft.com/rest/api/maps/search) 작업에 대한 URL을 나타냅니다.

세 번째 코드 블록은 마우스 커서의 스타일을 포인터로 업데이트하고 [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) 개체를 만듭니다. [맵에서 팝업 추가](./map-add-popup.md)에서 지침을 확인할 수 있습니다.

네 번째 코드 블록은 마우스 클릭 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)를 추가합니다. 트리거되면 클릭한 지점의 좌표를 사용하여 검색 쿼리를 만듭니다. 그런 다음, [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) 메서드를 사용하여 좌표 주소에 대해 [Get Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)를 쿼리합니다. 그런 다음, 응답에서 `geojson.getFeatures()` 메서드를 사용하여 GeoJSON 기능 컬렉션을 추출합니다.

다섯 번째 코드 블록은 클릭한 좌표 위치의 응답 주소를 표시하도록 HTML 팝업 콘텐츠를 설정합니다.

커서의 변경 내용, popup 개체 및 클릭 이벤트는 모두 맵의 [로드 이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)에 생성됩니다. 이 코드 구조는 좌표 정보를 검색하기 전에 맵이 완전히 로드되도록 합니다.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fetch API를 통해 역방향 검색 요청 만들기

페치를 사용하여 해당 위치에 대한 역방향 지오코드 요청을 수행하려면 맵을 클릭합니다.

<iframe height='500' scrolling='no' title='좌표에서 정보 가져오기' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>좌표에서 정보 가져오기</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 생성하고 액세스 토큰을 사용하도록 인증 메커니즘을 설정합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 포인터에 마우스 커서의 스타일을 업데이트합니다. [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) 개체를 인스턴스화합니다. [맵에서 팝업 추가](./map-add-popup.md)에서 지침을 확인할 수 있습니다.

세 번째 코드 블록은 마우스 클릭에 대한 이벤트 수신기를 추가합니다. 마우스 클릭 시 [Fetch API](https://fetch.spec.whatwg.org/)를 사용하여 [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)에서 클릭한 좌표 주소를 쿼리합니다. 성공적인 응답의 경우 클릭한 위치의 주소를 수집합니다. popup 클래스의 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 함수를 사용하여 팝업 콘텐츠 및 위치를 정의합니다.

커서의 변경 내용, popup 개체 및 클릭 이벤트는 모두 맵의 [로드 이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)에 생성됩니다. 이 코드 구조는 좌표 정보를 검색하기 전에 맵이 완전히 로드되도록 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [검색 서비스 사용 모범 사례](how-to-use-best-practices-for-search.md)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [팝업](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [A에서 B로의 방향 표시](./map-route.md)

> [!div class="nextstepaction"]
> [트래픽 표시](./map-show-traffic.md)
