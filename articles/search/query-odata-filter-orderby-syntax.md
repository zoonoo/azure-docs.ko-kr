---
title: OData 언어 개요-Azure Search
description: 필터에 대 한 OData 언어 개요, 선택한 by Azure Search 쿼리에 대 한 합니다.
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063695"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>에 대 한 OData 언어 개요 `$filter`하십시오 `$orderby`, 및 `$select` Azure Search에서

Azure Search의 OData 식 구문 하위 집합을 지원 **$filter**하십시오 **$orderby**, 및 **$select** 식. 필터 식은 쿼리 구문 분석 때 평가되고, 검색을 특정 필드로 제한하거나 인덱스 검색 때 사용되는 일치 조건을 추가합니다. Order by 식 결과 반환 되는 문서를 정렬 하려면 집합을 통해 사후 처리 단계로 적용 됩니다. Select 식 결과 집합에 포함 되는 문서 필드를 결정 합니다. 이러한 식의 구문은 구별 합니다 [간단한](query-simple-syntax.md) 또는 [전체](query-lucene-syntax.md) 쿼리 구문에 사용 되는 합니다 **검색** 매개 변수를 구문에서 중복 되는 부분이 있지만 필드를 참조 합니다.

이 문서에서는 필터, order by 및 select 식에 사용 된 OData 식 언어의 개요를 제공 합니다. 언어는 "아래에서 위로", 가장 기본적인 요소를 사용 하 여 시작 하 고 빌드에 표시 됩니다. 각 매개 변수에 대 한 최상위 구문은 별도 문서에서 설명 합니다.

- [$filter 구문](search-query-odata-filter.md)
- [$orderby 구문](search-query-odata-orderby.md)
- [$select 구문](search-query-odata-select.md)

OData 식 간단한에 이르는 매우 복잡 하지만 모든 공통 요소를 공유 합니다. Azure Search의 OData 식의 가장 기본적인 부분은 다음과 같습니다.

- **경로 필드**을 인덱스의 특정 필드를 나타냅니다.
- **상수**는 특정 데이터 형식의 리터럴 값입니다.

