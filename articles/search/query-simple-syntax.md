---
title: 단순 쿼리 구문
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색 쿼리에 사용되는 단순 쿼리 구문에 대한 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516591"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 단순 쿼리 구문

Azure Cognitive Search는 두 가지 Lucene 기반 쿼리 언어인 [단순 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 및 [Lucene 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)를 구현합니다. 단순 파서는 보다 유연하며, 완벽하게 구성되지 않은 요청도 해석하려고 합니다. 이러한 유연성으로 인해 Azure Cognitive Search에서 쿼리에 대한 기본 파서로 사용됩니다.

단순 구문은 동일한 요청의 [ **`$filter`**](search-filters.md) 및 [ **`$orderby`**](search-query-odata-orderby.md) 식에 사용되는 [구문](query-odata-filter-orderby-syntax.md)과 혼동하지 않도록 [문서 검색(REST API)](/rest/api/searchservice/search-documents)의 **`search`** 매개 변수에 전달되는 쿼리 식에 사용됩니다. OData 매개 변수에는 쿼리 생성, 문자열 이스케이프 등의 작업을 위한 다양한 구문과 규칙이 있습니다.

단순 파서는 [Apache Lucene 단순 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 클래스를 기준으로 하지만 Cognitive Search의 구현에서는 유사 항목 검색이 제외됩니다. [유사 항목 검색](search-query-fuzzy.md)을 수행해야 하는 경우 대체 [전체 Lucene 쿼리 구문](query-lucene-syntax.md)을 대신 사용해 보세요.

## <a name="example-simple-syntax"></a>예(단순 구문)

아래에는 **`queryType`** 이 설정되어 있지만 기본값이므로 대체 형식에서 원래로 되돌리지 않는 한, 생략할 수 있습니다. 다음 예에서는 일치하는 모든 문서에 "pool"이 포함되어야 한다는 요구 사항에 따라 독립적인 용어를 검색합니다.

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

**`searchMode`** 매개 변수가 이 예제에 해당합니다. 쿼리에서 부울 연산자를 사용할 때마다 일반적으로 `searchMode=all`을 설정하여 *모든* 조건이 일치하는지 확인해야 합니다. 그렇지 않은 경우 정밀도보다 재현율을 고려하는 기본 `searchMode=any`를 사용할 수 있습니다.

추가 예제에 대해서는 [간단한 쿼리 구문 예제](search-query-simple-examples.md)를 참조하세요. 쿼리 요청 및 매개 변수에 대한 자세한 내용은 [문서 검색(REST API)](/rest/api/searchservice/Search-Documents)을 참조하세요.

## <a name="keyword-search-on-terms-and-phrases"></a>용어 및 구에 대한 키워드 검색

**`search`** 매개 변수에 전달되는 문자열에는 지원되는 언어의 용어나 구, 부울 연산자, 선행 연산자, 와일드카드 또는 접두사 문자("시작" 쿼리, 이스케이프 문자 및 URL 인코딩 문자)가 포함될 수 있습니다. **`search`** 매개 변수는 선택 사항입니다. 지정하지 않을 경우 검색(`search=*` 또는 `search=" "`)은 상위 50개 문서를 임의(무순위) 순서로 반환합니다.

+ *용어 검색* 은 하나 이상의 용어로 이루어진 쿼리로, 용어 중 하나라도 일치하면 일치하는 것으로 간주됩니다.

+ *구 검색* 은 따옴표 `" "`로 묶인 정확한 구입니다. 예를 들어, ```Roach Motel```(따옴표 제외)은 순서 및 위치에 관계없이 ```Roach``` 및/또는 ```Motel```을 포함하는 문서를 검색하지만, ```"Roach Motel"```(따옴표 포함)은 해당 전체 구를 해당 순서로 포함하는 문서만 검색합니다(어휘 분석은 계속 적용됨). 

  검색 클라이언트에 따라 구 검색에서 따옴표를 이스케이프해야 할 수도 있습니다. 예를 들어, POST 요청의 Postman에서 요청 본문에서 `"Roach Motel"`에 대한 구 검색은 `"\"Roach Motel\""`로 지정됩니다.

기본적으로 **`search`** 매개 변수에 전달된 모든 용어 또는 구는 어휘 분석을 거칩니다. 사용 중인 분석기의 토큰화 동작을 이해해야 합니다. 종종 쿼리 결과가 예기치 않은 경우 쿼리 타임에 용어가 토큰화되는 방식에 대한 이유를 추적할 수 있습니다.

하나 이상의 단어를 포함하는 모든 텍스트는 쿼리 실행의 유효한 시작점으로 간주됩니다. Azure Cognitive Search는 텍스트 분석 중에 발견된 변형을 포함하여 모든 용어를 포함하는 문서를 검색합니다.

간단해 보일 수 있지만, Azure Cognitive Search에는 더 많은 용어와 연산자를 입력 문자열에 추가할 때 검색 결과를 줄이기보다 늘릴 수 *있는* 쿼리 실행의 측면이 있습니다. 이러한 증가가 실제로 발생하는지 여부는 NOT이 AND 또는 OR 동작과 관련해서 해석되는 방식을 결정하는 **`searchMode`** 매개 변수와 NOT 연산자를 포함하는지에 따라 좌우됩니다. 자세한 내용은 [부울 연산자](#boolean-operators)에서 NOT 연산자를 참조하세요.

## <a name="boolean-operators"></a>부울 연산자

쿼리 문자열에 부울 연산자를 포함하여 일치하는 항목의 정밀도를 높일 수 있습니다. 단순 구문에서 부울 연산자는 문자를 기준으로 합니다. 단어 AND와 같은 텍스트 연산자는 지원되지 않습니다.

| 문자 | 예제 | 사용량 |
|----------- |--------|-------|
| `+` | `pool + ocean` | AND 연산입니다. 예를 들어 `pool + ocean`은 문서에 두 용어를 모두 포함해야 한다고 규정합니다.|
| `|` | `pool | ocean` | OR 연산은 두 용어 중 하나를 찾으면 일치하는 항목으로 간주합니다. 이 예제에서 쿼리 엔진은 `pool` 또는 `ocean` 양쪽 모두를 포함하는 문서에 일치 하는 항목을 반환합니다. OR은 기본 결합 연산자이므로 `pool ocean`가 `pool | ocean`와 동일한 것처럼 생략할 수도 있습니다.|
| `-` | `pool – ocean` | NOT 연산은 해당 용어를 제외한 문서에 일치하는 항목을 반환합니다. <br/><br/>NOT 식에 대해 예상되는 동작을 구현하려면 요청에 대해 **`searchMode=all`** 을 설정하는 것이 좋습니다. 그러지 않고 기본값인 **`searchMode=any`** 를 사용할 때는 `pool`에 대한 일치 항목과 `ocean`을 포함하지 않는 모든 문서(상당히 많을 수 있음)가 일치 항목으로 간주됩니다. 쿼리 요청의 **`searchMode`** 매개 변수는 NOT 연산자를 사용하는 용어가 쿼리의 다른 용어와 함께 ANDed 또는 ORed 여부를 제어합니다(다른 조건에 `+`또는 `|` 연산자가 없는 경우). **`searchMode=all`** 을 사용하는 경우 더 적은 수의 결과를 포함하여 쿼리 정확도를 높이고, 기본적으로 -는 "AND NOT"으로 해석됩니다. <br/><br/>**`searchMode`** 설정을 결정할 때 다양한 애플리케이션의 쿼리에 대한 사용자 상호 작용 패턴을 고려 합니다. 정보를 검색하는 사용자는 더 많은 기본 제공 탐색 구조를 포함하는 전자 상거래 사이트와는 달리 쿼리에 연산자를 포함할 가능성이 높습니다. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>접두사 쿼리

"다음으로 시작함" 쿼리의 경우 접미사 연산자(`*`)를 용어의 나머지에 대한 자리 표시자로 추가합니다. 접미사 연산자를 추가하려면 접두사 쿼리를 하나 이상의 영숫자 문자로 시작해야 합니다.

| 문자 | 예제 | 사용량 |
|----------- |--------|-------|
| `*` | `lingui*`는 "linguistic" 또는 "linguini"를 일치 항목으로 간주합니다. | 별표(`*`)는 대/소문자를 무시하는 임의 길이를 갖는 하나 이상의 문자를 나타냅니다.  |

필터와 마찬가지로 접두사 쿼리는 정확히 일치하는 항목을 찾습니다. 따라서 관련성 점수 매기기는 없습니다(모든 결과가 1.0의 검색 점수를 받음). 특히 인덱스가 크고 접두사가 적은 수의 문자로 구성된 경우 접두사 쿼리 속도가 느려질 수 있습니다. 에지 n-Gram 토큰화와 같은 대체 방법은 더 빠르게 작동할 수 있습니다.

단순 구문은 접두사 일치만 지원합니다. 용어 끝 또는 중간에 대해 접미사 또는 중위 일치를 사용하는 경우 [와일드카드 검색의 전체 Lucene 구문](query-lucene-syntax.md#bkmk_wildcard)을 사용합니다.

## <a name="escaping-search-operators"></a>검색 연산자 이스케이프  

단순 구문에서 검색 연산자는 `+ | " ( ) ' \` 문자를 포함합니다.  

이러한 문자가 인덱스에 있는 토큰의 일부인 경우 쿼리에서 단일 백슬래시(`\`)를 접두사로 추가하여 이스케이프합니다. 예를 들어, 전체 용어 토큰화를 위해 사용자 지정 분석기를 사용했으며 인덱스에 "Luxury+Hotel" 문자열이 포함되어 있다고 가정합니다. 이 토큰과 정확히 일치하는 항목을 가져오려면 이스케이프 문자 `search=luxury\+hotel`을 삽입합니다.

좀 더 일반적인 경우에서 좀 더 간단히 나타내기 위해 이스케이프가 필요하지 않은 다음과 같은 예외 규칙 2가지를 살펴보겠습니다.  

+ NOT 연산자 `-`은 공백 뒤의 첫 번째 문자인 경우에만 이스케이프해야 합니다. `-`이 중간에 나타나면(예: `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) 이스케이프를 건너뛸 수 있습니다.

+ 접미사 연산자 `*`는 공백 앞의 마지막 문자인 경우에만 이스케이프해야 합니다. `*`가 중간에 나타나면(예: `4*4=16`) 이스케이프가 필요하지 않습니다.

> [!NOTE]  
> 기본적으로 표준 분석기는 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis) 동안 하이픈, 공백, 앰퍼샌드 및 기타 문자에서 단어를 삭제하고 끊습니다. 쿼리 문자열에 특수 문자를 유지해야 할 경우 인덱스에 해당 문자가 유지되는 분석기가 필요할 수 있습니다. 일부 옵션에는 하이픈을 넣은 단어를 유지하는 Microsoft 자연어 [분석기](index-add-language-analyzers.md)또는 보다 복잡한 패턴을 위한 사용자 지정 분석기가 포함됩니다. 자세한 내용은 [부분 용어, 패턴 및 특수 문자](search-query-partial-matching.md)를 참조하세요.

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL에서 안전하지 않은 문자 및 예약된 문자 인코딩

URL에서 안전하지 않은 문자 및 예약된 문자를 모두 인코딩하세요. 예를 들어 '#'은 URL에서 조각/앵커 식별자이므로 안전하지 않은 문자입니다. 이 문자는 URL에서 사용할 경우 `%23`으로 인코딩해야 합니다. '&'와 '='는 매개 변수를 구분하고 Azure Search에서 값을 지정하므로 예약된 문자에 포합됩니다. 자세한 내용은 [RFC1738: URL(Uniform Resource Locator)](https://www.ietf.org/rfc/rfc1738.txt)을 참조하세요.

안전하지 않은 문자는 ``" ` < > # % { } | \ ^ ~ [ ]``입니다. 예약된 문자는 `; / ? : @ = + &`입니다.

## <a name="special-characters"></a>특수 문자

경우에 따라 '❤' 이모지 또는 '€' 기호와 같은 특수 문자를 검색해야 할 수 있습니다. 이 경우 사용하는 분석기에서 해당 문자를 필터링하지 않는지 확인합니다. 표준 분석기는 인덱스에서 제외하고 많은 특수 문자를 건너뜁니다.

특수 문자를 토큰화하는 분석기에는 "공백" 분석기가 포함됩니다. 이 분석기는 공백으로 구분되는 문자 시퀀스를 토큰으로 간주하므로 "❤" 문자열은 토큰으로 간주됩니다. 또한 Microsoft English analyzer("en.microsoft")와 같은 언어 분석기는 "€" 문자열을 토큰으로 사용합니다. 분석기를 [테스트](/rest/api/searchservice/test-analyzer) 하여 지정된 쿼리에 생성되는 토큰을 확인할 수 있습니다.

유니코드 문자를 사용하는 경우 쿼리 URL에서 기호가 제대로 이스케이프되었는지 확인합니다. 예를 들어 "❤"는 이스케이프 시퀀스 `%E2%9D%A4+`를 사용합니다. Postman은 이 변환을 자동으로 수행합니다.  

## <a name="precedence-grouping"></a>우선 순위(그룹화)

괄호를 사용(괄호문 내에 연산자를 포함)하여 하위 쿼리를 만들 수 있습니다. 예를 들어, `motel+(wifi|luxury)`는 용어 motel과 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다.|

## <a name="query-size-limits"></a>쿼리 크기 제한

애플리케이션이 검색 쿼리를 프로그래밍 방식으로 생성하는 경우 쿼리가 제한 없는 크기로 생성되지 않도록 디자인하는 것이 좋습니다. 

+ GET의 경우 URL의 길이는 8KB를 초과할 수 없습니다.

+ 요청 본문에 `search` 및 기타 매개 변수(예: `filter` 및 `orderby`)가 포함되는 POST(및 기타 모든 요청)의 경우 최대 크기는 16MB입니다. 여기서 `search`의 최대 절 수(AND, OR 등으로 구분된 식)는 1024개입니다. 또한 한 쿼리의 개별 용어 크기도 약 32KB로 제한됩니다. 자세한 내용은 [API 요청 제한](search-limits-quotas-capacity.md#api-request-limits)을 참조하세요.

## <a name="next-steps"></a>다음 단계

프로그래밍 방식으로 쿼리를 생성하는 경우 [Azure Cognitive Search의 전체 텍스트 검색](search-lucene-query-architecture.md)을 검토하여 쿼리 처리 스테이지와 텍스트 분석의 의미를 이해합니다.

다음 문서를 검토하여 쿼리 생성에 대해 자세히 알아볼 수도 있습니다.

+ [단순 검색 위한 쿼리 예제](search-query-simple-examples.md)
+ [전체 Lucene 검색 쿼리 예제](search-query-lucene-examples.md)
+ [문서 검색 REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene 쿼리 구문](query-lucene-syntax.md)
+ [필터 및 Select(OData) 식 구문](query-odata-filter-orderby-syntax.md)