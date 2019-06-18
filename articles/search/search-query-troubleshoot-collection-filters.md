---
title: OData 컬렉션 필터-Azure Search를 문제 해결
description: Azure Search 쿼리에 OData 컬렉션 필터 오류를 해결 합니다.
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079627"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Azure Search의 OData 컬렉션 필터를 문제 해결

[필터](query-odata-filter-orderby-syntax.md) Azure Search의 컬렉션 필드를 사용할 수 있습니다 합니다 [ `any` 하 고 `all` 연산자](search-query-odata-collection-operators.md) 와 함께 **람다 식**. 람다 식에는 컬렉션의 각 요소에 적용 되는 하위 필터입니다.

필터 식의 일부 기능은 람다 식 안에 사용할 수 있습니다. 사용할 수 있는 기능을 필터링 하려는 컬렉션 필드의 데이터 형식에 따라 달라 집니다. 이 인해 해당 컨텍스트에서 지원 되지 않는 람다 식의 기능을 사용 하려고 하면 오류가 발생할 수 있습니다. 컬렉션 필드를 통해 복잡 한 필터를 작성 하는 동안 이러한 오류를 발생 하는 것으로 확인 되이 문서에서는 문제를 해결 하는 데 도움이 됩니다.

## <a name="common-collection-filter-errors"></a>일반적인 컬렉션 필터 오류

다음 표에서 컬렉션 필터를 실행 하려고 할 때 발생할 수 있는 오류를 보여 줍니다. 이러한 오류는 람다 식 내에서 지원 되지 않는 필터 식의 기능을 사용 하는 경우 발생 합니다. 각 오류는 오류를 방지 하려면 필터를 다시 작성할 수 있습니다 하는 방법에 대 한 일부 지침을 제공 합니다. 테이블에는 해당 오류를 방지 하는 방법에 자세한 정보를 제공 하는이 문서의 해당 섹션에 대 한 링크가 포함 됩니다.

