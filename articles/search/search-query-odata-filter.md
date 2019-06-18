---
title: OData 필터 참조-Azure Search
description: Azure Search 쿼리에 필터 구문은 OData 언어 참조입니다.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079913"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Azure Search의 OData $filter 구문

Azure Search를 사용 하 여 [OData 필터 식을](query-odata-filter-orderby-syntax.md) 전체 텍스트 검색 조건 외에도 검색 쿼리에 추가 기준을 적용 합니다. 이 문서에서는 자세히 필터의 구문을 설명 합니다. 필터 이란 무엇 이며 특정 쿼리 시나리오를 실현 하는 데 사용 하는 방법에 대 한 일반적인 내용은 참조 하세요. [Azure Search의 필터](search-filters.md)합니다.

## <a name="syntax"></a>구문

OData 언어에서 필터는 차례로 수 있는 식의 여러 유형 중 하나가 다음 EBNF에 표시 된 대로 부울 식 ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

부울 식의 유형은 다음과 같습니다.

- 사용 하 여 컬렉션 필터 식을 `any` 또는 `all`합니다. 이러한 컬렉션 필드에 필터 조건을 적용 합니다. 자세한 내용은 [Azure Search의 OData 컬렉션 연산자](search-query-odata-collection-operators.md)합니다.
- 연산자를 사용 하 여 다른 부울 식을 결합 하는 논리 식을 `and`, `or`, 및 `not`합니다. 자세한 내용은 [Azure Search의 OData 논리 연산자](search-query-odata-logical-operators.md)합니다.
- 필드를 비교 하거나 범위 연산자를 사용 하 여 상수 값 변수 비교 식 `eq`, `ne`를 `gt`를 `lt`를 `ge`, 및 `le`합니다. 자세한 내용은 [Azure Search의 OData 비교 연산자](search-query-odata-comparison-operators.md)합니다. 비교 식은 또한 비교 하는 데 사용 하 여 지리 공간 좌표 사이의 거리를 `geo.distance` 함수입니다. 자세한 내용은 [Azure Search의 OData 지리 공간 함수](search-query-odata-geo-spatial-functions.md)합니다.
- 부울 리터럴은 `true` 고 `false`입니다. 이러한 상수 때 유용할 수 있습니다 때때로 프로그래밍 방식으로 필터를 생성 하지만 그렇지 않으면 없는 실제로 사용할 수 있습니다.
- 부울 함수를 포함 하 여 호출 합니다.
  - `geo.intersects`에 주어진 점이 주어진된 다각형 여부를 테스트 하는 합니다. 자세한 내용은 [Azure Search의 OData 지리 공간 함수](search-query-odata-geo-spatial-functions.md)합니다.
  - `search.in`에 값 목록의 각 값을 사용 하 여 필드나 범위의 변수를 비교 합니다. 자세한 내용은 [OData `search.in` 함수를 Azure Search에서](search-query-odata-search-in-function.md)합니다.
  - `search.ismatch` 및 `search.ismatchscoring`, 필터 컨텍스트에 전체 텍스트 검색 작업을 실행 하는 합니다. 자세한 내용은 [Azure Search에서 전체 텍스트 검색 기능 OData](search-query-odata-full-text-search-functions.md)합니다.
- 경로 필드 또는 범위 변수 형식의 `Edm.Boolean`합니다. 예를 들어 인덱스에 라는 부울 필드가 `IsEnabled` 여기서이 필드는 모든 문서를 반환 하려는 `true`, 필터 식의 이름일 수만 `IsEnabled`합니다.
- 괄호 안에 부울 식입니다. 괄호를 사용 하 여 명시적으로 필터에서 연산의 순서를 알 수 있습니다. OData 연산자의 기본 우선 순위에 자세한 내용은 다음 섹션을 참조 하세요.

### <a name="operator-precedence-in-filters"></a>필터에 연산자 우선 순위

해당 하위 식 주위에 없는 괄호를 사용 하 여 필터 식에서 작성 하는 경우 Azure Search의 연산자 선행 규칙 집합에 따라 평가 됩니다. 이러한 규칙은 하위 식을 결합 하는 연산자를 사용 하는 기반으로 합니다. 다음 표에서 가장 낮은 우선 순위에서 높은 순서 대로 연산자의 그룹을 나열합니다.

| 그룹 | 운영자 |
| --- | --- |
| 논리 연산자 | `not` |
| 비교 연산자 | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| 논리 연산자 | `and` |
| 논리 연산자 | `or` |

