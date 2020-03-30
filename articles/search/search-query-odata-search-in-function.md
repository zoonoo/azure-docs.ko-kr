---
title: OData search.in 기능 참조
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 쿼리에서 search.in 함수를 사용하기 위한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113112"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Azure `search.in` 인지 검색의 OData 함수

[OData 필터 표현식의](query-odata-filter-orderby-syntax.md) 일반적인 시나리오는 각 문서의 단일 필드가 가능한 여러 값 중 하나와 동일한지 확인하는 것입니다. 예를 들어, 일부 응용 프로그램에서는 쿼리를 실행하는 사용자를 나타내는 보안 개체 목록과 하나 이상의 보안 개체수를 포함하는 필드를 확인하여 [보안 트리밍을](search-security-trimming-for-azure-search.md) 구현하는 방법입니다. 이와 같은 쿼리를 작성하는 한 [`eq`](search-query-odata-comparison-operators.md) 가지 [`or`](search-query-odata-logical-operators.md) 방법은 및 연산자를 사용하는 것입니다.

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

그러나 함수를 사용하여 이 것을 작성하는 `search.in` 짧은 방법이 있습니다.

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> 짧고 읽기 쉬운 것 `search.in` 외에도, 사용 은 [성능 이점을](#bkmk_performance) 제공하고 필터에 포함 할 값의 수백 또는 수천이있을 때 [필터의](search-query-odata-filter.md#bkmk_limits) 특정 크기 제한을 피할 수 있습니다. 따라서 같음 표현식의 `search.in` 보다 복잡한 분리 대신 사용하는 것이 좋습니다.

> [!NOTE]
> OData 표준의 버전 4.01은 최근에 Azure 인지 검색의 `search.in` 함수와 유사한 동작을 가지는 [ `in` 연산자도입을](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)도입했습니다. 그러나 Azure Cognitive Search는 이 연산자(이 `search.in` 연산자)를 지원하지 않으므로 대신 함수를 사용해야 합니다.

## <a name="syntax"></a>구문

다음 EBNF[(확장 Backus-Naur 양식)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)함수의 문법을 `search.in` 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

함수는 `search.in` 지정된 문자열 필드 또는 범위 변수가 지정된 값 목록 중 하나인지 여부를 테스트합니다. 변수와 목록의 각 값 간의 같음은 `eq` 연산자와 동일한 방식으로 대/소문자 구분 방식으로 결정됩니다. 따라서 `search.in(myfield, 'a, b, c')`과 같은 식은 `search.in`이 훨씬 더 나은 성능을 제공한다는 점을 제외하고 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`와 같습니다.

함수에는 두 가지 `search.in` 오버로드가 있습니다.

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

매개 변수는 다음 표에 정의되어 있습니다.

| 매개 변수 이름 | Type | Description |
| --- | --- | --- |
| `variable` | `Edm.String` | 문자열 필드 참조(또는 문자열 `search.in` `any` 또는 `all` 식 내에서 사용되는 경우 문자열 컬렉션 필드에 대한 범위 변수). |
| `valueList` | `Edm.String` | 매개 변수와 일치하도록 구분된 값 목록을 `variable` 포함하는 문자열입니다. 매개 `delimiters` 변수를 지정하지 않으면 기본 구분 기호는 공간과 쉼표입니다. |
| `delimiters` | `Edm.String` | 매개 변수를 구문 분석할 때 각 문자가 구분 `valueList` 기호로 처리되는 문자열입니다. 이 매개 변수의 `' ,'` 기본값은 공백 및/또는 쉼표가 있는 모든 값이 분리된다는 것을 의미합니다. 값에 해당 문자가 포함되어 있으므로 공백 및 쉼표 이외의 구분 기호를 사용해야 하는 경우 `'|'` 이 매개 변수와 같은 대체 구분 기호를 지정할 수 있습니다. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>성능`search.in`

`search.in`을 사용하는 경우 두 번째 매개 변수에 수십만 개 값 목록이 포함되어 있을 때 1초 미만의 응답 시간을 기대할 수 있습니다. 전달할 `search.in`수 있는 항목 수에는 명시적인 제한이 없지만 최대 요청 크기에 의해 제한됩니다. 그러나 값 수가 증가함에 따라 대기 시간도 증가합니다.

## <a name="examples"></a>예

'씨뷰 모텔' 또는 '저예산 호텔'과 같은 이름을 가진 모든 호텔을 검색합니다. 구에는 기본 구분 기호인 공백이 포함되어 있습니다. 단일 따옴표에서 대체 구분 기호를 세 번째 문자열 매개 변수로 지정할 수 있습니다.  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

'씨뷰 모텔' 또는 '저예산 호텔'과 '|'로 구분된 모든 호텔 찾기:

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

'wifi' 또는 '욕조'라는 태그가 있는 객실이 있는 모든 호텔을 검색합니다.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

태그에 '열수 수건 걸이' 또는 '헤어드라이어 포함'과 같은 컬렉션 내의 문구에 일치하는 내용을 찾습니다.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

'모텔' 또는 '캐빈'이라는 태그가 없는 모든 호텔 찾기:

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
