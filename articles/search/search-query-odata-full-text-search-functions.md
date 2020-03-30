---
title: OData 전체 텍스트 검색 기능 참조
titleSuffix: Azure Cognitive Search
description: OData 전체 텍스트 검색 기능, search.ismatch 및 search.ismatch득점, Azure 인지 검색 쿼리에서.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793361"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData Azure 인지 검색에서 전체 텍스트 `search.ismatch` 검색 기능 -`search.ismatchscoring`

Azure Cognitive Search는 `search.ismatch` 및 `search.ismatchscoring` 기능을 통해 [OData 필터 표현식의](query-odata-filter-orderby-syntax.md) 컨텍스트에서 전체 텍스트 검색을 지원합니다. 이러한 기능을 사용하면 `search` [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents)API의 최상위 매개 변수를 사용하여 는 불가능한 방식으로 전체 텍스트 검색을 엄격한 부울 필터링과 결합할 수 있습니다.

> [!NOTE]
> 및 함수는 [검색 API의](https://docs.microsoft.com/rest/api/searchservice/search-documents)필터에서만 지원됩니다. `search.ismatchscoring` `search.ismatch` [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API에서는 지원되지 않습니다.

## <a name="syntax"></a>구문

다음 EBNF[(확장 Backus-Naur 양식)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `search.ismatch` 함수의 `search.ismatchscoring` 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

### <a name="searchismatch"></a>검색.ismatch

이 `search.ismatch` 함수는 전체 텍스트 검색 쿼리를 필터 식의 일부로 평가합니다. 검색 쿼리와 일치하는 문서가 결과 집합에 반환됩니다. 이 함수의 다음 오버로드를 사용할 수 있습니다.

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

매개 변수는 다음 표에 정의되어 있습니다.

| 매개 변수 이름 | Type | Description |
| --- | --- | --- |
| `search` | `Edm.String` | 검색 [쿼리(단순](query-simple-syntax.md) 또는 [전체](query-lucene-syntax.md) Lucene 쿼리 구문)입니다. |
| `searchFields` | `Edm.String` | 검색할 검색 가능한 필드의 쉼표 로 구분된 목록; 기본값은 인덱스의 모든 검색 가능한 필드로 설정됩니다. 매개 변수에서 필드 검색을 사용하는 경우 Lucene 쿼리의 필드 지정자는 이 매개 변수에 지정된 모든 필드를 재정의합니다. [fielded search](query-lucene-syntax.md#bkmk_fields) `search` |
| `queryType` | `Edm.String` | `'simple'`또는; `'full'` 기본값입니다. `'simple'` `search` 매개 변수에 사용된 쿼리 언어를 지정합니다. |
| `searchMode` | `Edm.String` | `'any'`또는 `'all'`, 기본값 . `'any'` 문서를 일치로 계산하기 위해 `search` 매개 변수의 검색어 중 어느 또는 모든 것을 일치시켜야 하는지 여부를 나타냅니다. `search` 매개 변수에서 [Lucene Boolean 연산자사용](query-lucene-syntax.md#bkmk_boolean) 시 이 매개 변수보다 우선합니다. |

위의 모든 매개 변수는 Search API의 해당 [검색 요청 매개 변수와 동일합니다.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

함수는 `search.ismatch` 부울 논리 `Edm.Boolean`연산을 사용하여 다른 필터 하위 식으로 작성할 수 있는 형식 값을 [반환합니다.](search-query-odata-logical-operators.md)

> [!NOTE]
> Azure Cognitive Search는 `search.ismatch` 람다 식을 사용하거나 `search.ismatchscoring` 내부에서 사용하지 않습니다. 즉, 동일한 개체에 대한 엄격한 필터 일치와 전체 텍스트 검색 일치의 상관 관계를 지정할 수 있는 개체 컬렉션에 대한 필터를 작성할 수 없습니다. 이 제한 사항및 예제에 대한 자세한 내용은 [Azure Cognitive Search의 컬렉션 문제 해결 필터를](search-query-troubleshoot-collection-filters.md)참조하십시오. 이러한 제한이 존재하는 이유에 대한 자세한 내용은 Azure Cognitive Search 의 [컬렉션 필터 이해를](search-query-understand-collection-filters.md)참조하십시오.


### <a name="searchismatchscoring"></a>검색.ismatch점수 매기기

함수와 `search.ismatchscoring` `search.ismatch` 마찬가지로 함수는 `true` 매개 변수로 전달된 전체 텍스트 검색 쿼리와 일치하는 문서에 대해 반환합니다. 이 두 함수 간의 차이점은 `search.ismatchscoring` 쿼리와 일치하는 문서의 관련성 점수가 전반적인 문서 점수에 기여하지만, `search.ismatch`의 경우 문서 점수가 변경되지 않는다는 것입니다. 이 함수의 다음 오버로드는 `search.ismatch`의 매개 변수와 동일한 매개 변수에서 사용할 수 있습니다.

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch` 함수와 `search.ismatchscoring` 함수는 모두 동일한 필터 표현식에서 사용할 수 있습니다.

## <a name="examples"></a>예

"waterfront" 단어를 포함하는 문서를 찾습니다. 이 필터 쿼리는 `search=waterfront`를 사용한 [검색 요청](https://docs.microsoft.com/rest/api/searchservice/search-documents)과 동일합니다.

    search.ismatchscoring('waterfront')

단어 "hostel"을 포함하고 등급이 4 이상인 문서 또는 단어 "motel"을 포함하고 등급이 5인 문서를 찾습니다. 이 요청은 `search.ismatchscoring` 함수를 사용해야만 표시할 수 있습니다.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

단어 "luxury"가 없는 문서를 찾습니다.

    not search.ismatch('luxury')

구 "ocean view"를 포함하거나 등급이 5인 문서를 찾습니다. `search.ismatchscoring` 쿼리는 필드 `HotelName` 및 `Rooms/Description`에 대해서만 실행됩니다.

분리 명령의 두 번째 조항과 일치하는 문서도 반환됩니다 `Rating` - 5와 같은 호텔. 해당 문서가 표현식의 점수가 매겨지는 부분과 일치하지 않는다는 것을 분명히 하기 위해 점수가 0으로 반환됩니다.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

"호텔"과 "공항"이라는 용어가 호텔 설명에서 서로 5 단어 이내이며 적어도 일부 객실에서 흡연이 허용되지 않는 문서를 찾으십시오. 이 쿼리는 [전체 Lucene 쿼리 언어](query-lucene-syntax.md)를 사용합니다.

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
