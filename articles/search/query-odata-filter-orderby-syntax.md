---
title: OData 언어 개요
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 쿼리에 대한 필터, 선택 및 순서별에 대한 OData 언어 개요입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153879"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>및 Azure 인지 `$filter` `$orderby` `$select` 검색에서 OData 언어 개요

Azure Cognitive Search는 **$filter,** **$orderby**및 **$select** 식에 대한 OData 식 구문의 하위 집합을 지원합니다. 필터 식은 쿼리 구문 분석 때 평가되고, 검색을 특정 필드로 제한하거나 인덱스 검색 때 사용되는 일치 조건을 추가합니다. 순서별 표현식은 반환되는 문서를 정렬하기 위해 결과 집합에 대한 사후 처리 단계로 적용됩니다. 표현식을 선택하면 결과 집합에 포함되는 문서 필드가 결정됩니다. 이러한 식의 구문은 필드를 참조하기 위한 구문에 일부 겹치지만 **검색** 매개 변수에 사용되는 [단순](query-simple-syntax.md) 또는 [전체](query-lucene-syntax.md) 쿼리 구문과 구별됩니다.

이 문서에서는 필터, 순서별 및 선택 식에 사용되는 OData 표현식 언어에 대한 개요를 제공합니다. 언어는 가장 기본적인 요소로 시작하여 그 위에 구축, "상향식"을 제시한다. 각 매개 변수에 대한 최상위 구문은 별도의 문서에서 설명합니다.

- [$filter 구문](search-query-odata-filter.md)
- [$orderby 구문](search-query-odata-orderby.md)
- [$select 구문](search-query-odata-select.md)

OData 표현식은 단순에서 매우 복잡한 것까지 다양하지만 모두 공통 요소를 공유합니다. Azure 인지 검색에서 OData 식의 가장 기본적인 부분은 다음과 같습니다.

- 인덱스의 특정 필드를 참조하는 **필드 경로입니다.**
- **상수**- 특정 데이터 형식의 리터럴 값입니다.

