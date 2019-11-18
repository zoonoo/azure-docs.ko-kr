---
title: OData 논리 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 OData 논리 연산자, and, or 및 not을 사용 하는 방법에 대 한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113193"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure Cognitive Search의 OData 논리 연산자-`and`, `or`, `not`

Azure Cognitive Search의 [OData 필터 식은](query-odata-filter-orderby-syntax.md) `true` 또는 `false`으로 계산 되는 부울 식입니다. [간단한](search-query-odata-comparison-operators.md) 필터를 작성 하 고 [부울 대 수](https://en.wikipedia.org/wiki/Boolean_algebra)의 논리 연산자를 사용 하 여 작성 하 여 복잡 한 필터를 작성할 수 있습니다.

- `and`: 왼쪽 및 오른쪽 하위 식이 모두 `true`로 평가 되는 경우 `true`로 계산 되는 이항 연산자입니다.
- `or`: 왼쪽 또는 오른쪽 하위 식 중 하나가 `true`계산 되는 경우 `true`로 계산 되는 이항 연산자입니다.
- `not`: 하위 식이 `false`으로 계산 되는 경우 `true`로 계산 되는 단항 연산자이 고 그 반대의 경우도 마찬가지입니다.

[`any` 및 `all`컬렉션 연산자 ](search-query-odata-collection-operators.md)와 함께 사용 하 여 매우 복잡 한 검색 기준을 표현할 수 있는 필터를 만들 수 있습니다.

## <a name="syntax"></a>구문

다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 논리 연산자를 사용 하는 OData 식의 문법을 정의 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

논리적 식에는 이진 (`and`/`or`) 이라는 두 가지 형태가 있습니다. 여기에는 두 개의 하위 식과 단항 (`not`)가 있습니다. 하위 식은 모든 종류의 부울 식일 수 있습니다.

- `Edm.Boolean` 형식의 필드 또는 범위 변수
- `geo.intersects` 또는 `search.ismatch`와 같이 `Edm.Boolean`형식의 값을 반환 하는 함수
- `rating gt 4`와 같은 [비교 식](search-query-odata-comparison-operators.md)
- `Rooms/any(room: room/Type eq 'Deluxe Room')`와 같은 [컬렉션 식](search-query-odata-collection-operators.md)
- 부울 리터럴 `true` 또는 `false`입니다.
- `and`, `or`및 `not`를 사용 하 여 생성 된 다른 논리 식입니다.

> [!IMPORTANT]
> 모든 종류의 하위 식을 `and`/`or`, 특히 람다 식 내에서 사용할 수 없는 경우도 있습니다. 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 연산자](search-query-odata-collection-operators.md#limitations) 를 참조 하세요.

### <a name="logical-operators-and-null"></a>논리 연산자 및 `null`

함수 및 비교와 같은 대부분의 부울 식은 `null` 값을 생성할 수 없으며 논리 연산자를 `null` 리터럴에 직접 적용할 수 없습니다. 예를 들어 `x and null`은 허용 되지 않습니다. 그러나 부울 필드는 `null`될 수 있으므로 `and`, `or`및 `not` 연산자가 null 인 경우의 동작에 대해 알고 있어야 합니다. 다음 표에 요약 되어 있습니다. 여기에서 `b`은 `Edm.Boolean`형식의 필드입니다.

| 식 | `b`이 `null` 되는 경우의 결과 |
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

부울 필드 `b` 필터 식에 자체적으로 표시 되는 경우에는 `b eq true`작성 된 것 처럼 동작 하므로 `b` `null`경우 식은 `false`로 계산 됩니다. 마찬가지로 `not b`는 `not (b eq true)`처럼 동작 하므로 `true`로 평가 됩니다. 이러한 방식으로 `null` 필드는 `false`와 동일 하 게 동작 합니다. 위의 표에 나와 있는 것 처럼 `and` 및 `or`를 사용 하 여 다른 식과 결합 될 때 동작 하는 방법이 일치 합니다. 이 경우에도 `false` (`b eq false`)에 대 한 직접 비교는 `false`으로 평가 됩니다. 즉, `null`은 부울 식에서 처럼 동작 하는 경우에도 `false`와 같지 않습니다.

## <a name="examples"></a>예

`rating` 필드가 3에서 5 사이 (포함) 인 문서를 찾습니다.

    rating ge 3 and rating le 5

`ratings` 필드의 모든 요소가 3 보다 작거나 5 보다 큰 문서를 찾습니다.

    ratings/all(r: r lt 3 or r gt 5)

`location` 필드가 지정 된 다각형 내에 있고 문서에 "public" 이라는 용어가 포함 되어 있지 않은 문서를 찾습니다.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

160 미만의 기본 요금을 가진 deluxe 공간이 있는 밴쿠버 캐나다에서 호텔 문서를 찾습니다.

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 &#40;검색 Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
