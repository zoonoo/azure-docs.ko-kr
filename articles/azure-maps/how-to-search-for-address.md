---
title: Azure Maps Search services를 사용 하 여 위치 검색
description: Azure Maps Search 서비스에 대해 알아봅니다. 지 오 코딩, 역방향 지 오 코딩, 유사 항목 검색 및 역방향 교차 번 검색에 대해이 Api 집합을 사용 하는 방법을 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/21/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9628ecada2e427f6220ae2a5154cebb8e4958bd0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895701"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Azure Maps Search services를 사용 하 여 위치 검색

[Azure Maps Search Service](/rest/api/maps/search) 는 개발자가 이름, 범주 및 기타 지리적 정보를 기준으로 주소, 위치 및 비즈니스 목록을 검색 하는 데 도움이 되도록 설계 된 RESTful api 집합입니다. 서비스는 기존 지 오 코딩 지원 외에도 위도 및 경도를 기반으로 하는 역방향 geocode 주소 및 교차 거리를 설정할 수 있습니다. 검색에서 반환 된 위도 및 경도 값은 [경로](/rest/api/maps/route) 및 [날씨](/rest/api/maps/weather) 서비스와 같은 다른 Azure Maps 서비스에서 매개 변수로 사용할 수 있습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* [검색 주소 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)를 사용 하 여 주소 (geocode 주소 위치)에 대 한 위도 및 경도 좌표를 요청 합니다.
* [유사 항목 검색 API](/rest/api/maps/search/getsearchfuzzy)를 사용 하 여 주소나 관심 지점 (poi)을 검색 합니다.
* [역 주소 검색](/rest/api/maps/search/getsearchaddressreverse) 을 수행 하 여 좌표 위치를 주소로 변환 합니다.
* [검색 주소 역방향 교차 주소 API](/rest/api/maps/search/getsearchaddressreversecrossstreet)를 사용 하 여 좌표 위치를 인간 이해 하기 쉬운 교차 주소로 변환 합니다.  일반적으로 장치 또는 자산에서 GPS 피드를 수신 하는 응용 프로그램을 추적 하 고 좌표가 위치한 위치를 확인 하는 데 필요 합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>주소에 대 한 위도 및 경도 요청 (지 오 코딩)

이 예제에서는 Azure Maps [검색 주소 API 가져오기](/rest/api/maps/search/getsearchaddress) 를 사용 하 여 주소를 위도 및 경도 좌표로 변환 합니다. 이 프로세스를 *지 오 코딩* 라고도 합니다. 응답은 좌표를 반환 하는 것 외에도 주소, 우편 번호, 지방 자치 체 및 국가/지역 정보를 반환 합니다.

>[!TIP]
>Geocode에 대 한 주소 집합이 있는 경우 [사후 검색 주소 일괄 처리 api](/rest/api/maps/search/postsearchaddressbatch) 를 사용 하 여 단일 API 호출로 쿼리 일괄 처리를 보낼 수 있습니다.

1. Postman 앱을 엽니다. Postman 앱의 위쪽 근처에서 **새로 만들기** 를 선택합니다. **새로 만들기** 창에서 **컬렉션** 을 선택합니다.  컬렉션 이름을 지정하고, **만들기** 단추를 선택합니다. 이 문서의 나머지 예제에는이 컬렉션을 사용 합니다.

2. 요청을 만들려면 **새로 만들기** 를 다시 선택합니다. **새로 만들기** 창에서 **요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 단계에서 만든 컬렉션을 선택한 다음, **저장** 을 선택합니다.

3. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청에서 특정 주소를 검색 하는 중 `400 Braod St, Seattle, WA 98109` 입니다.

    이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다. 요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. 파란색 **보내기** 단추를 클릭 합니다. 응답 본문에는 단일 위치에 대 한 데이터가 포함 됩니다.

5. 이제 두 개 이상의 가능한 위치가 포함 된 주소를 검색 합니다. **Params** 섹션에서 키를로 변경 `query` `400 Broad, Seattle` 합니다. 파란색 **보내기** 단추를 클릭 합니다.

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="주소 검색":::

6. 그런 다음 키를로 설정 해 봅니다 `query` `400 Broa` .

7. **보내기** 단추를 클릭합니다.  이제 응답에 여러 국가의 응답이 포함 되어 있음을 알 수 있습니다. 사용자에 대 한 관련 영역에 ias 결과를 추가 하려면 항상 가능한 한 많은 위치 세부 정보를 요청에 추가 합니다.

## <a name="using-fuzzy-search-api"></a>유사 항목 검색 API 사용

