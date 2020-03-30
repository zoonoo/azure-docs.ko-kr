---
title: OData 비교 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 쿼리에서 OData 비교 연산자(eq, ne, gt, lt, ge 및 le)를 사용하기 위한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113212"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure 인지 검색의 OData `eq`비교 `ne` `gt`연산자 - `lt` `ge``le`

Azure 인지 검색의 [OData 필터 표현식에서](query-odata-filter-orderby-syntax.md) 가장 기본적인 작업은 필드를 지정된 값과 비교하는 것입니다. 두 가지 유형의 비교가 가능합니다: 같음 비교 및 범위 비교. 다음 연산자들을 사용하여 필드를 상수 값과 비교할 수 있습니다.

같음 연산자:

- `eq`: 필드가 상수값과 **같는지** 테스트합니다.
- `ne`: 필드가 상수값과 **같지 않은지** 테스트합니다.

범위 연산자:

- `gt`: 필드가 상수값보다 **큰지** 테스트합니다.
- `lt`: 필드가 상수값 **미만인지** 테스트
- `ge`: 필드가 상수 **값보다 크거나 같는지** 테스트합니다.
- `le`: 필드가 상수 **값보다 적거나 같는지** 테스트합니다.

[범위 연산자를 논리 연산자와](search-query-odata-logical-operators.md) 함께 사용하여 필드가 특정 값 범위 내에 있는지 여부를 테스트할 수 있습니다. 이 문서의 다음 [예제를](#examples) 참조하십시오.

> [!NOTE]
> 원하는 경우 연산자의 왼쪽에 상수 값을, 오른쪽에는 필드 이름을 넣을 수 있습니다. 범위 연산자의 경우 비교의 의미가 반전됩니다. 예를 들어 상수 값이 왼쪽에 `gt` 있는 경우 상수 값이 필드보다 큰지 여부를 테스트합니다. 비교 연산자(예: `geo.distance`)의 결과를 값과 비교할 수도 있습니다. `search.ismatch`과 같은 부울 함수의 경우 결과를 비교하거나 `true` `false` 선택 사항입니다.

## <a name="syntax"></a>구문

다음 EBNF(확장[백투스-Naur 양식)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)비교 연산자를 사용하는 OData 식의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

비교 식에는 두 가지 형태가 있습니다. 둘 사이의 유일한 차이점은 상수가 연산자의 왼쪽 또는 오른쪽에 나타나는지 여부입니다. 연산자의 다른 쪽에 있는 표현식은 **변수** 또는 함수 호출이어야 합니다. 변수는 필드 이름 또는 [람다 식의](search-query-odata-collection-operators.md)경우 범위 변수일 수 있습니다.

## <a name="data-types-for-comparisons"></a>비교를 위한 데이터 유형

비교 연산자의 양쪽에 있는 데이터 형식은 호환되어야 합니다. 예를 들어 왼쪽이 형식 `Edm.DateTimeOffset`필드인 경우 오른쪽은 날짜 시간 상수여야 합니다. 숫자 데이터 형식은 보다 유연합니다. 다음 표에 설명된 대로 몇 가지 제한 사항과 함께 모든 숫자 형식의 변수 및 함수를 다른 숫자 형식의 상수와 비교할 수 있습니다.

| 가변 또는 함수 유형 | 상수 값 유형 | 제한 사항 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 비교는 [에 대한 `NaN` 특별 규칙의](#special-case-nan) 적용을 받습니다. |
| `Edm.Double` | `Edm.Int64` | 상수는 `Edm.Double`변환되어 큰 크기의 값에 대한 정밀도가 손실됩니다. |
| `Edm.Double` | `Edm.Int32` | 해당 없음 |
| `Edm.Int64` | `Edm.Double` | 에 대한 비교 `INF` 또는 허용되지 않음 `-INF` `NaN` |
| `Edm.Int64` | `Edm.Int64` | 해당 없음 |
| `Edm.Int64` | `Edm.Int32` | 상수는 비교 `Edm.Int64` 전에 변환됩니다. |
| `Edm.Int32` | `Edm.Double` | 에 대한 비교 `INF` 또는 허용되지 않음 `-INF` `NaN` |
| `Edm.Int32` | `Edm.Int64` | 해당 없음 |
| `Edm.Int32` | `Edm.Int32` | 해당 없음 |

형식 `Edm.Int64` 필드를 비교하는 `NaN`것과 같이 허용되지 않는 비교의 경우 Azure 인지 검색 REST API는 "HTTP 400: 잘못된 요청" 오류를 반환합니다.

> [!IMPORTANT]
> 숫자 형식 비교는 유연하지만 상수 값이 비교되는 변수 또는 함수와 동일한 데이터 형식이 되도록 필터에 비교를 작성하는 것이 좋습니다. 이는 부동 점과 정수 값을 혼합할 때 특히 중요하며 정밀도를 잃는 암시적 변환이 가능합니다.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>에 대한 `null` 특별한 경우`NaN`

비교 연산자사용에서는 Azure Cognitive Search의 모든 비컬렉션 필드가 `null`잠재적으로 있을 수 있다는 점을 기억하는 것이 중요합니다. 다음 표에서는 어느 쪽이든 다음이 될 `null`수 있는 비교 식에 대해 가능한 모든 결과를 보여 주어집니다.

| 연산자 | 필드 또는 변수만`null` | 상수만`null` | 필드 또는 변수와 상수가 모두`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: 잘못된 요청 오류 | HTTP 400: 잘못된 요청 오류 |
| `lt` | `false` | HTTP 400: 잘못된 요청 오류 | HTTP 400: 잘못된 요청 오류 |
| `ge` | `false` | HTTP 400: 잘못된 요청 오류 | HTTP 400: 잘못된 요청 오류 |
| `le` | `false` | HTTP 400: 잘못된 요청 오류 | HTTP 400: 잘못된 요청 오류 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

요약하면, `null` 그 자체와 동일하며 다른 값보다 적거나 크지 않습니다.

인덱스에 형식 `Edm.Double` 필드가 있고 `NaN` 해당 필드에 값을 업로드하는 경우 필터를 작성할 때 이를 고려해야 합니다. Azure Cognitive Search는 `NaN` 처리 값에 대한 IEEE 754 표준을 구현하며, 이러한 값과의 비교는 다음 표와 같이 명확하지 않은 결과를 생성합니다.

| 연산자 | 하나 이상의 나연산자일 때의 결과`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

요약하면, `NaN` 자체를 포함한 모든 값과 같지 않습니다.

### <a name="comparing-geo-spatial-data"></a>지리 공간 데이터 비교

형식 `Edm.GeographyPoint` 필드를 상수 값과 직접 비교할 수는 없지만 함수를 `geo.distance` 사용할 수 있습니다. 이 함수는 형식 `Edm.Double`값을 반환하므로 상수 지리 공간 좌표의 거리를 기준으로 필터링할 숫자 상수와 비교할 수 있습니다. 아래 [예제를](#examples) 참조하십시오.

### <a name="comparing-string-data"></a>문자열 데이터 비교

문자열과 `eq` `ne` 연산자를 사용하여 정확한 일치 항목을 위해 필터에서 문자열을 비교할 수 있습니다. 이러한 비교는 대/소문자를 구분합니다.

## <a name="examples"></a>예

필드가 `Rating` 3에서 5 사이인 문서 일치(포함):

    Rating ge 3 and Rating le 5

필드가 `Location` 주어진 위도와 경도에서 2킬로미터 미만인 문서를 일치시다.

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

필드가 `LastRenovationDate` 2015년 1월 1일 자정 UTC보다 크거나 같은 문서 일치:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

필드가 `Details/Sku` 아닌 `null`문서 일치:

    Details/Sku ne null

`Rooms/Type` 필드 문자열이 필터와 정확히 일치하는 "디럭스 룸"을 하나 이상 사용하는 호텔의 문서 일치:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