> [!NOTE]
> Azure Search의 용어에서 다른 합니다 [OData 표준을](https://www.odata.org/documentation/) 몇 가지 방식에서입니다. 이라고 하는 **필드** Azure Search에서 호출 되는 **속성** OData에서와 마찬가지로 **필드 경로** 비교 **속성 경로**. **인덱스** 포함 된 **문서** Azure Search에서 라고 하며 일반적으로 더으로 OData는 **엔터티 집합** 포함 된 **엔터티**합니다. Azure Search 용어는이 참조 전체에 사용 됩니다.

## <a name="field-paths"></a>필드 경로

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 문법 필드 경로 정의 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

하나 이상의 필드 경로 이루어집니다 **식별자** 슬래시로 구분 합니다. 각 식별자는 ASCII 문자 또는 밑줄로 시작 하 고 ASCII 문자, 숫자 또는 밑줄을 포함 하는 문자 시퀀스입니다. 위-소문자 또는 문자 수 있습니다.

식별자 또는 필드의 이름 중 하나를 참조할 수는 **범위 변수** 의 컨텍스트에서 [컬렉션 식](search-query-odata-collection-operators.md) (`any` 또는 `all`) 필터에서. 범위 변수는 컬렉션의 현재 요소를 나타내는 루프 변수 비슷합니다. 복잡 한 컬렉션에 대 한 해당 변수 때문 변수의 하위 필드를 참조 하려면 필드 경로 사용할 수 있는 개체를 나타냅니다. 이 대부분의 프로그래밍 언어에서 점 표기법 유사 합니다.

필드 경로의 예는 다음 표에 표시 됩니다.

| 필드 경로 | 설명 |
| --- | --- |
| `HotelName` | 인덱스의 최상위 필드 참조 |
| `Address/City` | 참조 된 `City` ; 인덱스의 복잡 한 필드 하위 필드 `Address` 유형의 `Edm.ComplexType` 이 예제 |
| `Rooms/Type` | 참조 된 `Type` ; 인덱스에 있는 복잡 한 컬렉션 필드의 하위 필드 `Rooms` 유형의 `Collection(Edm.ComplexType)` 이 예제 |
| `Stores/Address/Country` | 참조 하는 `Country` 하위 필드는 `Address` ; 인덱스에 있는 복잡 한 컬렉션 필드의 하위 필드 `Stores` 유형의 `Collection(Edm.ComplexType)` 하 고 `Address` 형식의 `Edm.ComplexType` 이 예제 |
| `room/Type` | 참조 하는 `Type` 하위 필드는 `room` 필터 식의 예를 들어 범위 변수 `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 참조를 `Country` 하위 필드를 `Address` 하위 필드는 `store` 필터 식의 예를 들어 범위 변수 `Stores/any(store: store/Address/Country eq 'Canada')` |

필드 경로의 의미는 컨텍스트에 따라 달라 집니다. 필터에 필드 경로 참조의 값을 *단일 인스턴스* 현재 문서에서 필드의 합니다. 다른 컨텍스트에서 같은 **$orderby**를 **$select**, 또는 [전체 Lucene 구문에서 필드 지정된 검색](query-lucene-syntax.md#bkmk_fields)에 필드 경로 자체 필드를 나타냅니다. 이러한 차이 필드 경로 필터에서 사용 되는 방법에 대 한 일부 결과

필드 경로 고려해 보겠습니다 `Address/City`합니다. 필터의 경우, "샌프란시스코"와 같은 현재 문서에 대 한 단일 도시를 나타냅니다. 반대로 `Rooms/Type` 가리킵니다는 `Type` 많은 방 (예: "standard"에 대 한 첫 번째 대화방에 "디럭스" 두 번째 공간, 및 등)에 대 한 하위 필드입니다. 이후 `Rooms/Type` 를 참조 하지 않습니다는 *단일 인스턴스* 하위 필드의 `Type`, 필터에서 직접 사용할 수 없습니다. 대신 방 종류를 필터링 하려면 사용 된 [람다 식](search-query-odata-collection-operators.md) 같이 범위 변수를 사용 하 여:

    Rooms/any(room: room/Type eq 'deluxe')

이 예제에서는 범위 변수의 `room` 에 표시 되는 `room/Type` 필드 경로입니다. 이런 방식으로 `room/Type` 현재 문서에서 현재 대화방의 유형을 나타냅니다. 이 단일 인스턴스의 `Type` 하위 필드를 필터에서 직접 사용할 수 있도록 합니다.

### <a name="using-field-paths"></a>필드 경로 사용 하 여

필드 경로의 많은 매개 변수에서 사용 되는 [Azure Search API](https://docs.microsoft.com/rest/api/searchservice/)합니다. 다음 표에 사용할 수 있는, 모든 위치 및 용도에 제한이 있습니다.

| API | 매개 변수 이름 | 제한 |
| --- | --- | --- |
| [만들](https://docs.microsoft.com/rest/api/searchservice/create-index) 나 [업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) 인덱스 | `suggesters/sourceFields` | 없음 |
| [만들](https://docs.microsoft.com/rest/api/searchservice/create-index) 나 [업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) 인덱스 | `scoringProfiles/text/weights` | 참조할 수 있습니다 **검색 가능한** 필드 |
| [만들](https://docs.microsoft.com/rest/api/searchservice/create-index) 나 [업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) 인덱스 | `scoringProfiles/functions/fieldName` | 참조할 수 있습니다 **필터링 가능** 필드 |
| [이를 통해 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` 때 `queryType` 는 `full` | 참조할 수 있습니다 **검색 가능한** 필드 |
| [이를 통해 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | 참조할 수 있습니다 **facetable** 필드 |
| [이를 통해 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | 참조할 수 있습니다 **검색 가능한** 필드 |
| [이를 통해 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | 참조할 수 있습니다 **검색 가능한** 필드 |
| [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) 고 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | 포함 된 필드를 참조할 수 있습니다는 [확인 기](index-add-suggesters.md) |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents)하십시오 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions), 및 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | 참조할 수 있습니다 **필터링 가능** 필드 |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) 고 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | 참조할 수 있습니다 **정렬 가능** 필드 |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents)하십시오 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions), 및 [조회](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | 참조할 수 있습니다 **검색할** 필드 |

## <a name="constants"></a>상수

Odata에서는 상수는 리터럴 값을 지정 [엔터티 데이터 모델](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) 형식입니다. 참조 [지원 되는 데이터 형식](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Azure Search에서 지원 되는 형식의 목록을입니다. 컬렉션 형식 상수는 지원 되지 않습니다.

다음 표에서 각 Azure Search에서 지원 되는 데이터 형식에 대 한 상수의 예를 보여 줍니다.

| 데이터 형식 | 예제에서는 상수 |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 위의 표에 표시 된 상수 중 대부분에 대 한 문법을 정의 합니다. 지역 공간 형식에 대 한 문법에서 찾을 수 있습니다 [Azure Search의 OData 지리 공간 함수](search-query-odata-geo-spatial-functions.md)합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

## <a name="building-expressions-from-field-paths-and-constants"></a>필드 경로를 상수 식 작성

필드 경로 및 상수 OData 식의 가장 기본적인 일부 이지만 전체 식 자체 이미 있습니다. 실제로 **$select** Azure Search에서 매개 변수 없는 필드 경로의 쉼표로 구분 된 목록 및 **$orderby** 보다 훨씬 더 복잡 하지 않습니다 **$select**합니다. 형식의 필드가 있는 경우 `Edm.Boolean` 인덱스에 작성할 수도 있습니다. 필터를 해당 필드의 경로 뿐입니다. 상수 `true` 고 `false` 유효한 필터에 구현 되어 있습니다.

그러나 대부분의 해야 둘 이상의 필드와 상수를 참조 하는 복잡 한 식입니다. 매개 변수에 따라 다른 방법으로 이러한 식은 작성할 수 있습니다.

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))에 대 한 문법을 정의 합니다 **$filter**를 **$orderby**, 및 **$select** 매개 변수입니다. 이러한 필드 경로 및 상수를 참조 하는 간단한 식에서 작성 됩니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