위의 테이블에서 상위 수준으로 운영자는 "바인딩합니다 더 밀접 하 게" 연산자 보다 피연산자를 합니다. 예를 들어 `and` 보다 더 높은 우선 순위는 `or`, 및 비교 연산자, 둘 중 하나 보다 더 높은 우선 순위는 다음 두 식은 동일 하므로:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` 연산자는 우선 순위가 높은 모든-비교 연산자 보다 훨씬 높은 합니다. 이유는 바로 이와 같은 필터를 작성 하려는 경우:

    not Rating gt 5

이 오류 메시지가 표시 됩니다.

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

연산자를 사용 하 여 연결 이므로이 오류는 발생 요소만 `Rating` 형식인 필드 `Edm.Int32`, 전체 비교 식 관련이 없습니다. 피연산자에 적용할 수정 프로그램은 `not` 괄호 안에 있음:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>필터 크기 제한

Azure Search로 보낼 수 있는 필터 식의 크기 및 복잡성은 제한되어 있습니다. 이러한 제한은 대략적으로 필터 식의 절 수와 관련이 있습니다. 좋은 지침은 수백 개의 절에 있는 경우 있습니다 제한을 초과할 위험이 있습니다. 무제한 크기의 필터를 생성 하지 않습니다 하는 방식으로 응용 프로그램을 디자인 하는 것이 좋습니다.

> [!TIP]
> 사용 하 여 [는 `search.in` 함수](search-query-odata-search-in-function.md) 긴 식의 일치 하는 대신 비교를 방지할 수 있습니다 필터 절 제한 되므로 함수 호출을 단일 절으로 계산 합니다.

## <a name="examples"></a>예

$200 등급이 4 이상인 보다 작은 기본 요금을 사용 하 여 하나 이상의 공간이 있는 모든 호텔 찾기:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

2010 이후에 리모델링한 "Sea 보기 Motel" 이외의 모든 호텔 찾기:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

된 호텔이 2010 이상에서 하는 모든 호텔 찾기 Datetime 리터럴에 태평양 표준시 표준 시간대 정보가 포함 됩니다.  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

포함 된 주차장이 있는 모든 호텔 찾기 및 모든 대화방이 있는 비 번호:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- 또는  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

럭셔리 호텔이거나 주차장이 포함되어 있고 등급이 5인 모든 호텔을 찾습니다.  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

하나 이상의 방에 "wifi" 태그가 있는 모든 호텔 찾기 (각 공간에 저장 하는 태그에는 `Collection(Edm.String)` 필드).  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

모든 방 모든 호텔 찾기:  

    $filter=Rooms/any()

대화방 없는 모든 호텔 찾기:

    $filter=not Rooms/any()

지정된 된 참조점 10 킬로미터가 내의 모든 호텔 찾기 (여기서 `Location` 형식의 필드임 `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

다각형으로 설명 하는 지정한 뷰포트 내의 모든 호텔 찾기 (위치 `Location` Edm.GeographyPoint 형식의 필드임). 다각형을 반드시 닫고, 즉 첫 번째 및 마지막 점 집합 동일 해야 합니다. 또한 [점을 시계 반대 방향 순서로 나열 되어야 합니다](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)합니다.

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

"설명" 필드를 null 인 모든 호텔 찾기 필드는 null 일 설정 되지 않은 경우 명시적으로 설정 된 경우 null로:  

    $filter=Description eq null

모든 호텔 찾기 이름의 '바다 보기 motel' 또는 '예산 호텔' 같음). 이 구를 공백이 있고 공간이 기본 구분 기호. 세 번째 문자열 매개 변수로 작은따옴표에서는 다른 구분 기호를 지정할 수 있습니다.  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

이름의 모든 호텔을 '바다 보기 motel' 또는 '예산 호텔'으로 구분 된 같음 찾기 ' |').  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

모든 대화방이 태그 'wifi' 또는 '면'에 있는 모든 호텔 찾기:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

태그에 '가 열된 수건 랙' 또는 '헤어 드라이기 포함'와 같은 컬렉션 내의 구 일치를 찾습니다.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

"waterfront" 단어를 포함하는 문서를 찾습니다. 이 필터 쿼리는 `search=waterfront`를 사용한 [검색 요청](https://docs.microsoft.com/rest/api/searchservice/search-documents)과 동일합니다.

    $filter=search.ismatchscoring('waterfront')

단어 "hostel"을 포함하고 등급이 4 이상인 문서 또는 단어 "motel"을 포함하고 등급이 5인 문서를 찾습니다. 이 요청 하지 않고 표현할 수 없습니다는 `search.ismatchscoring` 함수를 사용 하 여 필터 작업을 사용 하 여 전체 텍스트 검색을 결합 하는 것 이므로 `or`합니다.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

단어 "luxury"가 없는 문서를 찾습니다.

    $filter=not search.ismatch('luxury')

구 "ocean view"를 포함하거나 등급이 5인 문서를 찾습니다. `search.ismatchscoring` 쿼리는 필드 `HotelName` 및 `Description`에 대해서만 실행됩니다. 분리 된 두 번째 절만 일치 하는 문서를 반환할 너무-호텔 `Rating` 5입니다. 점수 있도록 0을 사용 하 여 해당 문서를 반환할 일치 하지 않은 모든 식의 점수가 매겨진된 파트의 선택을 취소 합니다.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

여기서는 "호텔" 및 "공항"는 5 개 이하의 단어 설명에서 분리 및 모든 대화방이 아닌 번호는 호텔을 찾습니다. 이 쿼리는 [전체 Lucene 쿼리 언어](query-lucene-syntax.md)를 사용합니다.

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
