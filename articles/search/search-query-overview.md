---
title: 쿼리 유형
titleSuffix: Azure Cognitive Search
description: 자유 텍스트, 필터, 자동 완성 및 제안, 지역 검색, 시스템 쿼리 및 문서 조회를 포함 하 여 Cognitive Search에서 지원 되는 쿼리 형식에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 7277ad060c57b44d633054c4fc4d29d151bd7192
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400814"
---
# <a name="querying-in-azure-cognitive-search"></a>Azure Cognitive Search에서 쿼리

Azure Cognitive Search는 자유 텍스트 검색에서 항상 지정 된 쿼리 패턴에 이르기까지 광범위 한 시나리오를 지 원하는 풍부한 쿼리 언어를 제공 합니다. 이 문서에서는 쿼리 요청 및 만들 수 있는 쿼리의 종류에 대해 설명 합니다.

Cognitive Search 쿼리는 **`search`** 쿼리 실행을 알리고 응답의 모양을 지정 하는 매개 변수를 사용 하 여 라운드트립 작업을 전체적으로 지정 합니다. 매개 변수 및 파서는 쿼리 요청 유형을 결정 합니다. 다음 쿼리 예제는 [호텔-샘플 인덱스](search-get-started-portal.md) 문서 컬렉션을 대상으로 하는 [검색 문서 (REST API)](/rest/api/searchservice/search-documents)를 사용 하 여 부울 연산자를 사용 하는 자유 텍스트 쿼리입니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

쿼리 실행 중에 사용 되는 매개 변수는 다음과 같습니다.

+ **`queryType`**[기본 단순 쿼리 파서](search-query-simple-examples.md) (전체 텍스트 검색에 최적) 인 파서 또는 정규식, 근접 검색, 유사 항목 및 와일드 카드 검색 등의 고급 쿼리 구문에 사용 되는 [전체 Lucene 쿼리 파서](search-query-lucene-examples.md) 를 설정 합니다.

+ **`search`** 연산자를 사용 하거나 사용 하지 않고 일치 조건, 일반적으로 전체 용어 또는 문구를 제공 합니다. 인덱스 스키마에서 *검색* 가능으로 특성을 지정 하는 모든 필드는이 매개 변수에 대 한 후보입니다.

+ **`searchFields`** 쿼리 실행을 검색 가능한 특정 필드로 제한 합니다.

응답을 셰이핑 하는 데 사용 되는 매개 변수:

+ **`select`** 응답에 반환할 필드를 지정 합니다. 인덱스에서 *검색할* 수 있는 것으로 표시 된 필드만 select 문에서 사용할 수 있습니다.

+ **`top`** 지정 된 수의 가장 일치 하는 문서를 반환 합니다. 이 예제에서는 10 개의 적중만 반환 합니다. Top 및 skip (표시 되지 않음)을 사용 하 여 결과를 표시할 수 있습니다.

+ **`count`** 전체 인덱스의 문서 수가 전체적으로 일치 하는지 여부를 알려 줍니다 .이는 반환 되는 것 보다 많을 수 있습니다. 

+ **`orderby`** 등급 또는 위치와 같은 값을 기준으로 결과를 정렬 하려는 경우에 사용 됩니다. 그렇지 않으면 기본값은 관련성 점수를 사용 하 여 결과의 순위를 결정 하는 것입니다. 필드는이 매개 변수에 대 한 후보가 되도록 *정렬* 가능한 것으로 특성을 지정 해야 합니다.

위의 목록은 전체 지원 이지만 완전 하지는 않습니다. 쿼리 요청에 대 한 전체 매개 변수 목록을 보려면 [문서 검색 (REST API)](/rest/api/searchservice/search-documents)을 참조 하세요.

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>쿼리 유형

몇 가지 주목할 만한 예외를 제외 하 고 쿼리 요청은 빠른 검색을 위해 구조화 된 반전 된 인덱스를 반복 합니다. 이러한 인덱스는 검색 문서 수에 관계 없이 잠재적으로 모든 필드에서 일치 항목을 찾을 수 있습니다. Cognitive Search에서 일치 항목을 찾기 위한 기본 방법은 전체 텍스트 검색 또는 필터 중 하나 이지만 자동 완성, 지리적 위치 검색 등의 기타 잘 알려진 검색 환경을 구현할 수도 있습니다. 이 문서의 나머지 부분에서는 Cognitive Search의 쿼리를 요약 하 고 추가 정보 및 예제에 대 한 링크를 제공 합니다.

## <a name="full-text-search"></a>전체 텍스트 검색

검색 앱에서 용어 입력을 수집 하는 검색 상자를 포함 하는 경우 전체 텍스트 검색은 해당 환경을 지 원하는 쿼리 작업 일 것입니다. 전체 텍스트 검색은 **`search`** 인덱스의 모든 *검색* 가능 필드에서 매개 변수에 전달 된 용어 또는 구를 허용 합니다. 쿼리 문자열의 선택적 부울 연산자는 포함 또는 제외 조건을 지정할 수 있습니다. 단순 파서와 전체 파서는 전체 텍스트 검색을 지원 합니다.

