---
title: OData 논리 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 쿼리에서 OData 논리 연산자 및 사용 법및 사용 설명서를 사용합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113193"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure 인지 검색의 OData `and`논리 `or`연산자 - ,`not`

Azure 인지 검색의 [OData 필터 표현식은](query-odata-filter-orderby-syntax.md) 또는 `true` `false`을 평가하는 부울 식입니다. 일련의 [간단한 필터를](search-query-odata-comparison-operators.md) 작성하고 [부울 대수의](https://en.wikipedia.org/wiki/Boolean_algebra)논리 연산자를 사용하여 구성하여 복잡한 필터를 작성할 수 있습니다.

- `and`: 왼쪽 및 오른쪽 `true` 하위 식이 모두 를 평가하는지 평가하는 `true`이진 연산자입니다.
- `or`: 왼쪽 또는 오른쪽 `true` 하위 표현식 중 하나가 로 평가되는 경우 `true`를 평가하는 이진 연산자입니다.
- `not`: 하위 표현식이 `true` `false`[이의 반대)'로 평가되는지 평가하는 unary 연산자입니다.

[컬렉션 연산자와 `any` `all` ](search-query-odata-collection-operators.md)함께 매우 복잡한 검색 조건을 표현할 수 있는 필터를 생성할 수 있습니다.

## <a name="syntax"></a>구문

다음 EBNF(확장[백투스-Naur 양식)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)논리 연산자를 사용하는 OData 식의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

논리식에는 두 가지 형식의`and`/`or`이진식(이진식)이 있으며 두`not`개의 하위 식이 있는 경우는 unary()입니다. 하위 표현식은 모든 종류의 부울 식일 수 있습니다.

- 형식의 필드 또는 범위 변수`Edm.Boolean`
- 형식의 `Edm.Boolean`값을 반환하는 함수(예: `geo.intersects` 또는`search.ismatch`
- [와 같은 비교 식](search-query-odata-comparison-operators.md)`rating gt 4`
- [컬렉션 식(](search-query-odata-collection-operators.md)예:`Rooms/any(room: room/Type eq 'Deluxe Room')`
- 부울 리터럴 `true` 또는 `false`.
- 을 `and` `or`사용하여 구성된 다른 논리식입니다. `not`

> [!IMPORTANT]
> 특히 람다 식 내부에서 모든 종류의 하위 식을 `and` / `or`사용할 수 없는 경우도 있습니다. 자세한 내용은 [Azure 인지 검색에서 OData 컬렉션 연산자를](search-query-odata-collection-operators.md#limitations) 참조하십시오.

### <a name="logical-operators-and-null"></a>논리 연산자 및`null`

함수 및 비교와 같은 대부분의 부울 `null` 식은 값을 생성할 수 없으며 `null` 논리 연산자는 리터럴에 직접 적용할 수 없습니다(예: `x and null` 허용되지 않음). 그러나 부울 필드는 `null`이 필드가 될 수 있으므로 `and` `or`null이 `not` 있는 경우 에서 및 연산자가 어떻게 행동하는지 알아야 합니다. 이 형식은 다음 `b` `Edm.Boolean`표에 요약되어 있습니다.

| 식 | `b` 결과가`null` |
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

부울 필드가 `b` 필터 식에 자체적으로 나타나면 `b eq true`작성된 것처럼 작동하므로 `b` `null`식이 에 `false`대해 평가합니다. `not b` 마찬가지로 , 와 `not (b eq true)`같이 작동하므로 을 `true`평가합니다. 이러한 방식으로 `null` 필드는 `false`와 동일하게 행동합니다. 이는 위의 표와 같이 를 `and` `or`사용하여 다른 식과 결합할 때 의 행동 방식과 일치합니다. 그럼에도 불구하고 `false` ()에`b eq false`대한 직접적인 `false`비교는 여전히 을 평가합니다. 즉, `null` 부울 식에서 `false`처럼 작동 하더라도 와 같지 않습니다.

## <a name="examples"></a>예

필드가 `rating` 3에서 5 사이인 문서 일치(포함):

    rating ge 3 and rating le 5

`ratings` 필드의 모든 요소가 3 보다 크거나 5보다 큰 문서를 일치시면 됩니다.

    ratings/all(r: r lt 3 or r gt 5)

필드가 `location` 지정된 다각형 내에 있고 문서에 "public"이라는 용어가 포함되지 않은 문서를 일치시다.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

기본 요금이 160 미만인 디럭스룸이 있는 캐나다 밴쿠버의 호텔에 대한 문서 일치:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
