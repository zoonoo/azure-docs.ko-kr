---
title: OData 지리 공간 함수 참조-Azure Search
description: OData 지리 공간 함수를 geo.distance 및 Azure Search 쿼리에 geo.intersects입니다.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079796"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>-Azure Search의 OData 지리 공간 함수 `geo.distance` 및 `geo.intersects`

Azure Search에서 지리 공간 쿼리를 지 원하는 [OData 필터 식](query-odata-filter-orderby-syntax.md) 를 통해 합니다 `geo.distance` 및 `geo.intersects` 함수입니다. `geo.distance` 필터의 일부로 전달 되는 필드 또는 범위 변수와 상수 중 하나의 되, 함수 두 점 사이의 거리를 거리를 킬로미터 단위로 반환 합니다. 합니다 `geo.intersects` 함수에서 반환 `true` 는 주어진 점이 주어진된 다각형, 여기서 지점은 필드 또는 범위 변수가 고 다각형은 필터의 일부로 전달 되는 상수로 지정 됩니다.

`geo.distance` 함수에서 사용할 수도 있습니다는 [ **$orderby** 매개 변수](search-query-odata-orderby.md) 지정된 된 지점에서의 거리 별로 검색 결과 정렬 합니다. **$orderby**에서 `geo.distance`에 대한 구문은 **$filter**에 있을 때와 같습니다. 사용 하는 경우 `geo.distance` 에 **$orderby**에 적용 되는 필드 형식 이어야 합니다 `Edm.GeographyPoint` 여야 하 고 **정렬 가능한**합니다.

## <a name="syntax"></a>구문

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))의 문법을 정의 합니다 `geo.distance` 고 `geo.intersects` 작동 하는 지리 공간 값 뿐만 아니라 함수:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

### <a name="geodistance"></a>geo.distance

`geo.distance` 형식의 두 매개 변수를 사용 하는 함수 `Edm.GeographyPoint` 반환 하 고는 `Edm.Double` 값 간의 거리를 킬로미터 단위로 거리입니다. 이 일반적으로 미터에서 거리를 반환 하는 OData 지리 공간 작업을 지 원하는 다른 서비스에서 서로 다릅니다.

매개 변수 중 하나 `geo.distance` 지리 지점 상수 이어야 합니다 하 고 필드 경로일 다른 해야 (필터의 경우 범위 변수 형식의 필드에 대해 반복 하거나 `Collection(Edm.GeographyPoint)`). 이러한 매개 변수의 순서는 중요 하지 않습니다.

Geography 소수점 상수는 `geography'POINT(<longitude> <latitude>)'`, 경도 및 위도 숫자 상수입니다.

> [!NOTE]
> 사용 하는 경우 `geo.distance` 필터에서 사용 하 여 상수 함수에서 반환한 거리 비교 해야 합니다 `lt`를 `le`합니다 `gt`, 또는 `ge`합니다. 연산자 `eq` 및 `ne`는 거리를 비교할 때 사용할 수 없습니다. 예를 들어,이 변수의 올바른 사용법 `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`합니다.

### <a name="geointersects"></a>geo.intersects

`geo.intersects` 함수는 형식의 변수에 `Edm.GeographyPoint` 과 상수 `Edm.GeographyPolygon` 반환 하 고는 `Edm.Boolean`  --  `true` 지점 다각형의 경계 내에 있으면 `false` 이 고, 그렇지.

다각형은 경계 링을 정의 하는 요소의 시퀀스로 저장 하는 2 차원 표면 (참조를 [예제](#examples) 아래). 다각형은 닫혀야 합니다. 즉, 첫 번째 점과 마지막 점 세트가 같아야 합니다. [다각형의 점은 시계 반대 방향 순서여야 합니다](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>지리 공간 쿼리 및 180th 자오선에 걸쳐 다각형

라이브러리 (near 날짜 변경 선) 180th 자오선을 포함 하는 쿼리를 작성 하는 작업은 여러 지리 공간 쿼리에 대 한 직접 또는 다각형의 자오선의 양쪽에 하나씩 두 개로 분할 하는 등 문제를 해결 해야 합니다.

Azure search에서 180도 경도 포함 하는 지리 공간 쿼리는 쿼리 형태 이며 사각형 좌표는 경도 및 위도 따라 하면 모눈 레이아웃에 정렬 하는 경우 예상 대로 작동 (예를 들어 `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). 그렇지 않은 경우 사각형이 아닌 도형이나 정렬되지 않은 도형의 경우 분할된 다각형 접근 방법을 고려해야 합니다.  

### <a name="geo-spatial-functions-and-null"></a>지리 공간 함수 및 `null`

Azure search에서는 형식의 필드에 다른 모든 비 컬렉션 필드와 같은 `Edm.GeographyPoint` 포함할 수 있습니다 `null` 값입니다. Azure Search를 평가할 때 `geo.intersects` 필드에 대 한 `null`, 결과 항상 `false`합니다. 동작 `geo.distance` 이 경우에 컨텍스트에 따라 달라 집니다.

- 필터에서 `geo.distance` 의 한 `null` 결과에서 필드 `null`합니다. 즉, 때문에 문서는 일치 하지 않습니다 `null` 평가 하는 값 모두 null이 아닌에 비해 `false`합니다.
- 사용 하 여 결과 정렬 하는 경우 **$orderby**를 `geo.distance` 의 한 `null` 가능한 최대 거리는 결과 필드입니다. 이러한 필드를 사용 하 여 문서 나머지 보다 낮은 순서로 정렬 됩니다 때 정렬 방향을 `asc` 됩니다 (기본값)를 사용 하 고 다른 모든 방향으로 하는 경우 보다 많을 `desc`합니다.

## <a name="examples"></a>예

### <a name="filter-examples"></a>필터 예제

지정된 된 참조점 10 킬로미터가 내의 모든 호텔 찾기 (위치는 형식의 필드 `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

다각형으로 설명 하는 지정한 뷰포트 내의 모든 호텔 찾기 (위치는 형식의 필드 `Edm.GeographyPoint`). 다각형은 닫혀 있으며(첫 번째 점과 마지막 점 세트가 같음) [점은 시계 반대 방향 순서로 나열되어야 합니다](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 예제

호텔을 기준으로 내림차순으로 정렬 `rating`, 그런 다음 지정 된 좌표에서 거리를 기준 오름차순:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

호텔 내림차순으로 정렬 `search.score` 및 `rating`, 및 다음 오름차순으로 거리를 지정 된 좌표에서 동일한 등급을 사용 하 여 두 개 호텔 간의 가장 가까운 것이 먼저 나열 되도록 합니다.

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
