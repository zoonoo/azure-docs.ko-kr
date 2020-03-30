---
title: Azure 지도 검색 서비스를 사용하여 위치 검색 | 마이크로소프트 Azure 지도
description: 이 문서에서는 지오코딩 및 역방향 지오코딩을 위해 Microsoft Azure Maps 검색 서비스를 사용하여 위치를 검색하는 방법을 배웁니다.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335423"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Azure 지도 검색 서비스를 사용하여 위치 검색

Azure Maps [검색 서비스는](https://docs.microsoft.com/rest/api/maps/search) 개발자가 주소, 장소, 이름 또는 범주별 비즈니스 목록 및 기타 지리적 정보를 검색할 수 있도록 설계된 RESTful API 집합입니다. 기존의 지오코딩을 지원하는 것 외에도 서비스는 위도와 경도에 따라 지오코드 주소와 교차로를 되돌릴 수 있습니다. 검색에서 반환되는 위도 및 경도 값은 [경로](https://docs.microsoft.com/rest/api/maps/route) 및 [날씨](https://docs.microsoft.com/rest/api/maps/weather) 서비스와 같은 다른 Azure Maps 서비스의 매개 변수로 사용할 수 있습니다.

이 문서에서 는 방법을 배울 것입니다 :

* 검색 주소 [API를]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 사용하여 주소(지오코드 주소 위치)에 대한 위도 및 경도 좌표 요청
* [퍼지 검색 API를](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 사용하여 주소 또는 POI(관심 지점) 검색
* 속성 및 좌표와 함께 주소 검색
* 좌표 위치를 도로 주소로 변환하려면 [역주소 검색을](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 만듭니다.
* 검색 주소 역 [교차 도로 API를](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet) 사용하여 교차로 검색

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 먼저 Azure Maps 계정을 만들고 계정 구독 키를 매핑해야 합니다. [계정 만들기의](quick-demo-map-app.md#create-an-account-with-azure-maps) 지침에 따라 Azure Maps 계정 구독을 만들고 [기본 키를 받는](quick-demo-map-app.md#get-the-primary-key-for-your-account) 단계를 수행하여 계정의 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>주소에 대한 위도 및 경도 요청(지오코딩)

이 예제에서는 Azure Maps [Get Search 주소 API를](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 사용하여 거리 주소를 위도 및 경도 좌표로 변환합니다. 전체 또는 부분 도로 주소를 API에 전달하고 거리, 우편 번호 및 국가/지역과 같은 자세한 주소 속성과 위도 및 경도의 위치 값을 포함하는 응답을 받을 수 있습니다.

지오코딩할 주소 집합이 있는 경우 검색 [주소 배치 배치 후 API를](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) 사용하여 단일 API 호출에서 쿼리 일괄 처리를 보낼 수 있습니다.

1. 우체부에서 **새 요청** | **GET 요청을** 클릭하고 주소 **검색**이름을 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력한 다음 권한 부여 프로토콜이 있는 경우 해당 프로토콜을 선택합니다.

![주소 검색](./media/how-to-search-for-address/address_search_url.png)

| 매개 변수 | 제안 값 |
|---------------|------------------------------------------------| 
| HTTP 메서드 | GET |
| 요청 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| 권한 부여 | 인증 없음 |

3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다. 

![주소 검색](./media/how-to-search-for-address/address_search_params.png) 

| Key | 값 | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| 구독 키 | \<사용자의 Azure 맵 키\> | 
| Query | 400 한 광범위 한 St, 시애틀, WA 98109 | 

4. **보내기**를 클릭하고 응답 본문을 검토합니다. 

이 경우 전체 주소 쿼리를 지정했으며 응답 본문에 단일 결과를 받습니다. 

5. 매개 변수에 다음 값에 대한 쿼리 문자열을 편집합니다. 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다. 

| Key | 값 | 
|-----|------------| 
| typeahead | true | 

**typeahead** 플래그는 주소 검색 API에 쿼리를 부분 입력으로 처리하고 예측 값의 배열을 반환하도록 지시합니다.

## <a name="using-fuzzy-search-api"></a>퍼지 검색 API 사용

Azure Maps[ 퍼지 검색 API는](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 사용자 입력이 검색 쿼리에 대해 무엇인지 모르는 경우 사용하는 것이 좋습니다. API는 POI(관심 지점) 검색및 지오코딩을 표준 '한 줄 검색'으로 결합합니다. 예를 들어 API는 모든 주소 또는 POI 토큰 조합의 입력을 처리할 수 있습니다. 또한 API는 컨텍스트 위치(위도/경도 쌍)에 가중치를 부여하거나, 좌표와 반경으로 완전히 제한하거나, 지리적으로 편향된(geo biasing) 앵커 지점 없이 더 일반적으로 실행할 수도 있습니다.

대부분의 검색 쿼리는 성능을 달성하고 비정상적인 결과를 줄이기 위해 기본적으로 `maxFuzzyLevel=1`로 설정됩니다. 이 기본값은 요청에 따라 필요한 경우 쿼리 매개 변수에 `maxFuzzyLevel=2` 또는 `3`을 전달하여 재정의할 수 있습니다.

### <a name="search-for-an-address-using-fuzzy-search"></a>유사 항목 검색을 사용하여 주소 검색

1. Postman 앱을 열고 새로 만들기 새로 만들기를 클릭하고 **GET 요청**을 선택합니다. **유사 항목 검색**의 요청 이름을 입력하고 이를 저장할 컬렉션 또는 폴더를 선택하고 **저장**을 클릭합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_url.png)

    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | 권한 부여 | 인증 없음 |

    URL 경로의 **json** 특성은 응답 형식을 결정합니다. 이 문서에서는 사용 의 용이성과 가독성을 위해 json을 사용합니다. 사용 가능한 응답 형식은 [Maps 기능 API 참조](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)의 **유사 항목 검색 가져오기** 정의에서 찾을 수 있습니다.

3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Key | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 피자 |

4. **보내기**를 클릭하고 응답 본문을 검토합니다.

    "피자"에 대한 모호한 쿼리 문자열은 "피자" 및 "레스토랑" 범주 모두에서 관심 지점 결과(POI)를 [10포인트](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) 반환했습니다. 각 결과는 위치의 거리 주소, 위도 및 경도 값, 뷰 포트 및 진입점을 반환합니다.
  
    결과는 특정 기준 위치에 연결되지 않고 이 쿼리에 대해 변화됩니다. **countrySet** 매개 변수를 사용하여 응용 프로그램에 적용 범위가 필요한 국가/지역만 지정할 수 있습니다. 기본 동작은 전 세계를 검색하여 불필요한 결과를 반환하는 것입니다.

5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | Key | 값 |
    |------------------|-------------------------|
    | countrySet | US |
  
    이제 결과는 국가 코드로 경계가 구분되며 쿼리는 미국의 피자 음식점을 반환합니다.
  
    위치에 대한 결과를 제공하려면 관심 지점을 쿼리하고 반환된 위도와 경도 값을 유사 항목 검색 서비스에 대한 호출에 사용할 수 있습니다. 이 경우 검색 서비스를 사용하여 시애틀의 스페이스 니들(Space Needle) 탑의 위치를 반환하고 위도 / 경도를 사용했습니다. 검색 방향을 지정하는 값입니다.
  
6. 매개 변수에서 다음 키 / 값 쌍을 입력하고 **보내기**를 클릭합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Key | 값 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>역방향 주소 검색을 사용하여 주소 검색

Azure Maps [Get 검색 주소 역방향 API는]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 좌표(예: 37.786505, -122.3862)를 사람이 이해할 수 있는 거리 주소로 변환하는 데 도움이 됩니다. 대부분의 경우 이 방법은 장치 또는 자산에서 GPS 피드를 수신하고 좌표가 있는 주소를 알고 자하는 추적 응용 프로그램에 필요합니다.
지오코드를 되돌릴 좌표 위치 집합이 있는 경우 [검색 주소 역배치 배치 후 게시 를](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) 사용하여 단일 API 호출에서 쿼리 일괄 처리를 보낼 수 있습니다.


1. 우체부에서 **새 요청** | **GET 요청을** 클릭하고 **역주소 검색.**

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.
  
    ![역 주소 검색 URL](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | 권한 부여 | 인증 없음 |
  
3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
  
    ![역 주소 검색 매개 변수](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Key | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 47.591180,-122.332700 |
  
4. **보내기**를 클릭하고 응답 본문을 검토합니다.

    응답에는 Safeco Field에 대한 주요 주소 정보가 포함됩니다.
  
5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | Key | 값 |
    |-----|------------|
    | number | true |

    [요청과](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 함께 숫자 쿼리 매개 변수가 전송되는 경우 응답에는 거리의 측면(왼쪽 또는 오른쪽)과 해당 번호의 오프셋 위치가 포함될 수 있습니다.
  
6. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | Key | 값 |
    |-----|------------|
    | returnSpeedLimit | true |
  
    [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 쿼리 매개 변수가 설정되면 응답은 게시된 속도 제한을 반환합니다.

7. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | Key | 값 |
    |-----|------------|
    | returnRoadUse | true |

    [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 쿼리 매개 변수가 설정되면 응답에서 도로 수준의 역방향 지역 코드에 대한 도로 사용 배열을 반환합니다.

8. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | Key | 값 |
    |-----|------------|
    | roadUse | true |

    roadUse 쿼리 매개 변수를 사용하여 역방향 지오코드 쿼리를 특정 유형의 도로로 제한할 수 [있습니다.](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>역주소 크로스 스트리트 검색을 사용하여 교차로 검색

1. 우체부에서 **새 요청** | **GET 요청을** 클릭하고 **역주소 교차 거리 검색의**이름을 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.
  
    ![역 주소 교차로 검색](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | 권한 부여 | 인증 없음 |
  
3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
  
    | Key | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 47.591180,-122.332700 |
  
4. **보내기**를 클릭하고 응답 본문을 검토합니다.

## <a name="next-steps"></a>다음 단계

- Azure [Maps 검색 서비스 REST API 설명서를](https://docs.microsoft.com/rest/api/maps/search)살펴보십시오.
- Azure [Maps 검색 서비스 모범 사례](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) 및 쿼리를 최적화하는 방법에 대해 알아봅니다.
