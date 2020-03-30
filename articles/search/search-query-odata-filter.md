---
title: OData 필터 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 필터 식을 만드는 데 사용되는 OData 언어 참조 및 전체 구문입니다.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282888"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure 인지 검색에서 OData $filter 구문

Azure Cognitive [Search는 OData 필터 식을](query-odata-filter-orderby-syntax.md) 사용하여 전체 텍스트 검색어 외에 검색 쿼리에 추가 조건을 적용합니다. 이 문서에서는 필터 구문을 자세히 설명합니다. 필터의 기능 및 필터를 사용하여 특정 쿼리 시나리오를 실현하는 방법에 대한 자세한 내용은 [Azure Cognitive Search의 필터를](search-filters.md)참조하십시오.

## <a name="syntax"></a>구문

OData 언어의 필터는 부울 식으로, 다음 EBNF[(확장 백우스-Naur 양식)에](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)표시된 것처럼 여러 유형의 식 중 하나가 될 수 있습니다.

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

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

부울 식의 유형은 다음과 같습니다.

- 또는 `all`를 사용하여 `any` 컬렉션 필터 표현식을 컬렉션 필드에 필터 기준을 적용합니다. 자세한 내용은 [Azure 인지 검색의 OData 컬렉션 연산자](search-query-odata-collection-operators.md)를 참조하십시오.
- 연산자 및 `or` `not`을 사용하여 다른 부울 `and`식을 결합하는 논리적 식 자세한 내용은 [Azure 인지 검색의 OData 논리 연산자](search-query-odata-logical-operators.md)를 참조하십시오.
- 필드 또는 범위 변수를 `eq`연산자 , , `ne`및 `gt` `lt` `ge` `le`을 사용하여 상수 값과 비교하는 비교 식 자세한 내용은 [Azure 인지 검색의 OData 비교 연산자](search-query-odata-comparison-operators.md)를 참조하십시오. 비교 표현식은 `geo.distance` 함수를 사용하여 지리 공간 좌표 사이의 거리를 비교하는 데도 사용됩니다. 자세한 내용은 [Azure 인지 검색의 OData 지리적 공간 함수를](search-query-odata-geo-spatial-functions.md)참조하십시오.
- 부울 리터럴 `true` 및 `false`. 이러한 상수는 프로그래밍 방식으로 필터를 생성하는 경우에 유용할 수 있지만 실제로는 사용되지 않는 경우가 많습니다.
- 다음과 같은 부울 함수에 대한 호출:
  - `geo.intersects`을 통해 지정된 지점이 지정된 다각형 내에 있는지 여부를 테스트합니다. 자세한 내용은 [Azure 인지 검색의 OData 지리적 공간 함수를](search-query-odata-geo-spatial-functions.md)참조하십시오.
  - `search.in`을 클릭하여 필드 또는 범위 변수를 값 목록의 각 값과 비교합니다. 자세한 내용은 [Azure 인지 `search.in` 검색의 OData 함수를](search-query-odata-search-in-function.md)참조하십시오.
  - `search.ismatch`및 `search.ismatchscoring`을 사용하여 전체 텍스트 검색 작업을 필터 컨텍스트에서 실행합니다. 자세한 내용은 [Azure 인지 검색의 OData 전체 텍스트 검색 기능을](search-query-odata-full-text-search-functions.md)참조하십시오.
- 필드 경로 또는 형식의 `Edm.Boolean`범위 변수입니다. 예를 들어 인덱스에 부울 필드가 `IsEnabled` 호출되어 있고 이 필드가 `true`있는 모든 문서를 반환하려는 `IsEnabled`경우 필터 표현식은 이름일 수 있습니다.
- 괄호 안의 부울 표현식입니다. 괄호를 사용하면 필터에서 작업의 순서를 명시적으로 결정하는 데 도움이 될 수 있습니다. OData 연산자의 기본 우선 순위에 대한 자세한 내용은 다음 섹션을 참조하십시오.

### <a name="operator-precedence-in-filters"></a>필터의 연산자 우선 순위

하위 식 주위에 괄호가 없는 필터 식을 작성하는 경우 Azure Cognitive Search는 연산자 우선 순위 규칙 집합에 따라 이를 평가합니다. 이러한 규칙은 하위 식을 결합하는 데 사용되는 연산자기준입니다. 다음 표에는 가장 높은 우선 순위부터 가장 낮은 우선 순위의 연산자 그룹이 나열됩니다.

| 그룹 | 연산자 |
| --- | --- |
| 논리 연산자 | `not` |
| 비교 연산자 | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| 논리 연산자 | `and` |
| 논리 연산자 | `or` |

위 표에서 높은 연산자는 다른 연산자보다 해당 산각에 "더 단단히 바인딩"됩니다. 예를 들어 `and` 은 보다 `or`우선 순위가 높고 비교 연산자는 둘 중 하나보다 우선 순위가 높기 때문에 다음 두 식은 동일합니다.

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

연산자는 `not` 비교 연산자보다 훨씬 높은 우선 순위를 갖습니다. 이 같은 필터를 작성하려고 하면 다음과 같은 이유가 있습니다.

    not Rating gt 5

