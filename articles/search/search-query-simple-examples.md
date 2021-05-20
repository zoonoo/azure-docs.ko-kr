---
title: simple Lucene 쿼리 구문 사용
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱스에 대해 전체 텍스트 검색, 필터 검색 및 지역 검색에 대한 simple 구문을 보여 주는 쿼리 예제입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694039"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search에서 “simple” 검색 구문 사용

Azure Cognitive Search에서 [simple 쿼리 구문](query-simple-syntax.md)은 전체 텍스트 검색에 대한 기본 쿼리 파서를 호출합니다. 파서는 속도가 빠르며 전체 텍스트 검색, 필터링된 패싯 검색 및 접두사 검색을 포함한 일반적인 시나리오를 처리합니다. 이 문서에서는 예제를 사용하여 [검색 문서(REST API)](/rest/api/searchservice/search-documents) 요청의 simple 구문 사용을 보여 줍니다.

> [!NOTE]
> 대체 쿼리 구문은 유사 항목 및 와일드카드 검색과 같은 더 복잡한 쿼리 구조를 지원하는 [전체 Lucene](query-lucene-syntax.md)입니다. 자세한 내용 및 예제는 [전체 Lucene 구문 사용](search-query-lucene-examples.md)을 참조하세요.

## <a name="hotels-sample-index"></a>호텔 샘플 인덱스

다음 쿼리는 이 [빠른 시작](search-get-started-portal.md)의 지침에 따라 만들 수 있는 hotels-sample-index를 기반으로 합니다.

예제 쿼리는 REST API 및 POST 요청을 사용하여 설명됩니다. [Postman](search-get-started-rest.md)에서 또는 [Cognitive Search 확장을 사용한 Visual Studio Code](search-get-started-vs-code.md)에서 붙여넣거나 실행할 수 있습니다.

요청 헤더의 값은 다음과 같아야 합니다.

| 키 | 값 |
|-----|-------|
| 콘텐츠 형식 | application/json|
| api-key  | `<your-search-service-api-key>`, 쿼리 또는 관리 키 중 하나 |

URI 매개 변수는 다음 예제와 유사하게 인덱스 이름, docs 컬렉션, 검색 명령 및 API 버전을 포함하는 검색 서비스 엔드포인트를 포함해야 합니다.

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

요청 본문은 유효한 JSON으로 구성되어야 합니다.