합니다 **$orderby** 하 고 **$select** 매개 변수는 모두 간단한 식의 쉼표로 구분 된 목록입니다. 합니다 **$filter** 매개 변수는 하위 식을 더 간단한 식의 구성 된 부울 식입니다. 와 같은 논리 연산자를 사용 하 여 이러한 하위 식을 결합 [ `and`, `or`, 및 `not` ](search-query-odata-logical-operators.md), 같은 비교 연산자 [ `eq`, `lt`합니다 `gt`등과](search-query-odata-comparison-operators.md), 및와 같은 컬렉션 연산자 [ `any` 하 고 `all` ](search-query-odata-collection-operators.md)합니다.

합니다 **$filter**를 **$orderby**, 및 **$select** 매개 변수를 다음 문서에서 자세히 살펴보겠습니다.

- [Azure Search의 OData $filter 구문](search-query-odata-filter.md)
- [Azure Search의 OData $orderby 구문](search-query-odata-orderby.md)
- [Azure Search의 OData $select 구문](search-query-odata-select.md)

## <a name="see-also"></a>참고 항목  

- [Azure Search의 패싯 탐색](search-faceted-navigation.md)
- [Azure Search의 필터](search-filters.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 쿼리 구문](query-lucene-syntax.md)
- [Azure Search의 단순 쿼리 구문](query-simple-syntax.md)