Cognitive Search에서 전체 텍스트 검색은 Apache Lucene 쿼리 엔진을 기반으로 합니다. 전체 텍스트 검색의 쿼리 문자열은 검색을 보다 효율적으로 수행 하기 위해 어휘 분석을 수행 합니다. 분석에는 대/소문자를 모두 포함 하 고, "the"와 같은 중지 단어를 제거 하 고, 용어를 기본 루트 형식으로 축소 기본 분석기는 표준 Lucene입니다.

일치 용어를 찾을 때 쿼리 엔진은 문서 키 또는 ID를 사용 하 여 일치 항목이 포함 된 검색 문서를 다시 구성 하 여 필드 값을 조합 하 고, 관련성 순서로 문서 순위를 지정 하 고, 지정 된 경우 응답에 상위 50 (기본값)을 반환 합니다 **`top`** .

전체 텍스트 검색을 구현 하는 경우 콘텐츠가 토큰화 되는 방법을 이해 하면 모든 쿼리 변칙을 디버그 하는 데 도움이 됩니다. 하이픈을 넣은 문자열이 나 특수 문자를 쿼리하면 인덱스에 올바른 토큰이 포함 되어 있는지 확인 하기 위해 기본 표준 Lucene 이외의 분석기를 사용 하는 것이 필요할 수 있습니다. 어휘 분석을 수정 하는 [언어 분석기](index-add-language-analyzers.md#language-analyzer-list) 또는 [특수 분석기](index-add-custom-analyzers.md#AnalyzerTable) 를 사용 하 여 기본값을 재정의할 수 있습니다. 한 가지 예는 필드의 전체 내용을 단일 토큰으로 처리 하는 [키워드](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) 입니다. 우편 번호, ID 및 일부 제품 이름과 같은 데이터에 유용합니다. 자세한 내용은 [부분 용어 검색 및 특수 문자가 포함 된 패턴](search-query-partial-matching.md)을 참조 하세요.

큰 텍스트 블록 (내용 필드 또는 긴 설명)이 포함 된 인덱스에서 많은 부울 연산자를 많이 사용 하는 경우에는 매개 변수를 사용 하 여 쿼리를 테스트 하 여 **`searchMode=Any|All`** 해당 설정이 부울 검색에 미치는 영향을 평가 해야 합니다.

## <a name="autocomplete-and-suggested-queries"></a>자동 완성 및 제안 된 쿼리

[자동 완성 또는 제안 된 결과](search-autocomplete-tutorial.md) 는 **`search`** 검색 시 검색 환경에서 부분 문자열 입력 (각 문자 이후)을 기반으로 연속 쿼리 요청을 발생 시키는 대체 방법입니다. **`autocomplete`** **`suggestions`** [이 자습서](tutorial-csharp-type-ahead-and-suggestions.md)에 설명 된 대로 및 매개 변수를 함께 사용 하거나 개별적으로 사용할 수 있지만에서는 사용할 수 없습니다 **`search`** . 완료 된 용어와 제안 된 쿼리는 모두 인덱스 콘텐츠에서 파생 됩니다. 엔진은 인덱스에 존재 하지 않는 문자열 또는 제안을 반환 하지 않습니다. 자세한 내용은 [자동 완성 (REST API)](/rest/api/searchservice/autocomplete) 및 [제안 (REST API)](/rest/api/searchservice/suggestions)을 참조 하세요.

## <a name="filter-search"></a>필터 검색

필터는 Cognitive Search 포함 된 앱에서 널리 사용 됩니다. 응용 프로그램 페이지에서 필터는 사용자가 직접 필터링 할 수 있도록 링크 탐색 구조에서 패싯으로 시각화 되는 경우가 많습니다. 또한 필터는 인덱싱된 콘텐츠의 조각을 노출 하기 위해 내부적으로 사용 됩니다. 예를 들어 제품 범주에 대 한 필터를 사용 하 여 검색 페이지를 초기화 하거나 인덱스에 영어와 프랑스어의 필드가 모두 포함 된 경우 언어를 초기화할 수 있습니다.

다음 표에서 설명 하는 것 처럼 특수 쿼리 양식을 호출 하는 필터가 필요할 수도 있습니다. 지정 되지 않은 검색 ( **`search=*`** ) 또는 용어, 구, 연산자 및 패턴이 포함 된 쿼리 문자열을 사용 하 여 필터를 사용할 수 있습니다.

| 필터 시나리오 | 설명 |
|-----------------|-------------|
| 범위 필터 | Azure Cognitive Search에서는 필터 매개 변수를 사용 하 여 범위 쿼리를 작성 합니다. 자세한 내용 및 예제는 [범위 필터 예](search-query-simple-examples.md#example-4-range-filters)를 참조 하세요. |
| 지리적 위치 검색 | 검색 가능한 필드가 [GeographyPoint](/rest/api/searchservice/supported-data-types)인 경우 "주변 찾기" 또는 맵 기반 검색 컨트롤에 대 한 필터 식을 만들 수 있습니다. 지리적 검색을 구동 하는 필드에는 좌표가 포함 됩니다. 자세한 내용 및 예제는 [지역 검색 예제](search-query-simple-examples.md#example-5-geo-search)를 참조 하세요. |
| 패싯 탐색 | 패싯의 이벤트에 대 한 응답으로 필터를 호출 하면 패싯 탐색 구조가 사용자에 게 전달 되는 탐색에 사용 됩니다 `onclick` . 따라서 패싯 및 필터는 직접 이동 합니다. 패싯 탐색을 추가 하는 경우에는 환경을 완료 하는 필터가 필요 합니다. 자세한 내용은 [패싯 필터를 작성 하는 방법](search-filters-facets.md)을 참조 하세요. |

> [!NOTE]
> 필터 식에 사용 되는 텍스트는 쿼리를 처리 하는 동안 분석 되지 않습니다. 텍스트 입력은 일치에 성공 하거나 실패 하는 대/소문자를 구분 하는 문자 패턴으로 가정 됩니다. 필터 식은 [OData 구문을](query-odata-filter-orderby-syntax.md) 사용 하 여 생성 되 고 **`filter`** 인덱스의 *필터링* 가능한 모든 필드에서 매개 변수로 전달 됩니다. 자세한 내용은 [Azure Cognitive Search의 필터](search-filters.md)를 참조 하세요.

## <a name="document-look-up"></a>문서 조회

앞에서 설명한 쿼리 양식과 달리이 쿼리는 [ID로 단일 검색 문서](/rest/api/searchservice/lookup-document)를 검색 하 고 해당 인덱스 검색 또는 검색은 검색 하지 않습니다. 하나의 문서만 요청 되 고 반환 됩니다. 사용자가 검색 결과에서 항목을 선택 하는 경우 문서를 검색 하 고 필드를 사용 하 여 세부 정보 페이지를 채우면 일반적인 응답이 되며 문서 조회는이를 지 원하는 작업입니다.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>고급 검색: 유사 항목, 와일드 카드, 근접, regex

고급 쿼리 형태는 전체 Lucene 파서 및 특정 쿼리 동작을 트리거하는 연산자에 따라 달라 집니다.

| 쿼리 유형 | 사용량 | 예제 및 자세한 정보 |
|------------|--------|------------------------------|
| [필드 지정 검색](query-lucene-syntax.md#bkmk_fields) | **`search`**  변수에 **`queryType=full`**  | 단일 필드를 대상으로 복합 쿼리 식을 작성합니다. <br/>[필드 지정 검색 예제](search-query-lucene-examples.md#example-2-fielded-search) |
| [유사 항목 검색](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** 변수에 **`queryType=full`** | 유사한 구조 또는 철자가 포함된 용어를 검색합니다. <br/>[유사 항목 검색 예제](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [근접 검색](query-lucene-syntax.md#bkmk_proximity) | **`search`** 변수에 **`queryType=full`** | 문서에서 서로 가까이 있는 용어를 찾습니다. <br/>[근접 검색 예제](search-query-lucene-examples.md#example-4-proximity-search) |
| [용어 상승](query-lucene-syntax.md#bkmk_termboost) | **`search`** 변수에 **`queryType=full`** | 승격된 용어가 포함된 문서는 그렇지 않은 다른 문서보다 상대적으로 높은 순위를 매깁니다. <br/>[용어 상승 예제](search-query-lucene-examples.md#example-5-term-boosting) |
| [정규식 검색](query-lucene-syntax.md#bkmk_regex) | **`search`** 변수에 **`queryType=full`** | 정규식의 콘텐츠를 기반으로 검색합니다. <br/>[정규식 예제](search-query-lucene-examples.md#example-6-regex) |
|  [와일드 카드 또는 접두사 검색](query-lucene-syntax.md#bkmk_wildcard) | **`search`** 매개 변수를 *_`~`_* 사용 **`?`** 합니다. **`queryType=full`**| 접두사와 물결표(`~`) 또는 단일 문자(`?`)를 기반으로 검색합니다. <br/>[와일드카드 검색 예제](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>다음 단계

쿼리 구현에 대 한 자세한 내용을 보려면 각 구문에 대 한 예를 검토 하세요. 전체 텍스트 검색을 처음 접하는 경우 쿼리 엔진의 기능을 좀 더 잘 선택할 수 있습니다.

+ [단순 쿼리 예제](search-query-simple-examples.md)
+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)