| 오류 메시지 | 상황 | 자세한 내용은 다음을 참조하세요. |
| --- | --- | --- |
| 함수 'ismatch' 매개 변수가 없는 범위 변수에 바인딩된 '. 만 바인딩할 필드 참조 ('any' 또는 'all') 람다 식 내에서 지원 됩니다. 'Ismatch' 함수는 람다 식 외부 되도록 필터를 변경 하 고 다시 시도 하세요. | 사용 하 여 `search.ismatch` 또는 `search.ismatchscoring` 람다 식 내의 | [복잡 한 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_complex) |
| 잘못 된 람다 식입니다. 반대 collection (edm.string) 형식의 필드를 반복 하는 람다 식에 필요한 곳 같음 또는 같지 않음을 테스트를 발견 합니다. 'Any'에 대 한 식 형식 'x eq y' 또는 'search.in(...)'를 사용 하십시오. 에 대 한 'all', '없습니다 (x eq y)', 'x ne y' 양식 또는 'search.in(...)' 식을 사용 하세요. | 형식의 필드 필터링 `Collection(Edm.String)` | [문자열 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_strings) |
| 잘못 된 람다 식입니다. 지원 되지 않는 복잡 한 부울 식 형식의 찾을 수 있습니다. 에 'any', ' Or의 ANDs', Disjunctive Normal Form 라고도 하는 식은 사용 하십시오. 예를 들어: '(a and b) 또는 (c 및 d)' 여기서 a, b, c 및 d는 비교 연산자나 같음 하위 식입니다. 에 대 한 'all', ' ANDs의 ORs', 일반 형식 으로만 라고도 하는 식은 사용 하세요. 예를 들어: '(a or b) 및 (c 또는 d)' 여기서 a, b, c 및 d는 비교 또는 같지 않음 하위 식입니다. 비교 식의 예: ' x gt 5', ' le 2' x. 같음 식의 예: ' x eq 5'. 같지 않음 식의 예: ' x ne 5'. | 형식 필드에서 필터링 `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, 또는 `Collection(Edm.Int64)` | [비교할 수 있는 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_comparables) |
| 잘못 된 람다 식입니다. Collection(Edm.GeographyPoint) 형식의 필드를 반복 하는 람다 식에서 geo.distance() 또는 geo.intersects()는 지원 되지 않는 사용을 찾았습니다. 'Any', 해야 geo.distance() 'lt' 또는 'le' 연산자를 사용 하 여 비교 하 고 geo.intersects() 사용이 부정 되지 있는지 확인 합니다. 에 대 한 'all', 'gt' 또는 'ge' 연산자를 사용 하 여 geo.distance() 비교 하 고 해야 geo.intersects() 사용이 부정 있는지 확인 합니다. | 형식의 필드 필터링 `Collection(Edm.GeographyPoint)` | [GeographyPoint 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_geopoints) |
| 잘못 된 람다 식입니다. 복합 부울 식은 Collection(Edm.GeographyPoint) 형식의 필드를 반복 하는 람다 식에서 지원 되지 않습니다. 'Any'에 대 한 의문점에 있는 하위 식 '또는'; '및' 지원 되지 않습니다. 에 대 한 하위 식을 사용 하 여 의문점 'all', '및'; '또는' 지원 되지 않습니다. | 형식 필드에서 필터링 `Collection(Edm.String)` 또는 `Collection(Edm.GeographyPoint)` | [문자열 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_strings) <br/><br/> [GeographyPoint 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_geopoints) |
| 잘못 된 람다 식입니다. 비교 연산자 ('lt', 'le', 'gt', 'ge' 중 하나)를 찾을 수 있습니다. 같음 연산자만 collection (edm.string) 형식의 필드를 반복 하는 람다 식에서 허용 됩니다. 'Any'에 대 한 'x eq y' 형식의 식을 사용 하십시오. 에 대 한 'all', 'x ne y' 양식 또는 '없습니다 (x eq y)' 식을 사용 하세요. | 형식의 필드 필터링 `Collection(Edm.String)` | [문자열 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>유효한 컬렉션 필터를 작성 하는 방법

각 데이터 형식에 대 한 유효한 컬렉션 필터 작성에 대 한 규칙을 서로 다릅니다. 다음 섹션에서는 예제 필터의 기능은 지원 하지는 표시 하 여 규칙을 설명 합니다.

- [문자열 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_strings)
- [부울 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_bools)
- [GeographyPoint 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_geopoints)
- [비교할 수 있는 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_comparables)
- [복잡 한 컬렉션을 필터링 하는 것에 대 한 규칙](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>문자열 컬렉션을 필터링 하는 것에 대 한 규칙

사용할 수 있는 유일한 비교 연산자는 문자열 컬렉션에 대 한 람다 식 안에 `eq` 고 `ne`입니다.

> [!NOTE]
> Azure Search를 지원 하지 않습니다 합니다 `lt` / `le` / `gt` / `ge` 문자열에 대 한 연산자 내부 또는 외부 람다 식입니다.

본문을 `any` 본문 하는 동안 같은지 테스트할 수는 `all` 같지 않은지 테스트할 수 있습니다.

통해 여러 식을 결합할 수 이기도 `or` 의 본문에는 `any`, 및 `and` 본문에는 `all`합니다. 있으므로 `search.in` 함수는 같음 검사를 결합 `or`도의 본문에 사용할 수는 `any`합니다. 반대로 `not search.in` 의 본문에 허용 되는 `all`합니다.

예를 들어, 이러한 식은 허용 됩니다.

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

이러한 식 허용 되지 않습니다.

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>부울 컬렉션을 필터링 하는 것에 대 한 규칙

형식 `Edm.Boolean` 만 지원 합니다 `eq` 고 `ne` 연산자입니다. 이와 같이 사용 하 여 동일한 범위 변수를 확인 하는 이러한 절을 결합할 수 있도록 말 되어도 `and` / `or` 는 항상 시키는 tautologies 또는 모순 때문입니다.

허용 되는 부울 컬렉션에 대 한 필터의 몇 가지 예는 다음과 같습니다.

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

문자열 컬렉션과 달리 부울 컬렉션에는 연산자를 사용할 수 있습니다에 제한이 없는 람다 식의 형식을 있습니다. 둘 다 `eq` 하 고 `ne` 본문에서 사용할 수 있습니다 `any` 또는 `all`합니다.

부울 컬렉션에 대 한 다음과 같은 식은 허용 되지 않습니다.

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>GeographyPoint 컬렉션을 필터링 하는 것에 대 한 규칙

형식의 값 `Edm.GeographyPoint` 컬렉션에 서로에 직접 비교할 수 없습니다. 매개 변수로 사용할 수 있어야 대신 합니다 `geo.distance` 및 `geo.intersects` 함수입니다. 합니다 `geo.distance` 비교 연산자 중 하나를 사용 하 여 거리 값으로 비교 해야 다시 함수 `lt`를 `le`를 `gt`, 또는 `ge`합니다. 이러한 규칙이 비 컬렉션 Edm.GeographyPoint 필드에도 적용 합니다.

문자열 컬렉션 처럼 `Edm.GeographyPoint` 컬렉션에 지리 공간 함수 수 사용 및 다양 한 람다 식 결합 하는 방법에 대 한 몇 가지 규칙:

- 사용 하 여 사용할 수 있는 비교 연산자는 `geo.distance` 함수 람다 식의 형식에 따라 달라 집니다. 에 대 한 `any`에 사용할 수 있습니다 `lt` 또는 `le`합니다. 에 대 한 `all`에 사용할 수 있습니다 `gt` 또는 `ge`합니다. 포함 된 식을 부정할 수 있습니다 `geo.distance`, 비교 연산자를 변경 해야 하지만 (`geo.distance(...) lt x` 됩니다 `not (geo.distance(...) ge x)` 하 고 `geo.distance(...) le x` 됩니다 `not (geo.distance(...) gt x)`).
- 본문에는 `all`, `geo.intersects` 함수를 부정 합니다. 반대로, 본문에는 `any`, `geo.intersects` 함수를 무효화 하도록 해야 합니다.
- 본문에는 `any`를 사용 하 여 지리 공간 식을 결합할 수 있습니다 `or`합니다. 본문에는 `all`를 사용 하 여 이러한 식을 결합할 수 있습니다 `and`합니다.

위의 제한 사항 비슷한 이유로 문자열 컬렉션에 대 한 같음/같지 않음 제한은으로 존재 합니다. 참조 [Azure Search의 이해 OData 컬렉션 필터](search-query-understand-collection-filters.md) 보다 자세히 살펴볼이 달하는 대 한 합니다.

여기에 몇 가지 예가 필터 `Edm.GeographyPoint` 허용 되는 컬렉션:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

다음과 같은 식이 허용 되지 않습니다 `Edm.GeographyPoint` 컬렉션:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>비교할 수 있는 컬렉션을 필터링 하는 것에 대 한 규칙

이 섹션에서는 다음 모든 데이터 형식에 적용 됩니다.

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

와 같은 형식 `Edm.Int32` 하 고 `Edm.DateTimeOffset` 여섯 가지 비교 연산자를 지원: `eq`, `ne`, `lt`, `le`, `gt`, 및 `ge`. 이러한 형식의 컬렉션에 비해 람다 식에는 이러한 연산자를 사용 하 여 간단한 식을 포함할 수 있습니다. 이 둘 다에 적용 됩니다 `any` 고 `all`입니다. 예를 들어 이러한 필터는 허용 됩니다.

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

그러나 이러한 비교 식 람다 식 안에 더 복잡 한 식으로 결합할 수 있습니다 하는 방법에 제한이 있습니다.

- 규칙에 대 한 `any`:
  - 단순 불균등 식 유용 하 게 다른 식과 결합할 수 없습니다. 예를 들어이 식은 허용 됩니다.
    - `ratings/any(r: r ne 5)`

    하지만이 식은 되지 않습니다.
    - `ratings/any(r: r ne 5 and r gt 2)`

    및이 식을 허용 되지만,이 유용 하지 조건을 겹치기 때문에:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - 관련 된 단순 비교 식 `eq`, `lt`를 `le`를 `gt`, 또는 `ge` 결합할 수 있습니다 `and` / `or`합니다. 예를 들면 다음과 같습니다.
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - 비교 식 결합할 `and` (접속사) 결합할 수 있습니다 사용 하 여 `or`입니다. 이 형식으로 부울 논리에 알려져 있는 "[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (로). 예를 들면 다음과 같습니다.
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 규칙에 대 한 `all`:
  - 단순 동등 식 유용 하 게 다른 식과 결합할 수 없습니다. 예를 들어이 식은 허용 됩니다.
    - `ratings/all(r: r eq 5)`

    하지만이 식은 되지 않습니다.
    - `ratings/all(r: r eq 5 or r le 2)`

    및이 식을 허용 되지만,이 유용 하지 조건을 겹치기 때문에:
    - `ratings/all(r: r eq 5 and r le 7)`
  - 관련 된 단순 비교 식 `ne`, `lt`를 `le`를 `gt`, 또는 `ge` 결합할 수 있습니다 `and` / `or`합니다. 예를 들면 다음과 같습니다.
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - 비교 식 결합할 `or` (식) 결합할 수 있습니다 사용 하 여 `and`입니다. 이 형식으로 부울 논리에 알려져 있는 "[으로만 정규 양식](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). 예를 들면 다음과 같습니다.
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>복잡 한 컬렉션을 필터링 하는 것에 대 한 규칙

복잡 한 컬렉션에 비해 람다 식은 기본 형식의 컬렉션에 비해 람다 식 보다 훨씬 더 유연한 구문을 지원합니다. 이러한 람다 식 내의 두 가지 예외를 사용 하 여 외부 하나를 사용할 수 있는 모든 필터 구문을 사용할 수 있습니다.

첫째, 함수 `search.ismatch` 고 `search.ismatchscoring` 람다 식 내에서 지원 되지 않습니다. 자세한 내용은 [Azure Search의 이해 OData 컬렉션 필터](search-query-understand-collection-filters.md)합니다.

둘째, 없는 필드를 참조 *바인딩된* 범위 변수에 (소위 *자유 변수*) 허용 되지 않습니다. 예를 들어 다음 두 가지 해당 OData 필터 식:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

첫 번째 식 수, 두 번째 폼 되므로 거부 됩니다 `details/margin` 범위 변수에 바인딩되어 있지 않은 `s`합니다.

이 규칙은 또한 외부 범위에서 바인딩된 변수는 식으로 확장 합니다. 이러한 변수는 표시 되는 범위에 대해 무료입니다. 예를 들어, 첫 번째 식이 허용 됩니다, 두 번째 식은 허용 되지 않습니다 `s/name` 범위 변수의 범위와 관련 하 여 무료 `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

이 제한은 람다 식에 바인딩된 변수만 포함 될 수 있도록 필터를 구성할 수 있으므로 실제로 문제가 아니어야 합니다.

## <a name="cheat-sheet-for-collection-filter-rules"></a>컬렉션 필터 규칙에 대 한 참조 표

다음 표에서 각 컬렉션 데이터 형식에 대 한 유효한 필터를 생성 하기 위한 규칙을 보여 줍니다.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

각 사례에 대해 유효한 필터를 생성 하는 방법의 예제를 참조 하세요 [유효한 컬렉션 필터를 작성 하는 방법을](#bkmk_examples)합니다.

참조만 기억 하면 보다 많은 경우 필터를 자주 작성 하 고 첫 번째 원칙에서 규칙을 이해 하도록 도와줍니다 [Azure Search의 이해 OData 컬렉션 필터](search-query-understand-collection-filters.md)합니다.

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)
- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