```json
{
    "search": "*",
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ `*`로 설정된 “검색”은 지정되지 않은 쿼리로, null 또는 빈 검색에 해당합니다. 특별히 유용한 것은 아니지만 할 수 있는 것 중 가장 간단한 검색이며, 모든 값을 사용하여 인덱스에서 조회 가능 필드를 모두 표시합니다.

+ “simple”로 설정된 “queryType”은 기본값이며 생략할 수 있지만, 이 문서의 쿼리 예제가 simple 구문으로 표현되었음을 더 확실히 나타내기 위해 포함되었습니다.

+ 쉼표로 구분된 필드 목록으로 설정된 “select”는 검색 결과의 컨텍스트에서 유용한 필드만 포함하여 검색 결과 컴퍼지션에 사용됩니다.

+ “count”는 검색 조건과 일치하는 문서 수를 반환합니다. 빈 검색 문자열에서 개수는 인덱스의 모든 문서(hotels-sample-index의 경우 50개)가 됩니다.

## <a name="example-1-full-text-search"></a>예제 1: 전체 텍스트 검색

전체 텍스트 검색에는 부울 연산자를 사용하거나 사용하지 않는 독립 실행형 용어 또는 따옴표의 수가 될 수 있습니다. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

중요한 용어나 구로 구성된 키워드 검색을 사용하는 것이 가장 좋습니다. 문자열 필드는 인덱싱 및 쿼리 중에 텍스트 분석을 수행하여 “the”, “and”, “it”과 같은 필수적이지 않은 단어를 삭제합니다. 인덱스에서 쿼리 문자열이 토큰화되는 방식을 보려면 [텍스트 분석](/rest/api/searchservice/test-analyzer) 호출의 문자열을 인덱스에 전달합니다.

“searchMode” 매개 변수는 정확성 및 재현율을 제어합니다. 재현율을 더 원하는 경우 쿼리 문자열의 일부가 일치하는 경우 결과를 반환하는 기본 “any” 값을 사용합니다. 정확성을 선호하는 경우 문자열의 모든 부분이 일치해야 한다면 searchMode를 “all”로 변경합니다. searchMode로 결과가 어떻게 바뀌는지 확인하기 위해 위의 쿼리를 두 방법 모두로 시도해 보세요.

“pool spa +airport” 쿼리에 대한 응답은 간결하게 다듬은 다음 예제와 유사하게 표시되어야 합니다.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

응답에서 검색 점수를 확인해 보세요. 이는 일치 항목의 관련성 점수입니다. 기본적으로 검색 서비스는 이 점수를 기준으로 상위 50개의 일치 항목을 반환합니다.

순위가 없으면 균일하게 점수 “1.0”이 발생합니다. 검색이 전체 텍스트 검색이 아니거나 어떤 조건도 적용되지 않기 때문입니다. 예를 들어 빈 검색(search=`*`)에서는 행이 임의의 순서로 반환됩니다. 실제 조건을 포함하는 경우 검색 점수가 의미 있는 값으로 바뀌는 것을 볼 수 있습니다.

## <a name="example-2-look-up-by-id"></a>예제 2: ID별 조회

쿼리에서 검색 결과를 반환하는 경우 논리적 다음 단계는 문서에서 더 많은 필드를 포함하는 세부 정보 페이지를 제공하는 것입니다. 이 예제에서는 문서 ID를 전달하여 [문서 조회](/rest/api/searchservice/lookup-document)를 사용하는 단일 문서를 반환하는 방법을 보여 줍니다.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

모든 문서에는 고유 식별자가 있습니다. 포털을 사용하는 경우 **인덱스** 탭에서 인덱스를 선택한 다음, 필드 정의를 살펴봐서 키가 되는 필드를 확인합니다. REST를 사용하면 [Get Index](/rest/api/searchservice/get-index) 호출은 응답 본문의 인덱스 정의를 반환합니다.

위의 쿼리에 대한 응답은 키가 41인 문서로 구성됩니다. 인덱스 정의에서 “조회 가능”으로 표시된 모든 필드는 검색 결과에서 반환될 수 있으며 앱에서 렌더링될 수 있습니다.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>예제 3: 텍스트 필터링

[필터 구문](search-query-odata-filter.md)은 “검색” 또는 그 자체로 사용할 수 있는 OData 식입니다. 이러한 항목을 함께 사용하는 경우 먼저 “필터”가 전체 인덱스에 적용된 다음, 필터의 결과에서 검색이 수행됩니다. 따라서 필터는 검색 쿼리가 처리해야 하는 일련의 문서를 감소시키기 때문에 쿼리 성능을 개선하는 데 유용한 기술일 수 있습니다.

인덱스 정의에서 “필터링 가능”으로 표시된 모든 필드에서 필터를 정의할 수 있습니다. hotels-sample-index의 경우 필터링 가능 필드에는 범주, 태그, ParkingIncluded, 등급 및 대부분의 주소 필드가 포함됩니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

위의 쿼리에 대한 응답은 “art” 또는 “tours”라는 용어를 포함하는 “Report and Spa”로 분류된 호텔로만 범위가 한정됩니다. 이 경우 하나의 일치 항목만 있습니다.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>예제 4: 필터 함수

필터 식에는 사용자가 필터 내에서 검색 쿼리를 빌드할 수 있는 [“search.ismatch” 및 “search.ismatchscoring” 함수](search-query-odata-full-text-search-functions.md)가 포함될 수 있습니다. 이 필터 식은 *무료* 에 대한 와일드카드를 사용하여 무료 WiFi, 무료 주차 등을 포함한 편의 시설을 선택합니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

위의 쿼리에 대한 응답은 무료 편의 시설을 제공하는 19개의 호텔과 일치합니다. 검색 점수가 결과 전체에 걸쳐 균일하게 “1.0”임에 유의합니다. 이는 검색 식이 null이거나 비어 있기 때문입니다. 이로 인해 축자 필터는 일치하지만 전체 텍스트 검색은 없습니다. 관련성 점수는 전체 텍스트 검색에서만 반환됩니다. “검색” 없이 필터를 사용하는 경우 검색 순위를 제어할 수 있도록 정렬 가능한 필드가 충분한지 확인합니다.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>예제 5: 범위 필터

범위 필터링은 모든 데이터 형식에 대한 필터 식을 통해 지원됩니다. 다음 예제에서는 숫자 및 문자열 범위를 보여 줍니다. 데이터 형식은 범위 필터에서 중요하며, 숫자 데이터가 숫자 필드에 있고 문자열 데이터가 문자열 필드에 있으면 가장 잘 작동합니다. 숫자 문자열을 비교할 수 없으므로 문자열 필드의 숫자 데이터가 범위에 적합하지 않습니다.

다음 쿼리는 숫자 범위입니다. hotels-sample-index에서 필터링 가능한 유일한 숫자 필드는 등급입니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

이 쿼리에 대한 응답은 간결하게 다듬은 다음 예제와 유사하게 표시되어야 합니다.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

다음 쿼리는 문자열 필드(Address/StateProvince)에 대한 범위 필터입니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

이 쿼리에 대한 응답은 간결하게 다듬은 아래 예제와 유사하게 표시되어야 합니다. 이 예제에서는 인덱스 정의에서 필드의 특성을 “정렬 가능”으로 지정하지 않았으므로 StateProvince를 기준으로 정렬할 수 없습니다.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>예제 6: 지역에서 검색

hotels-sample-index에는 위도 및 경도 좌표를 사용한 geo_location 필드가 포함됩니다. 이 예제에서는 시작 지점의 원주 내에서 사용자가 지정하는 임의의 거리(킬로미터 단위)에 문서를 필터링하는 [geo.distance 함수](search-query-odata-geo-spatial-functions.md#examples)를 사용합니다. 쿼리(10)에서 마지막 값을 조정하여 쿼리 노출 영역을 줄이거나 확장할 수 있습니다.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

이 쿼리에 대한 응답은 제공된 좌표의 10km 거리에 있는 모든 호텔을 반환합니다.

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>예제 7: searchMode를 사용한 부울

단순 구문은 문자(`+, -, |`) 형식의 부울 연산자를 지원하여 AND, OR 및 NOT 쿼리 논리를 지원합니다. 부울 검색은 몇 가지 주목할 만한 예외를 제외하고 예상대로 작동합니다. 

이전 예제에서 “searchMode” 매개 변수는 정확성 및 재현율에 영향을 주는 메커니즘으로 도입되었습니다. 이는 재현율을 선호하면 “searchMode=any”(아무 조건을 충족하는 문서를 일치 항목으로 간주함)를, 정확도를 선호하면 “searchMode=all”(문서에서 모든 조건이 일치해야 함)을 사용합니다. 

부울 검색의 컨텍스트에서는 여러 연산자를 사용하여 쿼리를 누적하고 좁은 결과 대신 광범위하게 사용하는 경우 기본 “searchMode=any”를 사용하면 혼란스러울 수 있습니다. 결과에 특정 용어나 구를 “포함하지 않는” 모든 문서를 포함하게 되는 NOT을 사용할 경우에는 특히 이러한 현상이 두드러집니다.

다음 예제에서 이에 대해 설명합니다. searchMode(any)를 사용한 다음 쿼리를 실행하면 42개 문서가 반환됩니다. “restaurant”이라는 단어가 포함된 문서와 “air conditioning”이란 구가 포함되지 않은 모든 문서를 반환합니다. 

부울 연산자(`-`)와 “air conditioning”이란 구 사이에 공백이 없음에 유의하세요.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

“searchMode=all”로 변경하면 조건에 대한 누적 효과가 적용되고 “restaurant”이란 용어가 포함된 문서로 구성된 작은 결과 세트(7개 일치)를 반환합니다. 여기에는 “air conditioning”이라는 구가 포함된 문서가 빠져 있습니다.

이 쿼리에 대한 응답은 간결하게 다듬은 다음 예제와 유사하게 표시되어야 합니다.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>예제 8: 페이징 결과

이전 예제에서는 결과에 포함되는 필드를 결정하는 “select”, 정렬 순서 및 모든 일치 항목의 수를 포함시키는 방법을 비롯한 검색 결과 컴퍼지션에 영향을 주는 매개 변수에 대해 알아보았습니다. 이 예제는 지정된 페이지에 표시되는 결과의 수를 일괄 처리할 수 있도록 하는 페이징 매개 변수 형식의 연속적인 검색 결과 컴퍼지션입니다. 

기본적으로 검색 서비스는 상위 50개의 일치 항목을 반환합니다. 각 페이지의 일치 항목 수를 제어하려면 “top”을 사용하여 일괄 처리의 크기를 정의한 다음, “skip”을 사용하여 후속 일괄 처리를 선택합니다.

다음 예제에서는 정렬된 결과에 대한 페이징 효과를 쉽게 확인할 수 있으므로 등급 필드에서 필터 및 정렬 순서를 사용합니다(등급은 필터링 및 정렬 가능). 일반 전체 검색 쿼리에서 상위 일치 항목은 “@search.score”로 순위를 지정하고 페이징합니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

쿼리가 21개의 일치하는 문서를 찾았지만 “top”을 지정했기 때문에 응답은 등급이 4.9부터 시작하여 4.7로 종료되고 “Lady of the Lake B & B”가 포함된 상위 5개의 일치 항목만 반환합니다. 

다음 5개 문서를 가져오려면 첫 번째 일괄 처리를 건너뜁니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

두 번째 일괄 처리에 대한 응답은 처음 5개 일치 항목을 건너뛴 후 “Pull'r Inn Motel”로 시작하는 다음 5개를 반환합니다. 추가 일괄 처리를 계속하려면 “top”을 5로 유지한 다음, 새 요청마다 “skip”을 5로 늘립니다(skip=5, skip=10, skip=15 등).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>다음 단계

이제 기본 쿼리 구문으로 몇 가지 연습을 마쳤으니 코드에서 쿼리를 지정해 보세요. 다음 링크에서는 Azure SDK를 사용하여 검색 쿼리를 설정하는 방법에 대해 설명합니다.

+ [.NET SDK를 사용하여 인덱스 쿼리](search-get-started-dotnet.md)
+ [Python SDK를 사용하여 인덱스 쿼리](search-get-started-python.md)
+ [JavaScript SDK를 사용하여 인덱스 쿼리](search-get-started-javascript.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](query-simple-syntax.md)
+ [전체 Lucene 쿼리 구문](query-lucene-syntax.md)
+ [필터 구문](search-query-odata-filter.md)