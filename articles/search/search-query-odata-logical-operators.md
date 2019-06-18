---
title: OData 논리 연산자 참조-Azure Search
description: OData 논리 연산자를,와 하지 Azure Search 쿼리 합니다.
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079770"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>-Azure Search의 OData 논리 연산자 `and`, `or`, `not`

[OData 필터 식을](query-odata-filter-orderby-syntax.md) Azure Search에는 부울 식으로 계산 되는 `true` 또는 `false`합니다. 일련의 작성 하 여 복잡 한 필터를 작성할 수 있습니다 [간단한 필터](search-query-odata-comparison-operators.md) 에서 논리 연산자를 사용 하 여 작성 하 고 [부울 대 수](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: 계산 되는 이항 연산자 `true` 의 왼쪽 및 오른쪽 하위 식이 모두로 평가 되 면 `true`합니다.
- `or`: 계산 되는 이항 연산자 `true` 경우 왼쪽 또는 오른쪽 하위 식 중 하나를 평가 `true`합니다.
- `not`: 계산 되는 단항 연산자 `true` 해당 하위 식이 `false`, 그 반대로 합니다.

와 함께 이러한 합니다 [컬렉션 연산자 `any` 하 고 `all` ](search-query-odata-collection-operators.md), 매우 복잡 한 검색 조건을 표현할 수 있는 필터를 구성할 수 있습니다.

## <a name="syntax"></a>구문

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))의 논리 연산자를 사용 하는 OData 식 문법 정의 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

논리 식의 두 가지: 이진 (`and`/`or`)에 두 개의 하위 식 및 단항 (`not`)을 하나 뿐입니다. 하위 식에는 어떤 종류의 부울 식 수 있습니다.

- 형식의 필드 또는 범위 변수 `Edm.Boolean`
- 형식의 값을 반환 하는 함수 `Edm.Boolean`와 같은 `geo.intersects` 또는 `search.ismatch`
- [비교 식](search-query-odata-comparison-operators.md)와 같은 `rating gt 4`
- [컬렉션 식](search-query-odata-collection-operators.md)와 같은 `Rooms/any(room: room/Type eq 'Deluxe Room')`
- 부울 리터럴은 `true` 또는 `false`합니다.
- 다른 논리 식을 사용 하 여 생성할 `and`, `or`, 및 `not`합니다.

> [!IMPORTANT]
> 사용 하 여 하위 식의 일부 종류를 사용할 수 있는 경우도 있습니다 `and` / `or`특히 내부 람다 식입니다. 참조 [Azure Search의 OData 컬렉션 연산자](search-query-odata-collection-operators.md#limitations) 세부 정보에 대 한 합니다.

### <a name="logical-operators-and-null"></a>논리 연산자 및 `null`

함수 및 비교와 같은 대부분의 부울 식을 생성할 수 없습니다 `null` 값 및 논리 연산자에 적용할 수 없습니다 합니다 `null` 직접 리터럴 (예를 들어 `x and null` 허용 되지 않습니다). 그러나 부울 필드 수 `null`이므로 방법을 알아야 `and`를 `or`, 및 `not` 연산자는 null이 있는 경우 작동 합니다. 다음 표에 요약 되어 있습니다이 위치 `b` 형식의 필드임 `Edm.Boolean`:

| 식 | 결과 때 `b` 는 `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

경우 부울 필드를 `b` 가 작성 된 것 처럼 동작 하는 필터 식에 단독으로 나타나고 `b eq true`, 따라서 `b` 은 `null`, 식의 결과가 `false`합니다. 마찬가지로 `not b` 처럼 `not (b eq true)`로 계산 되므로 `true`합니다. 이렇게에서 `null` 필드와 동일 하 게 동작 `false`합니다. 사용 하 여 다른 식과 결합할 때의 동작으로 구성 됩니다 `and` 고 `or`위의 표에 나와 있는 것 처럼 합니다. 그럼에도 불구 하 고, 직접 비교 `false` (`b eq false`)로 계속 평가 됩니다 `false`합니다. 다시 말해 `null` 와 같지 않은 `false`부울 식에 것 처럼 동작 하는 경우에 합니다.

## <a name="examples"></a>예

일치 문서 위치를 `rating` 필드는 3, 5 사이입니다.

    rating ge 3 and rating le 5

일치 위치를 문서화의 모든 요소는 `ratings` 필드는 보다 작거나 3 보다 크거나 5:

    ratings/all(r: r lt 3 or r gt 5)

일치 문서 위치를 `location` 필드는 지정 된 다각형 내에 있고 "public" 라는 용어는 문서에 없습니다.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

캐나다 밴쿠버 호텔에 대 한 문서와 일치 된 자료 deluxe 공간이 있는 160 미만인 비율:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