이 오류 메시지가 나타납니다.

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

이 오류는 연산자가 전체 `Rating` 비교 식이 아닌 `Edm.Int32`형식의 필드와 만 연결되어 있기 때문에 발생합니다. 수정 은 괄호 안에 피연산을 `not` 두는 것입니다.

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>필터 크기 제한

Azure Cognitive Search에 보낼 수 있는 필터 식의 크기와 복잡성에는 제한이 있습니다. 이러한 제한은 대략적으로 필터 식의 절 수와 관련이 있습니다. 좋은 지침은 수백 개의 절이 있는 경우 제한을 초과할 위험이 있다는 것입니다. 무한 한 크기의 필터를 생성 하지 않는 방식으로 응용 프로그램을 디자인 하는 것이 좋습니다.

> [!TIP]
> 함수 [ `search.in` 호출이](search-query-odata-search-in-function.md) 단일 절로 계산되므로 같음 비교의 긴 분리 대신 함수를 사용하면 필터 절 제한을 피할 수 있습니다.

## <a name="examples"></a>예

기본 요금이 $200 미만인 객실이 하나 이상인 모든 호텔을 4 등급 이상 평가한 호텔 찾기:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

2010년부터 리뉴얼된 "씨뷰 모텔" 이외의 모든 호텔 을 검색하세요.

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

2010년 이후에 리노베이션된 모든 호텔을 찾아보십시오. 날짜 시간 리터럴에는 태평양 표준시의 표준 시간대 정보가 포함됩니다.  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

주차 공간이 있는 모든 호텔과 모든 객실이 금연 구역인 곳 찾기:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- 또는 -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

럭셔리 호텔이거나 주차장이 포함되어 있고 등급이 5인 모든 호텔을 찾습니다.  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

적어도 하나의 방에서 "wifi"라는 태그가있는 모든 호텔을 찾으십시오 (각 `Collection(Edm.String)` 방에는 필드에 태그가 저장되어 있음)  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

모든 객실이 있는 모든 호텔 찾기:  

    $filter=Rooms/any()

객실이 없는 모든 호텔 찾기:

    $filter=not Rooms/any()

지정된 기준점에서 10km 이내에 있는 `Location` 모든 호텔 찾기(유형 `Edm.GeographyPoint`필드위치):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

다각형으로 설명된 지정된 뷰포트 내에서 모든 `Location` 호텔을 찾습니다(Edm.GeographyPoint 형식필드). 다각형은 닫아야 하며, 첫 번째 점과 마지막 점 집합은 같아야 합니다. 또한 [포인트는 시계 반대 방향으로 나열되어야 합니다.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

"설명" 필드가 null인 모든 호텔을 찾습니다. 필드가 설정되지 않았거나 null로 명시적으로 설정된 경우 null이 됩니다.  

    $filter=Description eq null

'씨뷰 모텔' 또는 '저예산 호텔'과 같은 이름을 가진 모든 호텔을 검색합니다. 이러한 구문에는 공백이 포함되며 공백은 기본 구분 기호입니다. 단일 따옴표에서 대체 구분 기호를 세 번째 문자열 매개 변수로 지정할 수 있습니다.  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

'씨뷰 모텔' 또는 '저예산 호텔'과 '|'로 구분된 모든 호텔 찾기:  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

모든 객실에 'wifi' 또는 '욕조'라는 태그가 있는 모든 호텔을 검색합니다.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

태그에 '열수 수건 걸이' 또는 '헤어드라이어 포함'과 같은 컬렉션 내의 문구에 일치하는 내용을 찾습니다.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

"waterfront" 단어를 포함하는 문서를 찾습니다. 이 필터 쿼리는 `search=waterfront`를 사용한 [검색 요청](https://docs.microsoft.com/rest/api/searchservice/search-documents)과 동일합니다.

    $filter=search.ismatchscoring('waterfront')

단어 "hostel"을 포함하고 등급이 4 이상인 문서 또는 단어 "motel"을 포함하고 등급이 5인 문서를 찾습니다. 이 요청은 을 사용하여 `search.ismatchscoring` `or`전체 텍스트 검색과 필터 작업을 결합하기 때문에 함수 없이는 표현할 수 없습니다.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

단어 "luxury"가 없는 문서를 찾습니다.

    $filter=not search.ismatch('luxury')

구 "ocean view"를 포함하거나 등급이 5인 문서를 찾습니다. `search.ismatchscoring` 쿼리는 필드 `HotelName` 및 `Description`에 대해서만 실행됩니다. 분리 명령의 두 번째 조항과 일치하는 문서도 반환됩니다 `Rating` - 5와 같은 호텔. 이러한 문서는 0과 동일한 점수로 반환되어 표현식의 점수가 매겨지는 부분과 일치하지 않는다는 것을 분명히 합니다.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

"호텔"과 "공항"이라는 용어가 설명에서 5단어 이하이고 모든 객실이 금연인 호텔을 찾아보십시오. 이 쿼리는 [전체 Lucene 쿼리 언어](query-lucene-syntax.md)를 사용합니다.

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
