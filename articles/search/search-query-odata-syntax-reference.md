---
title: OData 식 구문 참조-Azure Search
description: Azure Search 쿼리에 OData 식 공식 문법 및 구문 사양.
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
ms.openlocfilehash: cccfb749af07d1deeeda6e94de9c2cd5ce5396f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079666"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Azure Search에 대 한 OData 식 구문 참조

다음을 사용 하 여 azure Search [OData 식](http://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) API 전반에 걸쳐 매개 변수로 합니다. OData 식 가장 일반적으로 사용 되는 `$orderby` 고 `$filter` 매개 변수입니다. 이러한 식은 여러 절, 함수 및 연산자를 포함 하 복잡할 수 있습니다. 그러나 단순한 OData 식 같은 속성 경로 Azure Search REST API의 많은 부분에서 사용 됩니다. 예를 들어 경로 식 하는 데 하위 필드 목록에서 하위 필드 때와 같은 API의 어디에서 나 복잡 한 필드의 참조를 [suggester](index-add-suggesters.md), [점수 매기기 함수](index-add-scoring-profiles.md)의 `$select` 매개 변수 심지어 [Lucene 쿼리 검색 필드 지정된](query-lucene-syntax.md)합니다.

이 문서에서는 이러한 모든 형태의 OData 식에서는 정식 문법을 사용 하 여 설명 합니다. 또한는 [대화형 다이어그램](#syntax-diagram) 문법을 시각적으로 탐색할 수 있도록 합니다.

## <a name="formal-grammar"></a>정식 문법

EBNF를 사용 하 여 Azure Search에서 지 원하는 OData 언어의 하위 집합을 설명할 수 있습니다 ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 문법입니다. 규칙 "하향식", 가장 복잡 한 식으로 시작 해 서 더 기본 식으로 세분화 나열 됩니다. 맨 위에 있는 Azure Search REST API의 특정 매개 변수에 해당 하는 문법 규칙:

- [`$filter`](search-query-odata-filter.md)에서 정의한는 `filter_expression` 규칙입니다.
- [`$orderby`](search-query-odata-orderby.md)에서 정의한는 `order_by_expression` 규칙입니다.
- [`$select`](search-query-odata-select.md)에서 정의한는 `select_expression` 규칙입니다.
- 경로 의해 정의 된 필드는 `field_path` 규칙입니다. 필드 경로 API에서 사용 됩니다. 인덱스의 최상위 필드 중 하나 또는 하나 이상의 하위 필드를 참조할 수 있습니다 [복잡 한 필드](search-howto-complex-data-types.md) 상위 항목입니다.

EBNF를 찾아볼 수 있는 되 면 [구문 다이어그램](https://en.wikipedia.org/wiki/Syntax_diagram) 를 대화형으로 해당 규칙 간의 문법 및 관계를 탐색할 수 있도록 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>구문 다이어그램

Azure Search에서 지 원하는 OData 언어 문법을 시각적으로 탐색 하려면 대화형 구문 다이어그램을 시도 합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>참고 항목  

- [Azure Search의 필터](search-filters.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 쿼리 구문](query-lucene-syntax.md)
- [Azure Search의 단순 쿼리 구문](query-simple-syntax.md)
