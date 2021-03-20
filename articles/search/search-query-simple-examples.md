---
title: Simple Lucene 쿼리 구문 사용
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱스에 대해 전체 텍스트 검색, 필터 검색 및 지역 검색에 대 한 간단한 구문을 보여 주는 쿼리 예제입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694039"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search에서 "simple" 검색 구문을 사용 합니다.

Azure Cognitive Search에서 [단순 쿼리 구문은](query-simple-syntax.md) 전체 텍스트 검색에 대 한 기본 쿼리 파서를 호출 합니다. 파서는 속도가 빠르며 전체 텍스트 검색, 필터링 된 패싯 검색 및 접두사 검색을 포함 한 일반적인 시나리오를 처리 합니다. 이 문서에서는 예제를 사용 하 여 [REST API (검색 문서)](/rest/api/searchservice/search-documents) 요청의 간단한 구문 사용을 보여 줍니다.

> [!NOTE]
> 대체 쿼리 구문은 [전체 Lucene](query-lucene-syntax.md)이며 유사 항목 및 와일드 카드 검색과 같은 보다 복잡 한 쿼리 구조를 지원 합니다. 자세한 내용 및 예제 [는 Full Lucene 구문 사용](search-query-lucene-examples.md)을 참조 하세요.

## <a name="hotels-sample-index"></a>호텔 샘플 인덱스

다음 쿼리는이 [빠른](search-get-started-portal.md)시작의 지침에 따라 만들 수 있는 호텔-샘플 인덱스를 기반으로 합니다.

예제 쿼리는 REST API 및 POST 요청을 사용 하 여 더 합니다. [Cognitive Search 확장을 사용](search-get-started-vs-code.md)하 여 [postman](search-get-started-rest.md) 또는 Visual Studio Code에서 붙여넣거나 실행할 수 있습니다.

요청 헤더의 값은 다음과 같아야 합니다.

| 키 | 값 |
|-----|-------|
| 콘텐츠 형식 | application/json|
| api-key  | `<your-search-service-api-key>`(쿼리 또는 관리 키 중 하나) |

URI 매개 변수는 다음 예제와 유사 하 게 인덱스 이름, docs 컬렉션, 검색 명령 및 API 버전을 포함 하는 검색 서비스 끝점을 포함 해야 합니다.

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

요청 본문은 유효한 JSON으로 구성 되어야 합니다.

