---
title: Azure Search-OData 비교 연산자 참조
description: OData 비교 연산자, eq, ne, gt, lt, ge 및 Azure Search 쿼리에 le, 합니다.
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079926"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Azure search-OData 비교 연산자 `eq`, `ne`를 `gt`를 `lt`, `ge`, 및 `le`

가장 기본적인 작업을 [OData 필터 식](query-odata-filter-orderby-syntax.md) 필드를 지정된 된 값을 비교 하는 것에서 Azure Search입니다. 두 가지 유형의 비교 가능한-는 같음 비교 및 범위 비교 합니다. 상수 값으로 필드를 비교한 다음 연산자를 사용할 수 있습니다.

같음 연산자:

- `eq`: 필드 인지 여부를 테스트 **같음** 상수 값
- `ne`: 필드 인지 여부를 테스트 **같지 않음** 상수 값

범위 연산자:

- `gt`: 필드 인지 여부를 테스트 **보다 큰** 상수 값
- `lt`: 필드 인지 여부를 테스트 **보다 작거나** 상수 값
- `ge`: 필드 인지 여부를 테스트 **보다 크거나 같은** 상수 값
- `le`: 필드 인지 여부를 테스트 **보다 작거나 같음** 상수 값

범위 연산자를 사용 하 여 함께에서 합니다 [논리 연산자](search-query-odata-logical-operators.md) 필드 값의 특정 범위 내에 있는지 테스트 하 합니다. 참조 된 [예제](#examples) 이 문서의 뒷부분에 나오는.

> [!NOTE]
> 원하는 경우에 오른쪽에 있는 필드 이름과 연산자의 좌 변에 있는 상수 값을 넣을 수 있습니다. 범위 연산자에 대 한 비교 의미를 반전 됩니다. 예를 들어 상수 값이 왼쪽에서 `gt` 상수 값을 필드 보다 크면 하는지 테스트 합니다. 함수의 결과 같은 비교할 비교 연산자를 사용할 수도 있습니다 `geo.distance`, 값을 사용 하 여 합니다. 함수에 대 한 부울과 같은 `search.ismatch`, 결과를 비교 `true` 또는 `false` 선택 사항입니다.

## <a name="syntax"></a>구문

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 비교 연산자를 사용 하는 OData 식 문법 정의 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

비교 식의 두 가지 있습니다. 유일한 차이점은 상수에 또는 오른쪽으로 hand-왼쪽 연산자의 표시 되는지 여부를 합니다. 연산자의 반대쪽에 있는 식 이어야 합니다는 **변수** 또는 함수 호출 합니다. 경우 범위 변수 또는 필드 이름을 변수를 사용할 수는 [람다 식](search-query-odata-collection-operators.md)합니다.

## <a name="data-types-for-comparisons"></a>비교에 대 한 데이터 형식

비교 연산자의 양쪽 모두에 데이터 형식이 호환 되어야 합니다. 예를 들어 왼쪽 형식의 필드임 `Edm.DateTimeOffset`, 오른쪽에 있는 날짜 / 시간 상수 여야 합니다. 숫자 데이터 형식은 보다 유연 합니다. 다음 표에 설명 된 대로 변수 및 다른 형식의 숫자를 몇 가지 제한 사항이 있는 상수를 사용 하 여 숫자 형식의 함수를 비교할 수 있습니다.

| 변수 또는 함수 형식 | 상수 값 형식 | 제한 사항 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 비교는 [에 대 한 특수 규칙 `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | 상수는 변환할 `Edm.Double`로 큰 크기의 값에 대 한 정밀도 손실 |
| `Edm.Double` | `Edm.Int32` | 해당 없음 |
| `Edm.Int64` | `Edm.Double` | 비교 `NaN`하십시오 `-INF`, 또는 `INF` 허용 되지 않습니다 |
| `Edm.Int64` | `Edm.Int64` | 해당 없음 |
| `Edm.Int64` | `Edm.Int32` | 상수는 변환할 `Edm.Int64` 비교 하기 전에 |
| `Edm.Int32` | `Edm.Double` | 비교 `NaN`하십시오 `-INF`, 또는 `INF` 허용 되지 않습니다 |
| `Edm.Int32` | `Edm.Int64` | 해당 없음 |
| `Edm.Int32` | `Edm.Int32` | 해당 없음 |

형식의 필드를 비교 하는 등 허용 하지 않는 비교를 위한 `Edm.Int64` 에 `NaN`, Azure Search REST API는 반환는 "HTTP 400: 잘못 된 요청"오류가 발생 했습니다.

> [!IMPORTANT]
> 숫자 형식 비교 유연한 인 경우에 변수 또는 함수 비교 되는 동일한 데이터 형식의 상수 값이 있도록 필터에서 비교를 쓰는 것이 좋습니다. 이 특히 중요 될 때 부동 소수점 혼합 및 정밀도 떨어질는 암시적 변환을 사용할 수 있는 정수 값입니다.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>에 대 한 특수 사례 `null` 및 `NaN`

비교 연산자를 사용 하는 경우 잠재적으로 Azure Search에서 모든 비 컬렉션 필드 수 있음을 고려해 야는 `null`합니다. 다음 표에서 비교 식에 대 한 가능한 결과 모두 포함 하는 어느 쪽이 든 될 수 있는 `null`:

| 연산자 | 필드 또는 변수는 경우의 결과 `null` | 결과 상수만 경우 `null` | 결과 모두 필드 또는 변수 및 상수 `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: 잘못 된 요청 오류 | HTTP 400: 잘못 된 요청 오류 |
| `lt` | `false` | HTTP 400: 잘못 된 요청 오류 | HTTP 400: 잘못 된 요청 오류 |
| `ge` | `false` | HTTP 400: 잘못 된 요청 오류 | HTTP 400: 잘못 된 요청 오류 |
| `le` | `false` | HTTP 400: 잘못 된 요청 오류 | HTTP 400: 잘못 된 요청 오류 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

요약 하자면, `null` 자체에 고 덜 하지 또는 다른 값 보다 큰입니다.

인덱스 형식의 필드가 있으면 `Edm.Double` 업로드 및 `NaN` 필터를 작성 하는 경우에 대 한 계정 해야 해당 필드에 값입니다. Azure Search 구현 처리에 대 한 IEEE 754 표준 `NaN` 값 및 이러한 값을 사용 하 여 비교는 다음 표에 나와 있는 것 처럼 확실치 않은 결과 생성 합니다.

| 연산자 | 하나 이상의 피연산자가 하는 경우의 결과 `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

요약 하자면, `NaN` 자신을 포함 한 모든 값과 같지 않습니다.

### <a name="comparing-geo-spatial-data"></a>지리 공간 데이터를 비교합니다.

형식의 필드를 직접 비교할 수 없습니다 `Edm.GeographyPoint` 상수 값으로 사용할 수 있지만 `geo.distance` 함수입니다. 이 함수 형식의 값을 반환 `Edm.Double`숫자를 사용 하 여 비교할 수 있도록, 상수 지리 공간 좌표에서 거리를 기준으로 필터링 하는 상수입니다. 참조 된 [예제](#examples) 아래.

### <a name="comparing-string-data"></a>문자열 데이터 비교

사용 하 여 정확히 일치에 대 한 필터에서 문자열을 비교할 수는 `eq` 고 `ne` 연산자입니다. 이러한 비교는 대/소문자 구분 합니다.

## <a name="examples"></a>예

일치 문서 위치를 `Rating` 필드는 3, 5 사이입니다.

    Rating ge 3 and Rating le 5

일치 문서 위치를 `Location` 필드는 지정 된 위도 및 경도에서 2 킬로미터 미만:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

일치 문서 위치를 `LastRenovationDate` 필드는 2015 년 1 월 1 일, UTC 기준 자정 보다 크거나 같은 경우:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

일치 위치를 문서화 합니다 `Details/Sku` 필드가 아닙니다 `null`:

    Details/Sku ne null

하나 이상의 공간 형식 "Deluxe 공간"에 있는 호텔에 대 한 문서를 찾습니다. 문자열은 `Rooms/Type` 필드 필터를 정확 하 게 일치:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
