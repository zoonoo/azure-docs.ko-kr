---
title: 단순 쿼리 구문
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 전체 텍스트 검색 쿼리에 사용 되는 단순 쿼리 구문에 대 한 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516591"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 단순 쿼리 구문

Azure Cognitive Search는 두 가지 Lucene 기반 쿼리 언어 즉, [단순 쿼리 파서와](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) [lucene 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)를 구현 합니다. 단순 파서는 보다 유연 하며, 완벽 하 게 구성 되지 않은 경우에도 요청을 해석 하려고 합니다. 이러한 유연성으로 인해 Azure Cognitive Search의 쿼리에 대 한 기본값입니다.

단순 구문은 **`search`** 같은 요청에서 및 식에 사용 되는 [OData 구문과](query-odata-filter-orderby-syntax.md) 혼동 하지 않도록 [검색 문서 (REST API)](/rest/api/searchservice/search-documents) 요청의 매개 변수에 전달 되는 쿼리 식에 사용 됩니다 [**`$filter`**](search-filters.md) [**`$orderby`**](search-query-odata-orderby.md) . OData 매개 변수에는 쿼리를 생성 하 고 문자열을 이스케이프 처리 하는 다양 한 구문과 규칙이 있습니다.

단순 파서는 [Apache Lucene Simple 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 클래스를 기반으로 하지만 Cognitive Search의 구현에서 유사 항목 검색을 제외 합니다. [유사 항목 검색](search-query-fuzzy.md)을 수행 해야 하는 경우 대신 대체 [전체 Lucene 쿼리 구문을](query-lucene-syntax.md) 사용해 보세요.

## <a name="example-simple-syntax"></a>예 (단순 구문)

**`queryType`** 는 아래에 설정 되어 있지만 대체 형식에서 되돌리면 안 되는 경우 기본값 이며 생략할 수 있습니다. 다음 예에서는 일치 하는 모든 문서에 "pool"이 포함 되어야 한다는 요구 사항이 있는 독립적인 용어를 검색 합니다.

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

**`searchMode`** 매개 변수는이 예제와 관련이 있습니다. 쿼리에 부울 연산자가 있을 때마다 일반적으로를 설정 하 여 `searchMode=all` *모든* 조건이 일치 하는지 확인 해야 합니다. 그렇지 않은 경우에 `searchMode=any` 는 전체 자릿수에 대해 회수를 우선적으로 사용 하는 기본값을 사용할 수 있습니다.

추가 예제는 [간단한 쿼리 구문 예제](search-query-simple-examples.md)를 참조 하세요. 쿼리 요청 및 매개 변수에 대 한 자세한 내용은 [문서 검색 (REST API)](/rest/api/searchservice/Search-Documents)을 참조 하세요.

## <a name="keyword-search-on-terms-and-phrases"></a>용어 및 구에 대 한 키워드 검색

**`search`** 매개 변수에 전달 되는 문자열에는 지원 되는 언어, 부울 연산자, 선행 연산자, 와일드 카드 또는 접두사 문자 ("시작" 쿼리, 이스케이프 문자 및 URL 인코딩 문자)의 용어 또는 구가 포함 될 수 있습니다. **`search`** 매개 변수는 선택 사항입니다. 지정 하지 않으면 검색 ( `search=*` 또는 `search=" "` )에서 상위 50 문서를 임의 (순위가 지정 되지 않은) 순서로 반환 합니다.

+ *용어 검색* 은 하나 이상의 용어로 이루어진 쿼리 이며, 용어 중 하나는 일치 하는 것으로 간주 됩니다.

+ *구 검색* 은 따옴표로 묶인 정확한 구입니다 `" "` . 예를 들어, ```Roach Motel``` (따옴표 제외)는 ```Roach``` 임의의 순서로 및/또는 모든 위치를 포함 하는 문서를 검색 합니다 ```Motel``` ```"Roach Motel"``` . (따옴표 포함)은 해당 전체 구를 포함 하는 문서를 해당 순서 대로 일치 시킵니다 (어휘 분석은 여전히 적용 됨). 

  검색 클라이언트에 따라 구 검색에서 따옴표를 이스케이프 해야 할 수도 있습니다. 예를 들어 POST 요청에서 Postman의 경우 요청 본문에서 검색 하는 구가 `"Roach Motel"` 로 지정 됩니다 `"\"Roach Motel\""` .

기본적으로 매개 변수에 전달 된 모든 용어 또는 구는 **`search`** 어휘 분석을 거칩니다. 사용 중인 분석기의 토큰화 동작을 이해 하 고 있어야 합니다. 종종 쿼리 결과가 예기치 않은 경우 쿼리 시간에 사용 약관을 토큰화 하는 방법에 대 한 이유를 추적할 수 있습니다.

하나 이상의 단어를 포함하는 모든 텍스트는 쿼리 실행의 유효한 시작점으로 간주됩니다. Azure Cognitive Search는 텍스트 분석 중에 발견 된 변형을 비롯 하 여 모든 용어 또는 모든 용어를 포함 하는 문서와 일치 합니다.

이와 같이 간단 하 게 하기 때문에 Azure Cognitive Search에서 예기치 않은 결과를 발생 *시킬 수* 있는 쿼리 실행의 한 가지 측면은 입력 문자열에 추가 된 용어 및 연산자가 추가 될 때 검색 결과를 줄이는 대신 향상 됩니다. 이 확장이 실제로 발생 하는지 여부는 NOT 연산자의 포함 여부에 따라 달라 지 **`searchMode`** 며, AND 또는 or 동작 측면에서 해석 되지 않는 방법을 결정 하는 매개 변수 설정과 함께 사용 됩니다. 자세한 내용은 [부울 연산자](#boolean-operators)에서 NOT 연산자를 참조 하세요.

## <a name="boolean-operators"></a>부울 연산자

쿼리 문자열에 부울 연산자를 포함 하 여 일치 하는 항목의 전체 자릿수를 향상할 수 있습니다. 단순 구문에서 부울 연산자는 문자 기반입니다. 단어 및와 같은 텍스트 연산자는 지원 되지 않습니다.

| 문자 | 예제 | 사용량 |
|----------- |--------|-------|
| `+` | `pool + ocean` | AND 작업입니다. 예를 들어 `pool + ocean` 문서에 두 용어를 모두 포함 해야 하도록 규정.|
| `|` | `pool | ocean` | 또는 작업 중 하나가 발견 되 면 일치 하는 항목을 찾습니다. 이 예제에서 쿼리 엔진은 또는 둘 다를 포함 하는 문서에 대해 일치 하는 항목을 반환 `pool` `ocean` 합니다. OR은 기본 결합 연산자이므로 `pool ocean`가 `pool | ocean`와 동일한 것처럼 생략할 수도 있습니다.|
| `-` | `pool – ocean` | NOT 연산은 용어를 제외 하는 문서에 대 한 일치 항목을 반환 합니다. <br/><br/>NOT 식에 대해 예상 되는 동작을 가져오려면 요청에 대해를 설정 하는 것이 좋습니다 **`searchMode=all`** . 그렇지 않은 경우의 기본값에서는 **`searchMode=any`** 와 일치 하는 항목을 포함 하지 않는 모든 문서에서 일치 하는 항목을 가져오고 `pool` `ocean` 많은 문서를 사용할 수 있습니다. **`searchMode`** 쿼리 요청의 매개 변수는 not 연산자를 사용 하는 용어가 쿼리의 다른 용어와 함께 and 또는 ORed 여부를 제어 합니다 ( `+` `|` 다른 조건에 or 연산자가 없는 경우). 를 사용 하면 **`searchMode=all`** 더 작은 결과를 포함 하 여 쿼리의 전체 자릿수를 늘리고 기본적으로-는 "AND NOT"으로 해석 됩니다. <br/><br/>설정을 결정할 때 **`searchMode`** 다양 한 응용 프로그램의 쿼리에 대 한 사용자 상호 작용 패턴을 고려 합니다. 정보를 검색 하는 사용자는 더 많은 기본 제공 탐색 구조를 포함 하는 전자 상거래 사이트와는 달리 쿼리에 연산자를 포함할 가능성이 높습니다. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>쿼리 접두사

"시작" 쿼리의 경우 `*` 나머지 용어에 대 한 자리 표시자로 접미사 연산자 ()를 추가 합니다. 접미사 연산자를 추가 하려면 접두사 쿼리를 하나 이상의 영숫자 문자로 시작 해야 합니다.

| 문자 | 예제 | 사용량 |
|----------- |--------|-------|
| `*` | `lingui*` "언어적" 또는 "linguini"에서 일치 합니다. | 별표 ( `*` )는 대/소문자를 무시 하 고 임의의 길이의 문자를 하나 이상 나타냅니다.  |

필터와 마찬가지로 접두사 쿼리는 정확히 일치 하는 항목을 찾습니다. 따라서 관련성 점수 매기기는 없습니다 (모든 결과가 1.0의 검색 점수를 수신 함). 특히 인덱스가 크고 접두사가 작은 수의 문자로 구성 된 경우 접두사 쿼리 속도가 느려질 수 있습니다. Edge n-영문법 토큰화와 같은 대체 방법은 더 빠르게 수행할 수 있습니다.

단순 구문은 접두사 일치만 지원 합니다. 용어 끝 또는 중간에 대해 접미사 또는 중 위 일치를 사용 하는 경우 [와일드 카드 검색에 전체 Lucene 구문을](query-lucene-syntax.md#bkmk_wildcard)사용 합니다.

## <a name="escaping-search-operators"></a>검색 연산자 이스케이프  

단순 구문에서 검색 연산자는 다음과 같은 문자를 포함 합니다. `+ | " ( ) ' \`  

이러한 문자가 인덱스에 있는 토큰의 일부인 경우 쿼리에서 단일 백슬래시 ()를 접두사로 사용 하 여 이스케이프 합니다 `\` . 예를 들어 전체 용어 토큰화를 위해 사용자 지정 분석기를 사용 하 고 인덱스에 "Luxury + 호텔" 문자열이 포함 되어 있다고 가정 합니다. 이 토큰과 정확히 일치 하는 항목을 가져오려면 이스케이프 문자를 삽입 `search=luxury\+hotel` 합니다.

보다 일반적인 사례를 단순하게 만들기 위해이 규칙에는 이스케이프가 필요 하지 않은 두 가지 예외가 있습니다.  

+ NOT 연산자는 `-` 공백 뒤의 첫 번째 문자인 경우에만 이스케이프 해야 합니다. 가 `-` 중간에 나타나면 (예:의 경우 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ) 이스케이프를 건너뛸 수 있습니다.

+ 접미사 연산자는 `*` 공백 앞의 마지막 문자인 경우에만 이스케이프 해야 합니다. 가 `*` 중간에 나타나면 (예:의 경우 `4*4=16` ) 이스케이프가 필요 하지 않습니다.

> [!NOTE]  
> 기본적으로 표준 분석기는 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)중 하이픈, 공백, 앰퍼샌드 및 기타 문자에서 단어를 삭제 하 고 중단 합니다. 쿼리 문자열에서 특수 문자를 유지 해야 하는 경우 인덱스에 해당 문자를 유지 하는 분석기가 필요할 수 있습니다. 일부 옵션에는 하이픈을 넣은 단어를 유지 하는 Microsoft 자연어 [분석기](index-add-language-analyzers.md)또는 보다 복잡 한 패턴을 위한 사용자 지정 분석기가 포함 됩니다. 자세한 내용은 [부분 용어, 패턴 및 특수 문자](search-query-partial-matching.md)를 참조 하세요.

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL에서 안전하지 않은 문자 및 예약된 문자 인코딩

모든 안전 하지 않은 문자 및 예약 된 문자가 URL로 인코딩 되었는지 확인 합니다. 예를 들어, ' # '은 URL의 조각/앵커 식별자 이므로 안전 하지 않은 문자입니다. 이 문자는 URL에서 사용할 경우 `%23`으로 인코딩해야 합니다. ' & ' 및 ' = '는 매개 변수를 구분 하 고 Azure Cognitive Search에서 값을 지정 하는 예약 된 문자의 예입니다. 자세한 내용은 [RFC1738: URL (Uniform Resource locator)](https://www.ietf.org/rfc/rfc1738.txt)을 참조 하세요.

안전하지 않은 문자는 ``" ` < > # % { } | \ ^ ~ [ ]``입니다. 예약된 문자는 `; / ? : @ = + &`입니다.

## <a name="special-characters"></a>특수 문자

일부 경우에는 ' ❤ '이 모 지 또는 ' € ' 기호와 같은 특수 문자를 검색 해야 할 수 있습니다. 이 경우 사용 하는 분석기에서 해당 문자를 필터링 하지 않는지 확인 합니다. 표준 분석기는 인덱스에서 제외 하 고 많은 특수 문자를 건너뜁니다.

특수 문자를 토큰화 하는 분석기에는 "공백" 분석기가 포함 됩니다 .이 분석기는 공백으로 구분 되는 문자 시퀀스를 토큰으로 간주 하므로 "❤" 문자열은 토큰으로 간주 됩니다. 또한 Microsoft English analyzer ("en-us")와 같은 언어 분석기는 "€" 문자열을 토큰으로 사용 합니다. 분석기를 [테스트](/rest/api/searchservice/test-analyzer) 하 여 지정 된 쿼리에 대해 생성 되는 토큰을 확인할 수 있습니다.

유니코드 문자를 사용 하는 경우 쿼리 url에서 기호가 제대로 이스케이프 되었는지 확인 합니다. 예를 들어 "❤"은 이스케이프 시퀀스를 사용 `%E2%9D%A4+` 합니다. Postman은이 변환을 자동으로 수행 합니다.  

## <a name="precedence-grouping"></a>우선 순위 (그룹화)

괄호를 사용(괄호문 내에 연산자를 포함)하여 하위 쿼리를 만들 수 있습니다. 예를 들어, `motel+(wifi|luxury)`는 용어 motel과 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다.|

## <a name="query-size-limits"></a>쿼리 크기 제한

애플리케이션이 검색 쿼리를 프로그래밍 방식으로 생성하는 경우 쿼리가 제한 없는 크기로 생성되지 않도록 디자인하는 것이 좋습니다. 

+ GET의 경우 URL의 길이는 8kb를 초과할 수 없습니다.

+ 요청 본문에 및 등의 기타 매개 변수를 포함 하는 POST (및 기타 모든 요청)의 경우 `search` `filter` `orderby` 최대 크기는 16mb입니다. 여기서의 최대 절 수 `search` (and, OR 등으로 구분 된 식)는 1024입니다. 또한 한 쿼리의 개별 용어 크기도 약 32KB로 제한됩니다. 자세한 내용은 [API 요청 제한](search-limits-quotas-capacity.md#api-request-limits)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

프로그래밍 방식으로 쿼리를 생성 하는 경우 [Azure Cognitive Search의 전체 텍스트 검색](search-lucene-query-architecture.md) 을 검토 하 여 쿼리 처리 단계와 텍스트 분석의 의미를 이해 합니다.

다음 문서를 검토 하 여 쿼리 생성에 대해 자세히 알아볼 수도 있습니다.

+ [단순 검색을 위한 쿼리 예제](search-query-simple-examples.md)
+ [전체 Lucene 검색에 대 한 쿼리 예제](search-query-lucene-examples.md)
+ [문서 검색 REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene 쿼리 구문](query-lucene-syntax.md)
+ [필터링 및 Select (OData) 식 구문](query-odata-filter-orderby-syntax.md)