---
title: OData 전체 텍스트 search 함수 참조-Azure Search
description: OData 전체 텍스트 검색 함수와 search.ismatch search.ismatchscoring, Azure Search 쿼리에 사용 합니다.
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079809"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Azure search-전체 텍스트 검색 기능 OData `search.ismatch` 및 `search.ismatchscoring`

Azure Search는 전체 텍스트 검색의 컨텍스트에서 지원 [OData 필터 식](query-odata-filter-orderby-syntax.md) 를 통해 합니다 `search.ismatch` 및 `search.ismatchscoring` 함수입니다. 이러한 함수를 사용 하면 최상위을 사용 하 여 가능 하지 않은 방식으로 엄격한 부울 필터링와 전체 텍스트 검색을 결합할 `search` 의 매개 변수를 [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents)합니다.

> [!NOTE]
> `search.ismatch` 하 고 `search.ismatchscoring` 함수에서 필터에만 지원 됩니다는 [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents)합니다. 지원 되지 않습니다 합니다 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) 하거나 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) Api.

## <a name="syntax"></a>구문

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))의 문법을 정의 된 `search.ismatch` 고 `search.ismatchscoring` 함수:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

### <a name="searchismatch"></a>search.ismatch

`search.ismatch` 함수는 필터 식의 일부로 전체 텍스트 검색 쿼리를 평가 합니다. 검색 쿼리와 일치하는 문서가 결과 집합에 반환됩니다. 이 함수의 다음 오버로드를 사용할 수 있습니다.

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

매개 변수는 다음 표에 정의 됩니다.

| 매개 변수 이름 | Type | 설명 |
| --- | --- | --- |
| `search` | `Edm.String` | 검색 쿼리 (하나로 [단순](query-simple-syntax.md) 또는 [전체](query-lucene-syntax.md) Lucene 쿼리 구문). |
| `searchFields` | `Edm.String` | 쉼표로 구분 된 목록을;에서 검색할 검색 가능한 필드 인덱스의 모든 검색 가능 필드의 기본값은입니다. 사용 하는 경우 [검색 한](query-lucene-syntax.md#bkmk_fields) 에 `search` 필드 지정자에서 Lucene 쿼리 매개 변수를이 매개 변수에 지정 된 모든 필드를 재정의 합니다. |
| `queryType` | `Edm.String` | `'simple'` 또는 `'full'`; 기본값으로 `'simple'`합니다. `search` 매개 변수에 사용된 쿼리 언어를 지정합니다. |
| `searchMode` | `Edm.String` | `'any'` 또는 `'all'`, 기본값은 `'any'`합니다. 일부 또는 모두 검색 조건에 있는지 여부를 나타냅니다는 `search` 매개 변수를 일치 항목으로 문서를 계산 하기 위해 일치 해야 합니다. 사용 하는 경우는 [Lucene 부울 연산자](query-lucene-syntax.md#bkmk_boolean) 에 `search` 매개 변수는 우선이 매개 변수입니다. |

위의 모든 매개 변수는 해당 같음 [검색 API에서 요청 매개 변수 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents)합니다.

합니다 `search.ismatch` 형식의 값을 반환 하는 함수 `Edm.Boolean`, 부울을 사용 하 여 다른 필터 하위 식을 사용 하 여 작성할 수 있습니다 [논리 연산자](search-query-odata-logical-operators.md)합니다.

> [!NOTE]
> Azure Search 사용을 지원 하지 않습니다 `search.ismatch` 또는 `search.ismatchscoring` 람다 식 내에서. 이 동일한 개체에서 일치 하는 엄격한 필터를 사용 하 여 전체 텍스트 검색 일치 항목을 상호 연결할 수 있는 개체 컬렉션을 통해 쓰기 필터 수 없는 것을 의미 합니다. 예제 뿐만 아니라이 제한에 대 한 자세한 내용은 참조 하세요. [Azure Search의 필터 컬렉션 문제 해결](search-query-troubleshoot-collection-filters.md)합니다. 이 제한은 존재 이유에서 보다 자세한 정보에 대 한 내용은 [Azure Search의 필터 컬렉션 이해](search-query-understand-collection-filters.md)합니다.


### <a name="searchismatchscoring"></a>search.ismatchscoring

`search.ismatchscoring` 같은 함수를 `search.ismatch` 함수를 반환 `true` 전체 텍스트 검색 쿼리와 일치 하는 문서에 대 한 매개 변수로 전달 합니다. 이 두 함수 간의 차이점은 `search.ismatchscoring` 쿼리와 일치하는 문서의 관련성 점수가 전반적인 문서 점수에 기여하지만, `search.ismatch`의 경우 문서 점수가 변경되지 않는다는 것입니다. 이 함수의 다음 오버로드는 `search.ismatch`의 매개 변수와 동일한 매개 변수에서 사용할 수 있습니다.

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

모두를 `search.ismatch` 고 `search.ismatchscoring` 동일한 필터 식에서 함수를 사용할 수 있습니다.

## <a name="examples"></a>예

"waterfront" 단어를 포함하는 문서를 찾습니다. 이 필터 쿼리는 `search=waterfront`를 사용한 [검색 요청](https://docs.microsoft.com/rest/api/searchservice/search-documents)과 동일합니다.

    search.ismatchscoring('waterfront')

단어 "hostel"을 포함하고 등급이 4 이상인 문서 또는 단어 "motel"을 포함하고 등급이 5인 문서를 찾습니다. 이 요청은 `search.ismatchscoring` 함수를 사용해야만 표시할 수 있습니다.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

단어 "luxury"가 없는 문서를 찾습니다.

    not search.ismatch('luxury')

구 "ocean view"를 포함하거나 등급이 5인 문서를 찾습니다. `search.ismatchscoring` 쿼리는 필드 `HotelName` 및 `Rooms/Description`에 대해서만 실행됩니다.

분리 된 두 번째 절만 일치 하는 문서를 반환할 너무-호텔 `Rating` 5입니다. 있도록 지우기는 해당 문서에는 식의 점수가 매겨진된 일부를 일치 하지 않습니다, 점수 0을 사용 하 여 반환 됩니다.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

용어 "호텔" 및 "공항" 되는 호텔의 설명에서 서로 5 개의 단어 내에서 위치 번호에 허용 되지 않습니다 이상 및 문서를 찾기는 대화방의 일부입니다. 이 쿼리는 [전체 Lucene 쿼리 언어](query-lucene-syntax.md)를 사용합니다.

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
