---
title: OData search.in 함수 참조-Azure Search
description: Azure Search 쿼리에 OData search.in 함수입니다.
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
ms.openlocfilehash: f72a59aac448796cf15220e15a3c8a4f12803bb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079731"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` Azure Search에서 함수

일반적인 시나리오 [OData 필터 식을](query-odata-filter-orderby-syntax.md) 각 문서의 단일 필드를 여러 개의 가능한 값 중 하나로 같은지 여부를 확인 하는 것입니다. 예를 들어,이 일부 응용 프로그램을 구현 하는 방법 [보안 트리밍이](search-security-trimming-for-azure-search.md) -쿼리를 실행 하는 사용자를 나타내는 보안 주체 Id의 목록에 대해 하나 이상의 보안 주체 Id를 포함 하는 필드를 확인 하 여 합니다. 다음과 같은 쿼리를 사용 하는 것을 작성 하는 한 가지 방법은 합니다 [ `eq` ](search-query-odata-comparison-operators.md) 하 고 [ `or` ](search-query-odata-logical-operators.md) 연산자:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

그러나를 사용 하 여이 쓸 수 있는 짧은 방법이 있습니다.는 `search.in` 함수:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> 더 간결 하 고 쉽게 사용 하 여 읽을 수 있을 뿐만 `search.in` 도 제공 [성능상의 이점을](#bkmk_performance) 특정 방지 [크기 필터 제한](search-query-odata-filter.md#bkmk_limits) 수백 또는 수천 개의 값이 있을 때 필터에 포함 합니다. 이러한 이유로 좋습니다를 사용 하 여 `search.in` 같음 표현의 복잡 한 분리를 대신 합니다.

> [!NOTE]
> 4\.01 OData 표준의 버전에 도입 합니다 [ `in` 연산자](http://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230),으로 유사한 동작을 포함 하는 `search.in` Azure Search에서 함수. 그러나 Azure Search 지원 하지 않으므로이 연산자를 사용 해야 합니다는 `search.in` 함수를 대신 합니다.

## <a name="syntax"></a>구문

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))의 문법을 정의 `search.in` 함수:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

`search.in` 함수 테스트 하는지 여부를 지정 된 문자열 필드 또는 범위 변수가 같은 값의 지정 된 목록 중 하나입니다. 변수 목록에서 각 값 사이의 같음 대/소문자 구분 방식에서와 동일한 방식으로 결정 됩니다는 `eq` 연산자입니다. 따라서 `search.in(myfield, 'a, b, c')`과 같은 식은 `search.in`이 훨씬 더 나은 성능을 제공한다는 점을 제외하고 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`와 같습니다.

두 가지 오버 로드의는 `search.in` 함수:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

매개 변수는 다음 표에 정의 됩니다.

| 매개 변수 이름 | Type | 설명 |
| --- | --- | --- |
| `variable` | `Edm.String` | 문자열 필드 참조 (또는 범위 변수에서는 문자열 컬렉션 필드 위에 위치 `search.in` 내에서 사용 되는 `any` 또는 `all` 식). |
| `valueList` | `Edm.String` | 구분 기호로 분리 된 일치 하는지 비교할 값 목록을 포함 하는 문자열을 `variable` 매개 변수입니다. 경우는 `delimiters` 매개 변수를 지정 하지 않으면 기본 구분 기호는 공백 및 쉼표입니다. |
| `delimiters` | `Edm.String` | 문자열을 구문 분석할 때 구분 기호로 각 문자 처리 되는 `valueList` 매개 변수입니다. 이 매개 변수의 기본값은 `' ,'` 즉, 공간 및/또는 쉼표로 구분 하는 모든 값을 구분 합니다. 대체 구분 기호를 같이 지정할 수 있으므로 해당 문자를 포함 하는 값 이외의 공백 및 쉼표 구분 기호를 사용 해야 할 경우 `'|'` 이 매개 변수입니다. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>성능 `search.in`

`search.in`을 사용하는 경우 두 번째 매개 변수에 수십만 개 값 목록이 포함되어 있을 때 1초 미만의 응답 시간을 기대할 수 있습니다. 에 전달할 수 있습니다 항목의 수에 명시적인 제한은 `search.in`이지만 최대 요청 크기에 따라 계속 제한 됩니다. 그러나 값 수가 증가함에 따라 대기 시간도 증가합니다.

## <a name="examples"></a>예

'바다 보기 motel' 또는 '예산 호텔' 같은 이름 가진 모든 호텔 찾기 구 기본 구분 기호는 공백을 포함 합니다. 세 번째 문자열 매개 변수로 작은따옴표에서는 다른 구분 기호를 지정할 수 있습니다.  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

이름의 모든 호텔을 '바다 보기 motel' 또는 '예산 호텔'으로 구분 된 같음 찾기 ' |').

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

대화방 태그 'wifi' 또는 '면'에 있는 모든 호텔 찾기:

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

태그에 '가 열된 수건 랙' 또는 '헤어 드라이기 포함'와 같은 컬렉션 내의 구 일치를 찾습니다.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

태그 'motel' 또는 '객실' 없는 모든 호텔 찾기:

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
