---
title: 검색 결과에 대한 필터
titleSuffix: Azure Cognitive Search
description: 사용자 보안 ID, 언어, 지리적 위치 또는 숫자 값별로 필터링하여 Microsoft Azure에서 호스팅되는 클라우드 검색 서비스인 Azure Cognitive Search의 쿼리에 대한 검색 결과를 줄입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191010"
---
# <a name="filters-in-azure-cognitive-search"></a>Azure 인지 검색의 필터 

*필터는* Azure 인지 검색 쿼리에 사용되는 문서를 선택하는 기준을 제공합니다. 필터링되지 않은 검색에서는 모든 문서가 인덱스에 포함됩니다. 필터는 문서의 하위 집합으로 검색 쿼리의 범위를 지정합니다. 예를 들어, 필터를 사용하면 전체 텍스트 검색을 특정 임계값 이상의 가격대에서 특정 브랜드 또는 색상을 가진 제품으로 제한할 수 있습니다.

구현 과정에서 필터 요구 사항이 있는 검색 환경도 일부 있기는 하지만 *값 기반* 조건을 사용하여 검색을 제한하려는 경우는 언제든지 필터를 사용할 수 있습니다("Simon & Schuster"에서 출판된 "논픽션" 카테고리를 찾기 위해 제품 유형 검색 범위를 "책"으로 지정).

특정 데이터 *구조*로 검색 대상을 설정하려는 경우(검색 범위를 고객 리뷰 필드로 지정) 아래에 대체 방법이 나와 있습니다.

## <a name="when-to-use-a-filter"></a>필터를 사용하는 경우

필터는 "내 주변 찾기", 패싯 탐색, 사용자가 볼 수 있는 문서만 표시하는 보안 필터를 비롯한 여러 검색 환경의 기초입니다. 이러한 환경 중 하나를 구현하는 경우 필터가 필요합니다. 지리적 위치 좌표, 사용자가 선택한 패싯 범주 또는 요청자의 보안 ID를 제공하는 검색 쿼리에 연결된 필터입니다.

다음은 예제 시나리오입니다.

1. 인덱스의 데이터 값에 따라 인덱스를 분할하는 필터를 사용합니다. 도시, 주택 유형 및 편의 시설이 포함된 스키마가 제공되면 조건(시애틀, 콘도, 해안가)을 충족하는 문서를 명시적으로 선택하기 위한 필터를 만들 수 있습니다. 

   동일한 입력을 사용하는 전체 텍스트 검색도 종종 비슷한 결과가 나오기는 하지만 필터는 인덱스의 콘텐츠에 대해 필터 용어가 정확하게 일치해야 하기 때문에 더 정확합니다. 

2. 검색 환경에 필터 요구 사항이 포함된 경우 필터를 사용합니다.

   * [패싯 탐색](search-faceted-navigation.md)은 사용자가 선택한 패싯 범주를 다시 전달하는 데 필터를 사용합니다.
   * 지리적 검색은 "내 주변 찾기" 앱에서 현재 위치의 좌표를 전달하는 데 필터를 사용합니다. 
   * 보안 필터는 보안 식별자를 필터 조건으로 전달합니다. 여기서 인덱스의 일치 항목은 문서에 액세스할 수 있는 권한을 위한 프록시 역할을 합니다.

3. 숫자 필드에서 조건을 검색하려는 경우 필터를 사용합니다. 

   숫자 필드는 문서에서 검색할 수 있고 검색 결과에 표시되기도 하지만, 개별적으로 검색할 수는 없습니다(전체 텍스트 검색에 적용). 숫자 데이터를 기반으로 하는 선택 조건이 필요한 경우 필터를 사용합니다.

### <a name="alternative-methods-for-reducing-scope"></a>범위를 줄이기 위한 대체 방법

검색 결과를 효과적으로 줄이고 싶을 때 필터 외에 다른 방법도 있습니다. 목적에 따라 이러한 대안이 더 나을 수 있습니다.

 + `searchFields` 쿼리 매개 변수는 검색을 특정 필드로 고정합니다. 예를 들어, 인덱스에서 영어와 스페인어 설명을 위한 별도의 필드를 제공하는 경우 searchFields를 사용하여 전체 텍스트 검색에 사용할 대상 필드를 지정할 수 있습니다. 

+ `$select` 매개 변수는 결과 집합에 포함할 필드를 지정하는 데 사용되며 호출 애플리케이션에 보내기 전에 효과적으로 응답을 조정합니다. 이 매개 변수는 쿼리를 구체화하거나 문서 컬렉션을 줄이지는 않지만 더 작은 응답이 목표인 경우 이 매개 변수를 고려하는 옵션입니다. 

