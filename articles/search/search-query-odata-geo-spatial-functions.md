---
title: OData 지리 공간 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 쿼리에서 OData 지리적 공간 함수, geo.distance 및 geo.intersects를 사용하기 위한 구문 및 참조 설명서입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113167"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure 인지 검색의 OData 지리적 `geo.distance` 공간 함수 -`geo.intersects`

Azure Cognitive Search는 `geo.distance` 및 `geo.intersects` 기능을 통해 [OData 필터 표현식에서](query-odata-filter-orderby-syntax.md) 지리적 공간 쿼리를 지원합니다. 이 `geo.distance` 함수는 필드 또는 범위 변수인 두 점 사이의 거리를 반환하고 하나는 필터의 일부로 전달되는 상수입니다. 지정된 `geo.intersects` 지점이 지정된 다각형 내에 있는 경우 함수가 반환되며, `true` 여기서 점은 필드 또는 범위 변수이고 다각형은 필터의 일부로 전달되는 상수로 지정됩니다.

이 `geo.distance` 함수는 [ **$orderby** 매개 변수에서](search-query-odata-orderby.md) 지정된 지점에서의 거리별로 검색 결과를 정렬하는 데 사용할 수도 있습니다. **$orderby**에서 `geo.distance`에 대한 구문은 **$filter**에 있을 때와 같습니다. $orderby `geo.distance` 사용할 **$orderby**때 적용되는 필드는 형식이어야 `Edm.GeographyPoint` 하며 **정렬 가능해야**합니다.

> [!NOTE]
> $orderby `geo.distance` 매개 **$orderby** 변수에서 사용하는 경우 함수에 전달하는 필드에는 단일 지리적 점만 포함되어야 합니다. 즉, 형식이어야 `Edm.GeographyPoint` 하며. `Collection(Edm.GeographyPoint)` Azure 인지 검색에서 컬렉션 필드를 정렬할 수 없습니다.

## <a name="syntax"></a>구문

다음 EBNF(확장[백투스-Naur Form)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `geo.distance` 함수의 `geo.intersects` 문법과 작동되는 지리적 공간 값을 정의합니다.

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

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

### <a name="geodistance"></a>지리 거리

함수는 `geo.distance` 형식의 `Edm.GeographyPoint` 두 매개변수를 `Edm.Double` 취하고 킬로미터에서 둘 사이의 거리인 값을 반환합니다. 이는 일반적으로 미터로 거리를 반환하는 OData 지리 공간 작업을 지원하는 다른 서비스와 다릅니다.

매개 변수 중 `geo.distance` 하나는 지리 점 상수여야 하고 다른 매개 변수는 필드 경로(또는 형식 `Collection(Edm.GeographyPoint)`필드를 반복하는 필터의 경우 범위 변수)여야 합니다. 이러한 매개 변수의 순서는 중요하지 않습니다.

지리점 상수는 경도와 `geography'POINT(<longitude> <latitude>)'`위도가 숫자 상수인 형식입니다.

> [!NOTE]
> 필터에서 `geo.distance` 사용할 때 함수에서 반환되는 `lt`거리를 `le`"를 `gt` `ge`사용하여 상수와 비교해야 합니다. 연산자 `eq` 및 `ne`는 거리를 비교할 때 사용할 수 없습니다. 예를 들어, 이것은 : `geo.distance` `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`의 올바른 사용입니다.

### <a name="geointersects"></a>geo.교차

`geo.intersects` 함수는 `Edm.GeographyPoint` 형식과 `Edm.GeographyPolygon` 상수의 변수를 `Edm.Boolean`  --  `true` 취하고 포인트가 다각형의 `false` 경계 내에 있는 경우 를 반환합니다.

다각형은 경계 링을 정의하는 점의 시퀀스로 저장된 2차원 [서피스입니다(아래 예제](#examples) 참조). 다각형은 닫혀야 합니다. 즉, 첫 번째 점과 마지막 점 세트가 같아야 합니다. [다각형의 점은 시계 반대 방향 순서여야 합니다](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>180번째 자오선에 걸친 지리 공간 쿼리 및 다각형

180번째 자오선(날짜 줄 근처)을 포함하는 쿼리를 공식화하는 많은 지리 공간 쿼리 라이브러리의 경우 제한이 없거나 다각형을 자오선의 양쪽에 하나씩 두 개로 분할하는 등의 해결 방법을 필요로 합니다.

Azure Cognitive Search에서 180도 경도를 포함하는 지리 공간 쿼리는 쿼리 셰이프가 직사각형이고 좌표가 경도와 위도(예: `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`위도)를 따라 그리드 레이아웃에 정렬되는 경우 예상대로 작동합니다. 그렇지 않은 경우 사각형이 아닌 도형이나 정렬되지 않은 도형의 경우 분할된 다각형 접근 방법을 고려해야 합니다.  

### <a name="geo-spatial-functions-and-null"></a>지리 공간 기능 및`null`

Azure Cognitive Search의 다른 모든 비컬렉션 필드와 마찬가지로 형식필드에는 `Edm.GeographyPoint` 값이 포함될 `null` 수 있습니다. Azure Cognitive Search가 `geo.intersects` `null`필드를 평가하는 경우 결과는 항상 `false`됩니다. 이 경우의 `geo.distance` 동작은 컨텍스트에 따라 다릅니다.

- 필터에서 `geo.distance` `null` 필드의 결과는 `null`. 즉, null이 아닌 `null` 값과 비교하여 에 대해 평가하기 때문에 문서가 일치하지 않습니다. `false`
- `null` 필드의 **$orderby** `geo.distance` 사용하여 결과를 정렬할 때 가능한 최대 거리가 생성됩니다. 정렬 방향(기본값)이 `asc` 사용될 때 이러한 필드가 있는 문서는 다른 모든 문서보다 낮게 정렬되고 방향이 다른 모든 문서보다 높게 정렬됩니다. `desc`

## <a name="examples"></a>예

### <a name="filter-examples"></a>필터 예제

지정된 기준점에서 10km 이내에 있는 모든 호텔 찾기(위치가 유형 `Edm.GeographyPoint`필드인 경우):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

다각형으로 설명된 지정된 뷰포트 내에서 모든 호텔을 찾습니다(위치가 유형 `Edm.GeographyPoint`필드인 경우). 다각형은 닫혀 있으며(첫 번째 점과 마지막 점 세트가 같음) [점은 시계 반대 방향 순서로 나열되어야 합니다](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 예제

지정된 좌표에서 `rating`의 거리별로 오름차순으로 내려오는 호텔을 정렬합니다.

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

내림차순으로 `search.score` `rating`호텔을 정렬한 다음 지정된 좌표에서 의 거리별로 오름차순으로 호텔을 정렬하여 동일한 등급을 가진 두 호텔 사이에 가장 가까운 호텔이 먼저 나열되도록 합니다.

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
