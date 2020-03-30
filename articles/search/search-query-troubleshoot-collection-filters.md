---
title: OData 수집 필터 문제 해결
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 쿼리에서 OData 컬렉션 필터 오류를 해결하기 위한 방법을 알아봅니다.
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113085"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Azure 인지 검색에서 OData 컬렉션 필터 문제 해결

Azure Cognitive Search의 컬렉션 필드를 [필터링하려면](query-odata-filter-orderby-syntax.md) **람다** [ `any` `all` 식과 함께 및 연산자](search-query-odata-collection-operators.md) 를 사용할 수 있습니다. 람다 식은 컬렉션의 각 요소에 적용되는 하위 필터입니다.

람다 식 내에서 필터 식의 모든 기능을 사용할 수 있는 것은 아닙니다. 사용할 수 있는 기능은 필터링하려는 컬렉션 필드의 데이터 형식에 따라 다릅니다. 이 경우 해당 컨텍스트에서 지원되지 않는 lambda 식에서 기능을 사용하려고 하면 오류가 발생할 수 있습니다. 컬렉션 필드에 복잡한 필터를 작성하는 동안 이러한 오류가 발생하는 경우 이 문서에서는 문제를 해결하는 데 도움이 됩니다.

## <a name="common-collection-filter-errors"></a>일반적인 컬렉션 필터 오류

다음 표에는 컬렉션 필터를 실행할 때 발생할 수 있는 오류가 나열되어 있습니다. 이러한 오류는 lambda 식 내에서 지원되지 않는 필터 식 의 기능을 사용할 때 발생합니다. 각 오류는 오류를 방지하기 위해 필터를 다시 작성하는 방법에 대한 몇 가지 지침을 제공합니다. 또한 이 표에는 이 오류 방지 방법에 대한 자세한 정보를 제공하는 이 문서의 관련 섹션에 대한 링크도 포함되어 있습니다.