Azure Maps [유사 항목 검색 API](/rest/api/maps/search/getsearchfuzzy) 는 표준 단일 줄 및 자유 형식 검색을 지원 합니다. 검색 요청에 대 한 사용자 입력 유형을 알 수 없는 경우 Azure Maps 검색 유사 항목 API를 사용 하는 것이 좋습니다.  쿼리 입력은 전체 또는 부분 주소가 될 수 있습니다. POI, POI category 또는 brand 이름과 같은 POI (관심 지점) 토큰 일 수도 있습니다. 또한 검색 결과의 관련성을 향상 시키기 위해 쿼리 결과는 좌표 위치 및 반지름에 의해 제한 되거나 경계 상자를 정의 하 여 제한할 수 있습니다.

>[!TIP]
>대부분의 검색 쿼리는 성능을 얻고 비정상적인 결과를 줄이기 위해 기본적으로 maxFuzzyLevel = 1이 됩니다. `maxFuzzyLevel`또는 매개 변수를 사용 하 여 허용량 수준을 조정할 수 있습니다 `minFuzzyLevel` . `maxFuzzyLevel`및 모든 선택적 매개 변수의 전체 목록에 대 한 자세한 내용은 [유사 항목 검색 URI 매개 변수](/rest/api/maps/search/getsearchfuzzy#uri-parameters) 를 참조 하세요.

### <a name="search-for-an-address-using-fuzzy-search"></a>유사 항목 검색을 사용하여 주소 검색

이 예제에서는 유사 항목 검색을 사용 하 여 전체를 검색 `pizza` 합니다.  그런 다음 특정 국가의 범위를 검색 하는 방법을 보여 드리겠습니다. 마지막으로, 좌표 위치 및 반경을 사용 하 여 특정 영역에 대 한 검색 범위를 지정 하 고 반환 된 결과의 수를 제한 하는 방법을 보여 줍니다.

>[!IMPORTANT]
>사용자에 대 한 관련 영역에 ias 결과를 추가 하려면 항상 가능한 한 많은 위치 정보를 추가 합니다. 자세한 내용은 [검색 모범 사례](how-to-use-best-practices-for-search.md#geobiased-search-results)를 참조 하세요.

1. Postman 앱을 열고 **새로 만들기** 를 클릭 한 다음 **요청** 을 선택 합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 섹션에서 만든 컬렉션을 선택 하거나 새 컬렉션을 만든 다음 **저장** 을 선택 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다. 요청은 다음 URL과 같습니다.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >URL 경로의 _json_ 특성은 응답 형식을 결정합니다. 이 문서에서는 사용 편의성과 가독성을 위해 json을 사용 합니다. 다른 지원 되는 응답 형식을 찾으려면 `format` [URI 매개 변수 참조 설명서](/rest/api/maps/search/getsearchfuzzy#uri-parameters)에서 매개 변수 정의를 참조 하세요.

3. **보내기** 를 클릭하고 응답 본문을 검토합니다.

    "피자"의 모호한 쿼리 문자열은 "피자" 및 "식당" 범주에서 10 개의 [이율 결과](/rest/api/maps/search/getsearchpoi#searchpoiresponse) (poi)를 반환 했습니다. 각 결과에는 위치에 대 한 주소, 위도 및 경도 값, 뷰 포트, 진입점 등의 세부 정보가 포함 됩니다. 이제이 쿼리에 대해 결과가 달라 지 며 어떤 참조 위치에도 연결 되지 않습니다.
  
    다음 단계에서는 매개 변수를 사용 하 여 `countrySet` 응용 프로그램에 적용 해야 하는 국가/지역만 지정 합니다. 지원 되는 국가/지역에 대 한 전체 목록은 [검색 범위](./geocoding-coverage.md)를 참조 하세요.

4. 기본 동작은 전체 세계를 검색 하 여 불필요 한 결과를 반환 하는 것입니다. 다음으로, 미국 에서만 피자를 검색 합니다. `countrySet` **Params** 섹션에 키를 추가 하 고 해당 값을로 설정 `US` 합니다. 키를 `countrySet` 로 설정 하면 `US` 결과를 미국에 바인딩합니다.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="주소 검색":::

    이제 결과는 국가 코드로 경계가 구분되며 쿼리는 미국의 피자 음식점을 반환합니다.

5. 훨씬 더 많은 대상을 검색 하려면/lon. 범위를 검색할 수 있습니다. 좌표 쌍입니다. 이 예제에서는/lon.를 사용 합니다. 시애틀 공간 니 들의입니다. 400-미터 반경 내 에서만 결과를 반환 하려고 하기 때문에 `radius` 매개 변수를 추가 합니다. 또한 `limit` 매개 변수를 추가 하 여 가장 가까운 5 개의 피자 위치로 결과를 제한 합니다.

    **Params** 섹션에서 다음 키/값 쌍을 추가 합니다.

     | 키 | 값 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | radius | 400 |
    | limit | 5|

6. **보내기** 를 클릭합니다. 응답에는 시애틀 공간 니 들 근처의 피자 식당에 대 한 결과가 포함 됩니다.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>역방향 주소 검색을 사용하여 주소 검색

Azure Maps [Get Search Address 역방향 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 는 좌표를 사람이 읽을 수 있는 주소로 변환 합니다. 이 API는 GPS 피드를 사용 하 고 특정 좌표 지점에서 주소를 검색 하려는 응용 프로그램에 자주 사용 됩니다.

>[!IMPORTANT]
>사용자에 대 한 관련 영역에 ias 결과를 추가 하려면 항상 가능한 한 많은 위치 정보를 추가 합니다. 자세한 내용은 [검색 모범 사례](how-to-use-best-practices-for-search.md#geobiased-search-results)를 참조 하세요.

>[!TIP]
>역방향 geocode를 위한 좌표 위치 집합이 있는 경우 [사후 검색 주소 역방향 BATCH API](/rest/api/maps/search/postsearchaddressreversebatch) 를 사용 하 여 단일 API 호출로 쿼리 일괄 처리를 보낼 수 있습니다.

이 예제에서는 사용 가능한 선택적 매개 변수 중 일부를 사용 하 여 역방향 검색을 수행 합니다. 선택적 매개 변수의 전체 목록은 [역방향 검색 매개 변수](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)를 참조 하세요.

1. Postman 앱에서 **새로 만들기** 를 클릭 하 고 **요청** 을 선택 합니다. 요청에 대한 **요청 이름** 을 입력합니다. 첫 번째 섹션에서 만든 컬렉션을 선택 하거나 새로 만든 다음, **저장** 을 선택 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다. 요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. **보내기** 를 클릭 하 고 응답 본문을 검토 합니다. 하나의 쿼리 결과가 표시 되어야 합니다. 응답에는 Safeco Field에 대한 주요 주소 정보가 포함됩니다.
  
4. 이제 **Params** 섹션에 다음 키/값 쌍을 추가 합니다.

    | 키 | 값 | 반환
    |-----|------------|------|
    | number | 1 |응답에는 번 지 (왼쪽/오른쪽) 및 숫자에 대 한 오프셋 위치가 포함 될 수 있습니다.|
    | returnSpeedLimit | true | 주소의 속도 제한을 반환 합니다.|
    | returnRoadUse | true | 주소에서도로 사용 유형을 반환 합니다. 모든 가능한 이동 형식에 대해 [이동 유형 사용](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)을 참조 하세요.|
    | returnMatchType | true| 일치 항목의 유형을 반환 합니다. 가능한 모든 값은 [역방향 주소 검색 결과](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult) 를 참조 하세요.

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="주소 검색":::

5. **보내기** 를 클릭 하 고 응답 본문을 검토 합니다.

6. 다음으로 키를 추가 하 `entityType` 고 해당 값을로 설정 `Municipality` 합니다. `entityType` `returnMatchType` 이전 단계에서 키가 키를 재정의 합니다. `returnSpeedLimit` `returnRoadUse` 지방 자치 체에 대 한 정보를 요청 하 고 있으므로도 제거 해야 합니다.  가능한 모든 엔터티 형식은 [엔터티 형식](/rest/api/maps/search/getsearchaddressreverse#entitytype)을 참조 하세요.

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="주소 검색":::

7. **보내기** 를 클릭합니다. 결과를 5 단계에서 반환 된 결과와 비교 합니다.  요청 된 엔터티 형식이 이제 이므로 `municipality` 응답은 주소 정보를 포함 하지 않습니다. 또한 반환 된를 `geometryId` 사용 하 여 Azure Maps [검색 polygon API](/rest/api/maps/search/getsearchpolygon)를 통해 경계 다각형을 요청할 수 있습니다.

>[!TIP]
>이러한 매개 변수에 대 한 자세한 내용과 다른 방법에 대 한 자세한 내용은 [역방향 검색 매개 변수 섹션](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)을 참조 하세요.

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>역 주소 교차 번 검색을 사용 하 여 교차 주소 검색

이 예제에서는 주소의 좌표를 기준으로 교차 주소를 검색 합니다.

1. Postman 앱에서 **새로 만들기** 를 클릭 하 고 **요청** 을 선택 합니다. 요청에 대한 **요청 이름** 을 입력합니다. 첫 번째 섹션에서 만든 컬렉션을 선택 하거나 새로 만든 다음, **저장** 을 선택 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다. 요청은 다음 URL과 같습니다.
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="주소 검색":::
  
3. **보내기** 를 클릭 하 고 응답 본문을 검토 합니다. 응답에는 값이 포함 되어 있음을 알 수 있습니다 `crossStreet` `Occidental Avenue South` .

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Maps Search Service REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure Maps Search Service 모범 사례](how-to-use-best-practices-for-search.md)