```json
{
    "search": "*",
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ 로 설정 된 "검색" `*` 은 지정 되지 않은 쿼리로 null 또는 빈 검색에 해당 합니다. 특히 유용 하지는 않지만 모든 값을 사용 하 여 인덱스에서 검색할 수 있는 모든 필드를 표시 하는 것이 가장 간단 합니다.

+ "simple"로 설정 된 "queryType"은 기본값이 며 생략할 수 있지만이 문서의 쿼리 예제는 간단한 구문으로 표현 됩니다.

+ 쉼표로 구분 된 필드 목록으로 설정 된 "선택"은 검색 결과의 컨텍스트에 유용한 필드만 포함 하 여 검색 결과 컴포지션에 사용 됩니다.

+ "count"는 검색 조건과 일치 하는 문서 수를 반환 합니다. 빈 검색 문자열에서 개수는 인덱스의 모든 문서 (호텔-샘플 인덱스의 경우 50)가 됩니다.

## <a name="example-1-full-text-search"></a>예 1: 전체 텍스트 검색

전체 텍스트 검색에는 부울 연산자를 사용 하거나 사용 하지 않는 독립 실행형 용어 또는 따옴표를 포함할 수 있습니다. 

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

중요 한 용어 또는 구로 구성 된 키워드 검색을 사용 하는 것이 가장 좋습니다. 문자열 필드는 인덱싱 및 쿼리 중에 텍스트 분석을 수행 하 여 "the", "and", "it"와 같은 필수적이 지 않은 단어를 삭제 합니다. 인덱스에서 쿼리 문자열을 토큰화 하는 방법을 보려면 인덱스에 [텍스트 분석](/rest/api/searchservice/test-analyzer) 호출의 문자열을 전달 합니다.

"SearchMode" 매개 변수는 전체 자릿수 및 회수를 제어 합니다. 더 많은 회수를 원하는 경우 쿼리 문자열의 일부가 일치 하는 경우 결과를 반환 하는 기본 "any" 값을 사용 합니다. 전체 자릿수를 선호 하는 경우 문자열의 모든 부분이 일치 해야 한다면 searchMode를 "all"로 변경 합니다. 위의 쿼리를 사용해 서 searchMode가 결과를 어떻게 변경 하는지 확인 합니다.

"풀 spa + 공항" 쿼리에 대 한 응답은 다음 예제와 유사 하 게 표시 되어야 합니다.

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

응답의 검색 점수를 확인 합니다. 일치 항목의 관련성 점수입니다. 기본적으로 검색 서비스는이 점수를 기준으로 상위 50 개 일치 항목을 반환 합니다.

"1.0"의 균일 점수는 검색에 전체 텍스트 검색이 없거나 조건이 제공 되지 않았기 때문에 순위가 없을 때 발생 합니다. 예를 들어 빈 검색 (search =)에서는 `*` 행이 임의의 순서로 반환 됩니다. 실제 조건을 포함하는 경우 검색 점수가 의미 있는 값으로 바뀌는 것을 볼 수 있습니다.

## <a name="example-2-look-up-by-id"></a>예제 2: ID별 조회

쿼리에서 검색 결과를 반환 하는 경우 논리적 다음 단계는 문서에서 더 많은 필드를 포함 하는 세부 정보 페이지를 제공 하는 것입니다. 이 예에서는 문서 ID를 전달 하 여 [조회 문서](/rest/api/searchservice/lookup-document) 를 사용 하는 단일 문서를 반환 하는 방법을 보여 줍니다.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

모든 문서에는 고유 식별자가 있습니다. 포털을 사용 하는 경우 **인덱스 탭에서 인덱스를** 선택 하 고 필드 정의를 확인 하 여 키가 되는 필드를 확인 합니다. REST를 사용 하 여 [인덱스 가져오기](/rest/api/searchservice/get-index) 호출은 응답 본문의 인덱스 정의를 반환 합니다.

위의 쿼리에 대 한 응답은 키가 41 인 문서로 구성 됩니다. 인덱스 정의에서 "검색 가능"으로 표시 된 모든 필드는 검색 결과에서 반환 되 고 앱에서 렌더링 될 수 있습니다.

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

[필터 구문은](search-query-odata-filter.md) 단독으로 또는 "검색"으로 사용할 수 있는 OData 식입니다. "필터"를 함께 사용 하 여 전체 인덱스에 먼저 적용 한 다음 필터 결과에 대해 검색을 수행 합니다. 따라서 필터는 검색 쿼리가 처리해야 하는 일련의 문서를 감소시키기 때문에 쿼리 성능을 개선하는 데 유용한 기술일 수 있습니다.

인덱스 정의에서 "필터링 가능"으로 표시 된 모든 필드에서 필터를 정의할 수 있습니다. 호텔-샘플 인덱스의 경우 필터링 가능한 필드에는 범주, 태그, ParkingIncluded, 등급 및 대부분의 주소 필드가 포함 됩니다.

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

위의 쿼리에 대 한 응답은 "art" 또는 "둘러보기" 라는 용어를 포함 하는 "보고서 및 Spa"로 분류 된 호텔로만 범위가 한정 됩니다. 이 경우 하나의 일치 항목만 있습니다.

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

필터 식에는 필터 내에서 검색 쿼리를 작성할 수 있도록 하는 ["ismatch" 및 "검색. ismatchscoring 매기기" 함수](search-query-odata-full-text-search-functions.md)를 포함할 수 있습니다. 이 필터 식에서는 와일드 카드를 *사용 하 여* 무료 wifi, 무료 주차 등을 포함 한 편의 시설을 선택 합니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

위의 쿼리에 대 한 응답은 무료 편의 시설을 제공 하는 19 개의 호텔에 일치 합니다. 검색 점수가 결과 전체에 걸쳐 균일 하 게 "1.0"입니다. 이는 검색 식이 null 이거나 비어 있기 때문입니다 .이로 인해 축 자 필터는 일치 하지만 전체 텍스트 검색은 없습니다. 관련성 점수는 전체 텍스트 검색 에서만 반환 됩니다. "검색" 없이 필터를 사용 하는 경우 검색 순위를 제어할 수 있도록 정렬 가능한 필드가 충분 한지 확인 합니다.

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

## <a name="example-5-range-filters"></a>예 5: 범위 필터

범위 필터링은 모든 데이터 형식에 대 한 필터 식을 통해 지원 됩니다. 다음 예에서는 숫자 및 문자열 범위를 보여 줍니다. 데이터 형식은 범위 필터에서 중요하며, 숫자 데이터가 숫자 필드에 있고 문자열 데이터가 문자열 필드에 있으면 가장 잘 작동합니다. 문자열 필드의 숫자 데이터는 숫자 문자열을 비교할 수 없기 때문에 범위에 적합 하지 않습니다.

다음 쿼리는 숫자 범위입니다. 호텔-샘플 인덱스에서 필터링 가능한 유일한 숫자 필드는 등급입니다.

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

이 쿼리에 대 한 응답은 다음 예제와 유사 하 게 보입니다.

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

다음 쿼리는 문자열 필드 (Address/StateProvince)에 대 한 범위 필터입니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

이 쿼리에 대 한 응답은 아래 예제와 같이 간단 하 게 나타내기 위해 잘립니다. 이 예에서는 인덱스 정의에서 필드의 특성을 "정렬 가능"으로 지정 하지 않으므로 StateProvince를 기준으로 정렬할 수 없습니다.

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

## <a name="example-6-geo-search"></a>예 6: 지역에서 검색

호텔-샘플 인덱스에는 위도 및 경도 좌표가 있는 geo_location 필드가 포함 됩니다. 이 예제에서는 시작 지점의 원주 내에서 사용자가 지정하는 임의의 거리(킬로미터 단위)에 문서를 필터링하는 [geo.distance 함수](search-query-odata-geo-spatial-functions.md#examples)를 사용합니다. 쿼리의 마지막 값 (10)을 조정 하 여 쿼리의 노출 영역을 줄이거나 늘릴 수 있습니다.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

이 쿼리에 대 한 응답은 제공 된 좌표의 10 kilometer 거리에 있는 모든 호텔을 반환 합니다.

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

단순 구문은 문자 () 형식의 부울 연산자를 지원 `+, -, |` 하 여 and, OR 및 NOT 쿼리 논리를 지원 합니다. 부울 검색은 몇 가지 주목할 만한 예외를 제외 하 고 예측할 수 있는 것 처럼 동작 합니다. 

이전 예제에서 "searchmode" 매개 변수는 precision 및 회수에 영향을 주는 메커니즘으로 도입 되었습니다. "searchMode = any" 찾은 다음 리콜 (조건을 충족 하는 문서는 일치 항목으로 간주 됨) 및 "searchMode = all" 찾은 다음 precision (모든 조건은 문서에서 일치 해야 함). 

부울 검색의 컨텍스트에서는 여러 연산자를 사용 하 여 쿼리를 누적 하 고 좁은 결과 대신 광범위 하 게 사용 하는 경우 기본 "searchMode = any"를 사용 하면 혼란 스 러 울 수 있습니다. 이는 결과에 특정 용어나 구를 포함 하지 않는 모든 문서를 포함 하는 경우에 특히 그렇습니다.

다음 예제에서 이에 대해 설명합니다. SearchMode (any)를 사용 하 여 다음 쿼리 42를 실행 하면 "식당" 이라는 단어가 포함 된 문서와 "공기 조절" 이라는 구가 없는 모든 문서를 반환 합니다. 

부울 연산자 ( `-` )와 "공기 조절" 이라는 문구 사이에 공백이 없습니다.

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

"SearchMode = all"로 변경 하면 조건에 대 한 누적 효과가 적용 되 고 "식당" 이라는 용어가 포함 된 문서로 구성 된 작은 결과 집합 (7 개 일치)을 반환 합니다. 여기에는 "공기 조절" 이라는 구가 포함 되어 있습니다.

이 쿼리에 대 한 응답은 다음 예제와 유사 하 게 보입니다.

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

## <a name="example-8-paging-results"></a>예 8: 페이징 결과

이전 예제에서는 결과에 포함 되는 필드를 결정 하는 "select"를 비롯 하 여 검색 결과 컴퍼지션에 영향을 주는 매개 변수를 배웠습니다. 정렬 순서 및 모든 일치 항목의 수를 포함 하는 방법을 배웠습니다. 이 예제는 지정 된 페이지에 표시 되는 결과의 수를 일괄 처리할 수 있는 페이징 매개 변수 형식의 검색 결과 컴퍼지션의 연속입니다. 

기본적으로 검색 서비스는 상위 50 일치 항목을 반환 합니다. 각 페이지의 일치 항목 수를 제어 하려면 "top"을 사용 하 여 일괄 처리의 크기를 정의한 다음 "skip"을 사용 하 여 후속 일괄 처리를 선택 합니다.

다음 예에서는 정렬 된 결과에 대 한 페이징 효과를 쉽게 확인할 수 있으므로 등급 필드에서 필터 및 정렬 순서를 사용 합니다 (등급은 필터링 및 정렬 가능). 일반 전체 검색 쿼리에서 상위 일치 항목은 ""으로 순위를 지정할 수 있습니다 @search.score .

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

이 쿼리는 21 개의 일치 하는 문서를 찾았지만 "top"을 지정 했기 때문에 응답은 4.9에서 시작 하 여 4.7에서 종료 되 고 "Aalake B & B"로 끝나는 상위 5 개 일치 항목만 반환 합니다. 

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

두 번째 일괄 처리에 대 한 응답은 처음 5 개 일치 항목을 건너뛴 후 다음 5 개를 반환 합니다. "Pull'r I n n Motel"부터 시작 합니다. 추가 일괄 처리를 계속 하려면 "위쪽"을 5로 유지 한 다음 새 요청 마다 "skip"을 5로 늘립니다 (skip = 5, skip = 10, skip = 15 등).

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

이제 기본 쿼리 구문을 사용 하는 것이 좋지만 코드에서 쿼리를 지정 해 보세요. 다음 링크는 Azure Sdk를 사용 하 여 검색 쿼리를 설정 하는 방법을 설명 합니다.

+ [.NET SDK를 사용 하 여 인덱스 쿼리](search-get-started-dotnet.md)
+ [Python SDK를 사용 하 여 인덱스 쿼리](search-get-started-python.md)
+ [JavaScript SDK를 사용 하 여 인덱스 쿼리](search-get-started-javascript.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](query-simple-syntax.md)
+ [전체 Lucene 쿼리 구문](query-lucene-syntax.md)
+ [필터 구문](search-query-odata-filter.md)