> [!NOTE]
> Azure 인지 검색의 용어는 몇 가지 방법으로 [OData 표준과](https://www.odata.org/documentation/) 다릅니다. Azure Cognitive Search에서 필드를 호출하는 **필드를** OData의 **속성이라고** 하며 필드 **경로와** **속성 경로에**대해서도 비슷합니다. Azure Cognitive Search에 **있는 문서를** 포함하는 **인덱스를** OData에서 엔터티를 포함하는 **엔터티 집합으로** 보다 일반적으로 지칭됩니다. **entities** Azure 인지 검색 용어는 이 참조 전체에서 사용됩니다.

## <a name="field-paths"></a>필드 경로

다음 EBNF(확장[백투스-나우어 양식)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)필드 경로의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

필드 경로는 슬래시로 구분된 하나 이상의 **식별자로** 구성됩니다. 각 식별자는 ASCII 문자 또는 밑줄로 시작해야 하며 ASCII 문자, 숫자 또는 밑줄만 포함해야 하는 문자 시퀀스입니다. 문자는 대문자 또는 소문자일 수 있습니다.

식별자는 필터에서 컬렉션 식(또는)의`any` `all`컨텍스트에서 필드 이름 또는 [collection expression](search-query-odata-collection-operators.md) **범위 변수를** 참조할 수 있습니다. 범위 변수는 컬렉션의 현재 요소를 나타내는 루프 변수와 같습니다. 복잡한 컬렉션의 경우 해당 변수는 개체를 나타내므로 필드 패스를 사용하여 변수의 하위 필드를 참조할 수 있습니다. 이는 많은 프로그래밍 언어의 점 표기와 유사합니다.

필드 경로의 예는 다음 표에 나와 있습니다.

| 필드 경로 | 설명 |
| --- | --- |
| `HotelName` | 인덱스의 최상위 필드를 참조합니다. |
| `Address/City` | 인덱스에서 `City` 복잡한 필드의 하위 필드를 나타냅니다. `Address` 이 예제에서 형식에 `Edm.ComplexType` 해당합니다. |
| `Rooms/Type` | 인덱스에서 `Type` 복잡한 컬렉션 필드의 하위 필드를 참조합니다. `Rooms` 이 예제에서 형식에 `Collection(Edm.ComplexType)` 해당합니다. |
| `Stores/Address/Country` | 인덱스에서 `Country` 복합 컬렉션 필드의 `Address` 하위 필드의 하위 필드를 참조합니다. `Stores` 이 예제에서는 `Address` 형식이 `Edm.ComplexType` `Collection(Edm.ComplexType)` 있고 형식입니다. |
| `room/Type` | `room` 범위 변수의 `Type` 하위 필드(예: 필터 식)를 참조합니다.`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 범위 변수의 `Country` `Address` 하위 필드의 하위 필드를 참조 합니다., 예를 들어 필터 식에서 `store``Stores/any(store: store/Address/Country eq 'Canada')` |

필드 경로의 의미는 컨텍스트에 따라 다릅니다. 필터에서 필드 경로는 현재 문서에서 필드의 *단일 인스턴스* 값을 나타냅니다. **$orderby**, **$select**와 같은 다른 컨텍스트에서 또는 [전체 Lucene 구문에서 필드 검색에서](query-lucene-syntax.md#bkmk_fields)필드 경로는 필드 자체를 참조합니다. 이러한 차이는 필터에서 필드 경로를 사용하는 방법에 몇 가지 영향을 미칩니다.

필드 경로를 `Address/City`고려하십시오. 필터에서 이 필터는 "샌프란시스코"와 같이 현재 문서의 단일 도시를 나타냅니다. 반대로 많은 `Rooms/Type` 룸의 `Type` 하위 필드(예: 첫 번째 룸의 "표준", 두 번째 룸의 "디럭스" 등)를 나타냅니다. 하위 `Rooms/Type` 필드의 `Type` *단일 인스턴스를* 참조하지 않으므로 필터에서 직접 사용할 수 없습니다. 대신 룸 유형을 필터링하려면 다음과 같이 범위 변수가 있는 [lambda 식을](search-query-odata-collection-operators.md) 사용합니다.

    Rooms/any(room: room/Type eq 'deluxe')

이 예제에서는 범위 `room` 변수가 `room/Type` 필드 경로에 나타납니다. 이렇게 하면 `room/Type` 현재 문서의 현재 룸 유형을 나타냅니다. 이 인스턴스는 `Type` 하위 필드의 단일 인스턴스이므로 필터에서 직접 사용할 수 있습니다.

### <a name="using-field-paths"></a>필드 경로 사용

필드 경로는 Azure 인지 검색 [REST API의](https://docs.microsoft.com/rest/api/searchservice/)많은 매개 변수에 사용됩니다. 다음 표에는 사용할 수 있는 모든 장소와 사용 제한 사항이 나열되어 있습니다.

| API | 매개 변수 이름 | 제한 |
| --- | --- | --- |
| 인덱스 [만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 또는 [업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) | `suggesters/sourceFields` | None |
| 인덱스 [만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 또는 [업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | **검색 가능한** 필드만 참조할 수 있습니다. |
| 인덱스 [만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 또는 [업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | **필터링 가능한** 필드만 참조할 수 있습니다. |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`있는 `queryType` 경우`full` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | **면이 있는** 필드만 참조할 수 있습니다. |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) 및 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | [제안자의](index-add-suggesters.md) 일부인 필드만 참조할 수 있습니다. |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents), [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions)및 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | **필터링 가능한** 필드만 참조할 수 있습니다. |
| [검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) 및 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | **정렬 가능한** 필드만 참조할 수 있습니다. |
| [검색,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions)및 [조회](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | **검색 가능한** 필드만 참조할 수 있습니다. |

## <a name="constants"></a>상수

OData의 상수는 지정된 [EDM(엔터티 데이터 모델)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) 형식의 리터럴 값입니다. Azure 인지 [검색에서](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) 지원되는 형식 목록에 대한 지원되는 데이터 형식을 참조하십시오. 컬렉션 형식의 상수는 지원되지 않습니다.

다음 표에서는 Azure Cognitive Search에서 지원하는 각 데이터 형식에 대한 상수 예제를 보여 주며 있습니다.

| 데이터 형식 | 상수 예 |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>문자열 상수에서 특수 문자 이스케이프

OData의 문자열 상수는 따옴표로 구분됩니다. 따옴표를 포함할 수 있는 문자열 상수로 쿼리를 생성해야 하는 경우 포함된 따옴표를 두 배로 늘려서 이스케이프할 수 있습니다.

예를 들어 "Alice의 자동차"와 같은 형식이 지정되지 않은 아포스트로피가 있는 구는 `'Alice''s car'`OData에서 문자열 상수로 표시됩니다.

> [!IMPORTANT]
> 프로그래밍 방식으로 필터를 생성할 때는 사용자 입력에서 오는 문자열 상수를 이스케이프하는 것이 중요합니다. 이는 특히 필터를 사용하여 [보안 트리밍을](search-security-trimming-for-azure-search.md)구현할 때 [주입 공격의](https://wikipedia.org/wiki/SQL_injection)가능성을 완화하기 위한 것입니다.

### <a name="constants-syntax"></a>상수 구문

다음 EBNF(확장[백투스-Naur 양식)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)위의 표에 표시된 대부분의 상수에 대한 문법을 정의합니다. 지리적 공간 형식에 대한 문법은 [Azure 인지 검색의 OData 지리적 공간 함수에서](search-query-odata-geo-spatial-functions.md)찾을 수 있습니다.

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

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

## <a name="building-expressions-from-field-paths-and-constants"></a>필드 경로 및 상수에서 표현식 작성

필드 경로와 상수는 OData 식의 가장 기본적인 부분이지만 이미 전체 표현식 입니다. 실제로 Azure Cognitive Search의 **$select** 매개 변수는 쉼표로 구분된 필드 경로 목록에 불과하며 **$orderby** **$select**것보다 훨씬 복잡하지 않습니다. 인덱스에 형식 `Edm.Boolean` 필드가 있는 경우 해당 필드의 경로에 불과한 필터를 작성할 수도 있습니다. 상수와 `true` `false` 마찬가지로 유효한 필터입니다.

그러나 대부분의 경우 두 개 이상의 필드와 상수를 참조하는 더 복잡한 식이 필요합니다. 이러한 식은 매개 변수에 따라 다른 방식으로 빌드됩니다.

다음 EBNF(확장[Backus-Naur Form)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) **$filter,** **$orderby**및 **$select** 매개 변수에 대한 문법을 정의합니다. 필드 경로 및 상수를 참조하는 간단한 식에서 빌드됩니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

**$orderby** 및 **$select** 매개 변수는 쉼표로 구분된 간단한 식 목록입니다. **$filter** 매개 변수는 간단한 하위 표현식으로 구성된 부울 식입니다. These sub-expressions are combined using logical operators such as [ `and`, `or`, and `not` ](search-query-odata-logical-operators.md), comparison operators such as [ `eq`, `lt`, `gt`, and so on](search-query-odata-comparison-operators.md), and collection operators such as [ `any` and `all` ](search-query-odata-collection-operators.md).

**$filter** **$orderby**및 **$select** 매개 변수는 다음 문서에서 자세히 설명합니다.

- [Azure 인지 검색에서 OData $filter 구문](search-query-odata-filter.md)
- [Azure 인지 검색에서 OData $orderby 구문](search-query-odata-orderby.md)
- [Azure 인지 검색에서 OData $select 구문](search-query-odata-select.md)

## <a name="see-also"></a>참조  

- [Azure 인지 검색에서 면탐색](search-faceted-navigation.md)
- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 쿼리 구문](query-lucene-syntax.md)
- [Azure 인지 검색의 간단한 쿼리 구문](query-simple-syntax.md)
