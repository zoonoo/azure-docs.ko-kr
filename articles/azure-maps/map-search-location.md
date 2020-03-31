---
title: 지도에 검색 결과 표시 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 검색 요청을 수행하고 결과를 맵에 표시하는 방법을 알아봅니다.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371390"
---
# <a name="show-search-results-on-the-map"></a>지도에 검색 결과 표시

이 문서에서는 관심 있는 위치를 검색하고 지도에 검색 결과를 표시하는 방법을 보여줍니다.

원하는 위치를 검색하는 방법은 두 가지가 있습니다. 한 방법은 서비스 모듈을 사용하여 검색 요청을 하는 것입니다. 다른 방법은 [가져오기 API를](https://fetch.spec.whatwg.org/)통해 [Azure Maps 퍼지 검색 API에](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 대한 검색 요청을 하는 것입니다. 두 방법 모두 아래에 설명되어 있습니다.

## <a name="make-a-search-request-via-service-module"></a>서비스 모듈을 통해 검색 요청

<iframe height='500' scrolling='no' title='지도에 검색 결과 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>지도에 검색 결과 표시</a>(서비스 모듈)를 참조하세요.
</iframe>

위의 코드에서 첫 번째 블록은 맵 개체를 구성하고 액세스 토큰을 사용하도록 인증 메커니즘을 설정합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 액세스 토큰을 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 A를 `TokenCredential` 만듭니다. 그런 다음 `TokenCredential` 에 `atlas.service.MapsURL.newPipeline()` 전달 하 고 [파이프라인](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) 인스턴스를 만듭니다. `searchURL`은 Azure Maps [검색](https://docs.microsoft.com/rest/api/maps/search) 작업에 대한 URL을 나타냅니다.

세 번째 코드 블록에서는[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 데이터 원본 개체를 만들고 검색 결과를 추가합니다. [기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다.  그런 다음 기호 레이어가 작성됩니다. 데이터 원본이 기호 계층에 추가된 다음, 맵에 추가됩니다.

네 번째 코드 블록은 [서비스 모듈에서](how-to-use-services-module.md) [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) 메서드를 사용합니다. 그것은 당신이 관심지점을 검색하기 위해 [검색 퍼지 나머지 API를 얻기를](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 통해 자유 양식 텍스트 검색을 수행 할 수 있습니다. Search 퍼지 API에 대한 요청을 받으면 퍼지 입력의 모든 조합을 처리할 수 있습니다. 그런 다음, `geojson.getFeatures()` 메서드를 사용하여 응답의 GeoJSON 기능 컬렉션을 추출하고 데이터 원본에 추가하면, 기호 레이어를 통해 데이터가 맵에 자동으로 렌더링됩니다.

마지막 코드 블록은 맵의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용하여 맵의 카메라 경계를 조정합니다.

검색 요청, 데이터 원본, 기호 레이어 및 카메라 경계는 맵의 [이벤트 리스너](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 안에 있습니다. 맵이 완전히 로드된 후에 결과가 표시되도록 하려고 합니다.


## <a name="make-a-search-request-via-fetch-api"></a>가져오기 API를 통해 검색 요청

<iframe height='500' scrolling='no' title='지도에 검색 결과 표시' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>지도에 검색 결과 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 액세스 토큰을 사용하는 인증 메커니즘을 설정합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 검색 요청을 할 수 있는 URL을 만듭니다. 또한 검색 결과에 대한 경계와 핀을 저장하는 두 개의 배열을 만듭니다.

세 번째 코드 블록은 [Fetch API를](https://fetch.spec.whatwg.org/)사용합니다. [Fetch API는](https://fetch.spec.whatwg.org/) [Azure Maps 퍼지 검색 API에](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 요청하여 관심 지점을 검색하는 데 사용됩니다. 유사 항목 검색 API는 모든 조합의 유사 항목 입력을 처리할 수 있습니다. 그런 다음 검색 응답을 처리하고 구문 분석하고 검색 Pins 배열에 결과 핀을 추가합니다.

네 번째 코드 블록은 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 데이터 원본 개체를 만듭니다. 코드에서 소스 개체에 검색 결과를 추가합니다. [기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다. 그런 다음 기호 레이어가 작성됩니다. 데이터 원본이 기호 계층에 추가된 다음, 맵에 추가됩니다.

코드의 마지막 블록은 [BoundingBox 개체를](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) 만듭니다. 결과 배열을 사용 하 고 지도의 [setCamera를](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)사용 하 여 지도에 대 한 카메라 경계를 조정 합니다. 그런 다음 결과 핀을 렌더링합니다.

검색 요청, 데이터 원본, 기호 레이어 및 카메라 경계는 맵의 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 내에 설정되어 맵이 완전히 로드된 후 결과가 표시되도록 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [검색 서비스 사용 모범 사례](how-to-use-best-practices-for-search.md)

**유상 항목 검색**에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [지도](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [좌표에서 정보 가져오기](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [A에서 B로의 방향 표시](map-route.md)