두 매개 변수에 대한 자세한 내용은 [문서 검색 > 요청 > 쿼리 매개 변수](/rest/api/searchservice/search-documents#query-parameters)를 참조하세요.


## <a name="how-filters-are-executed"></a>필터 실행 방법

쿼리 시 필터 파서는 조건을 입력으로 받아들이고 식을 트리로 나타내는 원자성 부울 식으로 변환한 다음 인덱스의 필터링 가능한 필드를 통해 필터 트리를 평가합니다.

필터링은 검색과 함께 발생하며 문서 검색 및 관련성 점수를 위해 다운스트림 처리에 포함할 문서를 한정합니다. 검색 문자열과 페어링하면 필터는 후속 검색 작업의 리콜 집합을 효과적으로 줄입니다. 단독으로 사용되면(예를 들어 `search=*` 같이 쿼리 문자열이 비어 있는 경우) 필터 조건이 유일한 입력입니다. 

## <a name="defining-filters"></a>필터 정의
필터는 [Azure 인지 검색에서 지원되는 OData V4 구문의 하위 집합을](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)사용하여 관절된 OData 표현식입니다. 

각 **검색** 작업에 대해 하나의 필터를 지정할 수 있지만 필터 자체에는 여러 필드, 여러 조건이 포함될 수 있으며 **ismatch** 함수를 사용하는 경우 여러 전체 텍스트 검색 식을 사용할 수 있습니다. 다중 파트 필터 표현식에서 조건자는 순서대로 지정할 수 있습니다(연산자 우선 순위 규칙에 따라 다수). 특정한 순서로 조건자를 다시 정렬해도 성능에 별다른 도움은 되지 않습니다.

필터 식의 제한 중 하나는 요청의 최대 크기 제한입니다. 필터를 포함한 전체 요청은 POST의 경우 최대 16MB, GET의 경우 최대 8KB가 될 수 있습니다. 필터 식의 절 수에도 제한이 있습니다. 경험에 따르면 수백 개의 절이 있는 경우 제한에 도달할 위험이 있습니다. 애플리케이션을 설계할 때 필터의 크기를 무제한으로 생성하지 않는 것이 좋습니다.

다음 예제는 여러 API의 정형화된 필터 정의를 나타냅니다.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>사용 패턴 필터링

다음 예제에서는 필터 시나리오에 대한 몇 가지 사용 패턴을 보여 줍니다. 자세한 내용은 [OData 식 구문 > 예제](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)를 참조하세요.

+ 독립 실행형 **$filter**는 쿼리 문자열 없이, 필터 식이 관심 있는 문서를 정규화할 수 있을 때 유용합니다. 쿼리 문자열이 없으면 어휘 또는 언어 분석, 점수 매기기 및 순위 지정 등이 없으며 검색 문자열은 "모든 문서 일치"를 의미하는 별표일 뿐입니다.

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ 쿼리 문자열과 **$filter**의 조합에서 필터가 하위 집합을 만들면 쿼리 문자열이 필터링된 하위 집합에 대해 전체 텍스트 검색에 용어 입력을 제공합니다. 용어(도보 거리 극장)가 추가되면 검색 점수가 결과에 추가되며, 이 경우 용어와 가장 일치하는 문서가 더 높은 순위에 표시됩니다. 쿼리 문자열이 있는 필터를 사용하는 것이 가장 일반적인 사용 패턴입니다.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ "or"로 구분된 복합 쿼리에서는 각각 자체 필터 조건이 있습니다. 예를 들어, '개'는 '비글', '고양이'는 '샤미즈' 등입니다. 결합된 `or` 표현식은 개별적으로 평가되며 응답에서 다시 전송된 각 식과 일치하는 문서의 결합이 있습니다. 이 사용 패턴은 `search.ismatchscoring` 함수를 통해 달성됩니다. 점수가 없는 버전을 사용할 수도 `search.ismatch`있습니다.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  전체 텍스트 검색을 `search.ismatchscoring` 대신 `and` `or`사용하는 필터와 결합할 수도 있지만 이는 검색 요청에서 `search` 및 `$filter` 매개 변수를 사용하는 것과 기능적으로 동일합니다. 예를 들어 다음 두 쿼리는 동일한 결과를 생성합니다.

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

특정 사용 사례에 대한 포괄적인 지침은 다음 문서를 참조하세요.

+ [패싯 필터](search-filters-facets.md)
+ [언어 필터](search-filters-language.md)
+ [보안 조정](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>필터링을 위한 필드 요구 사항

REST API에서 간단한 필드에 대해 *기본적으로* 필터링 가능합니다. 필터링 가능 필드는 인덱스 크기가 늘어나기 때문에 필터에서 실제로 사용하지 않는 필드에 대해서는 `"filterable": false`로 설정합니다. 필드 정의 설정에 대한 자세한 내용은 [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)(인덱스 만들기)를 참조하세요.

.NET SDK에서는 필터링 가능이 기본적으로 *해제*되어 있습니다. 해당 [필드](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) 개체의 [IsFilterable 속성을](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) 로 설정하여 필드를 `true`필터링할 수 있습니다. [IsFilterable 특성을](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute)사용하여 선언적으로 이 작업을 수행할 수도 있습니다. 아래 예제에서 특성은 인덱스 정의에 매핑되는 모델 클래스의 `BaseRate` 속성에 설정됩니다.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>기존 필드를 필터링 가능으로 만들기

기존 필드를 필터링할 수 있도록 수정할 수 없습니다. 대신 새 필드를 추가하거나 인덱스를 다시 작성해야 합니다. 인덱스 다시 작성 또는 필드 다시 채우기에 대한 자세한 내용은 [Azure Cognitive Search 인덱스를 다시 작성하는 방법을](search-howto-reindex.md)참조하세요.

## <a name="text-filter-fundamentals"></a>텍스트 필터 기본 사항

텍스트 필터는 필터에서 제공하는 리터럴 문자열과 문자열 필드를 일치시다. 전체 텍스트 검색과 달리 텍스트 필터에 대한 어휘 분석이나 단어 분석이 없으므로 정확한 일치 에 대해서만 비교합니다. 예를 들어 필드 *f에* "화창한 날"이 `$filter=f eq 'Sunny'` 포함되어 `$filter=f eq 'sunny day'` 있다고 가정하면 일치하지 않지만 일치하지는 않습니다. 

텍스트 문자열은 대/소문자를 구분합니다. 대문자 단어의 낮은 케이스가 없습니다 : `$filter=f eq 'Sunny day'` "맑은 날"을 찾을 수 없습니다.

### <a name="approaches-for-filtering-on-text"></a>텍스트 필터링 방법

| 접근 방식 | 설명 | 사용 시기 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 구분된 문자열 목록과 필드와 일치하는 함수입니다. | 보안 [필터](search-security-trimming-for-azure-search.md) 및 많은 원시 텍스트 값을 문자열 필드와 일치시켜야 하는 필터에 권장됩니다. **search.in** 함수는 속도를 위해 설계되었으며 을 `eq` `or`사용하여 각 문자열과 필드를 명시적으로 비교하는 것보다 훨씬 빠릅니다. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 동일한 필터 식에서 전체 텍스트 검색 작업과 엄격한 부울 필터 작업을 혼합할 수 있게 해주는 함수입니다. | 한 요청에서 여러 검색 필터 조합을 원할 때 **search.ismatch(또는** 점수와 동일한 **search.ismatch득점)를**사용합니다. 크기가 더 큰 문자열 내에서 부분적으로 문자열을 필터링하기 위해 *contains* 필터에 대해 이를 사용할 수도 있습니다. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 필드, 연산자 및 값으로 구성된 사용자 정의 식입니다. | 문자열 필드와 문자열 값 간의 정확한 일치 항목을 찾으려면 이 방법을 사용합니다. |

## <a name="numeric-filter-fundamentals"></a>숫자 필터 기본 사항

숫자 필드는 전체 텍스트 검색의 컨텍스트에서 `searchable`이 아닙니다. 문자열만 전체 텍스트 검색이 가능합니다. 예를 들어, 검색어로 99.99를 입력하면 $99.99로 책정된 항목이 반환되지 않고 문서의 문자열 필드에 숫자 99가 있는 항목이 표시됩니다. 따라서 숫자 데이터가 있는 경우 범위, 패싯, 그룹 등을 포함하여 필터에 사용한다고 가정합니다. 

숫자 필드(가격, 크기, SKU, ID)를 포함하는 문서는 필드가 `retrievable`로 표시되면 검색 결과에서 해당 값을 제공합니다. 여기서 핵심은 전체 텍스트 검색 자체가 숫자 필드 유형에는 적용되지 않는다는 것입니다.

## <a name="next-steps"></a>다음 단계

먼저 포털의 **검색 탐색기**에서 **$filter** 매개 변수를 사용하여 쿼리를 제출합니다. 필터링된 다음 쿼리를 검색 창에 붙여넣으면 [부동산 샘플 인덱스](search-get-started-portal.md)에서 해당 쿼리에 대한 흥미로운 결과를 제공합니다.

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

더 많은 예제를 사용하려면 [OData 필터 식 구문 > 예제](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)를 참조하세요.

## <a name="see-also"></a>참조

+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [문서 나머지 API 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [지원되는 데이터 유형](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