| 오류 메시지 | 상황 | 자세한 내용은 다음을 참조하세요. |
| --- | --- | --- |
| 함수 'ismatch'에는 범위 변수 's'에 바인딩된 매개 변수가 없습니다. 바인딩된 필드 참조만 람다 식('any' 또는 'all') 내에서 지원됩니다. 'ismatch' 함수가 람다 식 외부에 있도록 필터를 변경하고 다시 시도하십시오. | 람다 식 사용 `search.ismatch` 또는 `search.ismatchscoring` 내부 | [복잡한 컬렉션을 필터링하기 위한 규칙](#bkmk_complex) |
| 잘못된 람다 식입니다. Edm.String(형식 컬렉션(Edm.String) 필드를 통해 이터레이션되는 람다 식에서 반대가 예상되는 같음 또는 부등식에 대한 테스트를 찾았습니다. 'any'의 경우 'x eq y' 또는 'search.in(...)'의 표현식을 사용하십시오. 'all'의 경우 'x ne y', 'not(eq y)' 또는 'search.in 않음(...)'의 식을 사용하십시오. | 형식 필드에 대한 필터링`Collection(Edm.String)` | [문자열 컬렉션을 필터링하는 규칙](#bkmk_strings) |
| 잘못된 람다 식입니다. 지원되지 않는 복잡한 부울 식 형식을 찾았습니다. 'any'의 경우 'AND의 OR'인 표현식(디스커디브 법선 양식이라고도 함)을 사용하십시오. 예를 들어' (a 및 b) 또는 (c 및 d)' 여기서 a, b, c 및 d는 비교 또는 같음 하위 표현식이다. 'all'의 경우 결막 법선 양식이라고도 하는 'ANDs of O'인 식을 사용하십시오. 예를 들어, '(a) 및 (c 또는 d)' 여기서 a, b, c 및 d는 비교 또는 부등식하위표현식이다. 비교 식의 예: 'x gt 5', 'x le 2'. 같음 표현식의 예: 'x eq 5'. 불평등 표현식의 예: 'x ne 5'. | 유형 `Collection(Edm.DateTimeOffset)`의 `Collection(Edm.Double)` `Collection(Edm.Int32)`필드에 필터링`Collection(Edm.Int64)` | [비교 가능한 컬렉션을 필터링하기 위한 규칙](#bkmk_comparables) |
| 잘못된 람다 식입니다. 컬렉션(Edm.GeographyPoint) 형식의 필드를 통해 반복해서 사용하는 람다 식에서 geo.distance() 또는 geo.intersects()의 지원되지 않는 사용을 찾습니다. 'any'의 경우 'lt' 또는 'le' 연산자를 사용하여 geo.distance()를 비교하고 geo.intersects()의 사용이 무효화되지 않았는지 확인합니다. '모두'의 경우 'gt' 또는 'ge' 연산자를 사용하여 geo.distance()를 비교하고 geo.intersects()의 사용이 무효화되는지 확인합니다. | 형식 필드에 대한 필터링`Collection(Edm.GeographyPoint)` | [지리포인트 컬렉션 필터링 규칙](#bkmk_geopoints) |
| 잘못된 람다 식입니다. 복잡한 부울 식은 형식 컬렉션(Edm.GeographyPoint)의 필드를 반복하는 람다 식에서는 지원되지 않습니다. 'any'의 경우 하위 표현식에 'or'를 함께 조인하십시오. 'and'는 지원되지 않습니다. 'all'의 경우 하위 표현식에 'and'를 함께 조인하십시오. 'or'는 지원되지 않습니다. | 형식 `Collection(Edm.String)` 또는 필드의 필터링`Collection(Edm.GeographyPoint)` | [문자열 컬렉션을 필터링하는 규칙](#bkmk_strings) <br/><br/> [지리포인트 컬렉션 필터링 규칙](#bkmk_geopoints) |
| 잘못된 람다 식입니다. 비교 연산자('lt', 'le', 'gt' 또는 'ge'중 하나)를 찾았습니다. 형식 컬렉션(Edm.String)의 필드를 반복하는 람다 식에서는 같음 연산자만 허용됩니다. 'any'의 경우 'x eq y'형식의 식을 사용하십시오. 'all'의 경우 'x ne y' 또는 'not(x eq y)'의 표현식을 사용하십시오. | 형식 필드에 대한 필터링`Collection(Edm.String)` | [문자열 컬렉션을 필터링하는 규칙](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>유효한 컬렉션 필터를 작성하는 방법

유효한 컬렉션 필터를 작성하는 규칙은 데이터 형식마다 다릅니다. 다음 섹션에서는 지원되는 필터 피쳐와 지원되지 않는 필터 피쳐의 예를 보여 줌으로써 규칙을 설명합니다.

- [문자열 컬렉션을 필터링하는 규칙](#bkmk_strings)
- [부울 컬렉션 필터링 규칙](#bkmk_bools)
- [지리포인트 컬렉션 필터링 규칙](#bkmk_geopoints)
- [비교 가능한 컬렉션을 필터링하기 위한 규칙](#bkmk_comparables)
- [복잡한 컬렉션을 필터링하기 위한 규칙](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>문자열 컬렉션을 필터링하는 규칙

문자열 컬렉션에 대한 lambda 식 내에서 사용할 수 있는 `eq` 유일한 `ne`비교 연산자는 및 .

> [!NOTE]
> Azure Cognitive Search는 `lt` / `le` / `gt` / 람다 식 의 내부 또는 외부 여부에 관계없이 문자열에 대한 `ge` 연산자(연산자)를 지원하지 않습니다.

a의 `any` 본문에만 같음을 테스트할 `all` 수 있는 반면, a의 본문에는 부등식만 테스트할 수 있습니다.

`or` 의 본문 과 `any` `and` `all`에서의 본문 에서 여러 식을 결합할 수도 있습니다. `search.in` 함수는 같음 검사를 결합하는 `or`것과 같기 때문에 `any`의 본문에도 허용됩니다. 반대로 `not search.in` `all`의 본문에 허용됩니다.

예를 들어 이러한 식은 허용됩니다.

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

이러한 식은 허용되지 않지만 다음 을 수행할 수 있습니다.

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>부울 컬렉션 필터링 규칙

형식은 `Edm.Boolean` `eq` 및 `ne` 연산자만 지원합니다. 따라서 동일한 범위 변수를 `and` / `or` 확인하는 절을 결합하는 것은 의미가 없습니다.

다음은 허용되는 부울 컬렉션에 대한 필터의 몇 가지 예입니다.

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

문자열 컬렉션과 달리 부울 컬렉션에는 람다 식 의 형식에 사용할 수 있는 연산자가 없습니다. 둘 `eq` `ne` 다 `any` 또는 본체에서 `all`사용할 수 있습니다.

부울 컬렉션에는 다음과 같은 표현식이 허용되지 않습니다.

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>지리포인트 컬렉션 필터링 규칙

컬렉션의 `Edm.GeographyPoint` 형식 값은 서로 직접 비교할 수 없습니다. `geo.distance` 대신, 그들은 및 `geo.intersects` 함수에 매개 변수로 사용 해야 합니다. `geo.distance` 함수는 비교 연산자 `lt`중 하나를 사용하여 거리 값과 `le` `gt`차례로 `ge`비교해야 합니다. 이러한 규칙은 비컬렉션 Edm.GeographyPoint 필드에도 적용됩니다.

문자열 컬렉션과 `Edm.GeographyPoint` 마찬가지로 컬렉션에는 다양한 유형의 람다 식에서 지리적 공간 함수를 사용하고 결합하는 방법에 대한 몇 가지 규칙이 있습니다.

- `geo.distance` 함수와 함께 사용할 수 있는 비교 연산자는 람다 식의 형식에 따라 다릅니다. 의 `any`경우 또는 `le` `lt` 에서만 사용할 수 있습니다. 의 `all`경우 또는 `ge` `gt` 에서만 사용할 수 있습니다. 과 `geo.distance`관련된 식을 부정할 수 있지만 비교 연산자(됨 `geo.distance(...) le x` `not (geo.distance(...) gt x)``geo.distance(...) lt x` `not (geo.distance(...) ge x)` 및 됨)를 변경해야 합니다.
- 의 `all`본문에서 함수는 `geo.intersects` 부정되어야 합니다. 반대로, 의 `any`본문에서 함수는 `geo.intersects` 부정되어서는 안됩니다.
- 의 본문에서 `any`지리적 공간 식을 사용하여 `or`결합할 수 있습니다. 의 `all`본문에서 이러한 식을 사용하여 `and`결합 할 수 있습니다.

위의 제한은 문자열 컬렉션의 같음/부등금 제한과 유사한 이유로 존재합니다. 이러한 이유를 자세히 보려면 [Azure 인지 검색에서 OData 컬렉션 필터 이해를](search-query-understand-collection-filters.md) 참조하세요.

허용되는 컬렉션에 대한 `Edm.GeographyPoint` 필터의 몇 가지 예는 다음과 같습니다.

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

다음과 같은 식은 컬렉션에 `Edm.GeographyPoint` 사용할 수 없습니다.

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>비교 가능한 컬렉션을 필터링하기 위한 규칙

이 섹션은 다음 의 모든 데이터 형식에 적용됩니다.

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

비교 연산자 6개 모두 지원 `eq`및 `ne` `Edm.DateTimeOffset` `lt`지원: `gt` `Edm.Int32` . `ge` `le`. 이러한 형식의 컬렉션에 대한 Lambda 식은 이러한 연산자 중 일부를 사용하는 간단한 식을 포함할 수 있습니다. 이 두 `any` 가지 `all`모두에 적용 됩니다. 예를 들어 이러한 필터는 허용됩니다.

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

그러나 이러한 비교 식을 lambda 식 내에서 보다 복잡한 식으로 결합하는 방법에는 제한이 있습니다.

- 에 `any`대 한 규칙 :
  - 간단한 부등식식은 다른 식과 유용하게 결합할 수 없습니다. 예를 들어 이 식은 허용됩니다.
    - `ratings/any(r: r ne 5)`

    그러나 이 표현식은 그렇지 않습니다.
    - `ratings/any(r: r ne 5 and r gt 2)`

    이 식은 허용되지만 조건이 겹치기 때문에 유용하지 않습니다.
    - `ratings/any(r: r ne 5 or r gt 7)`
  - `eq`과 `lt` `and` / `or`관련된 간단한 비교 식을 `ge` " 또는 와 결합할 수 있습니다. `le` `gt` 예를 들어:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - (연결)과 `and` 결합된 비교 식은 을 `or`사용하여 더 결합될 수 있다. 이 양식은 부울 논리에서[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)"DNF"(DNF)로 알려져 있습니다. 예를 들어:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 에 `all`대 한 규칙 :
  - 단순 같음 표현식은 다른 식과 유용하게 결합할 수 없습니다. 예를 들어 이 식은 허용됩니다.
    - `ratings/all(r: r eq 5)`

    그러나 이 표현식은 그렇지 않습니다.
    - `ratings/all(r: r eq 5 or r le 2)`

    이 식은 허용되지만 조건이 겹치기 때문에 유용하지 않습니다.
    - `ratings/all(r: r eq 5 and r le 7)`
  - `ne`과 `lt` `and` / `or`관련된 간단한 비교 식을 `ge` " 또는 와 결합할 수 있습니다. `le` `gt` 예를 들어:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - (분리)와 `or` 결합된 비교 식은 을 사용하여 `and`더 결합될 수 있다. 이 양식은 부울 논리에서["결막 정상](https://en.wikipedia.org/wiki/Conjunctive_normal_form)형태"(CNF)로 알려져 있습니다. 예를 들어:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>복잡한 컬렉션을 필터링하기 위한 규칙

복잡한 컬렉션에 대한 Lambda 식은 기본 형식의 컬렉션보다 람다 식보다 훨씬 유연한 구문을 지원합니다. 두 가지 예외만 을 제외하고 외부에서 사용할 수 있는 이러한 lambda 식 내에서 필터 구문 생성을 사용할 수 있습니다.

첫째, 람다 `search.ismatch` `search.ismatchscoring` 식 내에서 함수및 지원되지 않습니다. 자세한 내용은 [Azure 인지 검색에서 OData 컬렉션 필터 이해를](search-query-understand-collection-filters.md)참조하세요.

둘째, range 변수(소위 *자유 변수)에* *바인딩되지* 않은 참조 필드는 허용되지 않습니다. 예를 들어 다음과 같은 두 개의 동등한 OData 필터 식을 고려하십시오.

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

첫 번째 표현식은 허용되고 두 번째 `details/margin` 형식은 range 변수에 `s`바인딩되지 않으므로 거부됩니다.

이 규칙은 외부 범위에 바인딩된 변수가 있는 식으로도 확장됩니다. 이러한 변수는 표시되는 범위와 관련하여 무료입니다. 예를 들어 첫 번째 표현식은 허용되지만 두 번째 `s/name` 등가 식은 range 변수의 `a`범위에 대해 무료이기 때문에 허용되지 않습니다.

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

람다 식에 바인딩된 변수만 포함되도록 필터를 생성할 수 있으므로 이러한 제한은 실제로 문제가 되지 않습니다.

## <a name="cheat-sheet-for-collection-filter-rules"></a>컬렉션 필터 규칙에 대한 치트 시트

다음 표에는 각 컬렉션 데이터 형식에 대해 유효한 필터를 생성하는 규칙이 요약됩니다.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

각 사례에 대해 유효한 필터를 생성하는 방법의 예는 [유효한 컬렉션 필터를 작성하는 방법을](#bkmk_examples)참조하십시오.

필터를 자주 작성하고 첫 번째 원칙의 규칙을 이해하면 필터를 암기하는 것 이상을 이해하는 데 도움이 되는 경우 [Azure Cognitive Search의 OData 컬렉션 이해 필터 이해를](search-query-understand-collection-filters.md)참조하십시오.

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색에서 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)
- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
