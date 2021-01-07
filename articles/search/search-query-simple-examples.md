---
title: Simple Lucene 쿼리 구문 사용
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱스에 대해 전체 텍스트 검색, 필터 검색, 지리적 검색, 패싯 검색에 대 한 간단한 구문을 기반으로 쿼리를 실행 하 여 예제로 알아보세요.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401749"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search에서 "simple" 검색 구문을 사용 합니다.

Azure Cognitive Search에서 [단순 쿼리 구문은](query-simple-syntax.md) 전체 텍스트 검색에 대 한 기본 쿼리 파서를 호출 합니다. 파서는 속도가 빠르며 전체 텍스트 검색, 필터링 된 패싯 검색 및 접두사 검색을 포함 한 일반적인 시나리오를 처리 합니다. 이 문서에서는 예제를 사용 하 여 [REST API (검색 문서)](/rest/api/searchservice/search-documents) 요청의 간단한 구문 사용을 보여 줍니다.

> [!NOTE]
> 대체 쿼리 구문은 [전체 Lucene](query-lucene-syntax.md)이며 유사 항목 및 와일드 카드 검색과 같은 보다 복잡 한 쿼리 구조를 지원 합니다. 자세한 내용 및 예제 [는 Full Lucene 구문 사용](search-query-lucene-examples.md)을 참조 하세요.

## <a name="nyc-jobs-examples"></a>NYC 작업 예제

다음 예에서는 [뉴욕 OpenData 이니셔티브의 도시](https://nycopendata.socrata.com/)에서 제공 하는 데이터 집합을 기반으로 하 여 사용 가능한 작업으로 구성 된 [NYC jobs 검색 인덱스](https://azjobsdemo.azurewebsites.net/) 를 활용 합니다. 이 데이터는 최신 또는 완료로 간주되어서는 안 됩니다. 인덱스는 Microsoft에서 제공 하는 샌드박스 서비스에 있습니다. 즉, 이러한 쿼리를 사용해 볼 수 있는 Azure 구독 또는 Azure Cognitive Search 필요 하지 않습니다.

GET 또는 POST에서 HTTP 요청을 발급 하는 데 필요한 작업은 Postman 또는 동등한 도구입니다. 이러한 도구에 익숙하지 않은 경우 [빠른 시작: Azure Cognitive Search REST API 살펴보기](search-get-started-rest.md)를 참조 하세요.

## <a name="set-up-the-request"></a>요청 설정

1. 요청 헤더의 값은 다음과 같아야 합니다.

   | 키 | 값 |
   |-----|-------|
   | 콘텐츠 형식 | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> NYC Jobs 인덱스를 호스트 하는 샌드박스 검색 서비스에 대 한 실제 쿼리 API 키입니다. |

1. 동사를로 설정 **`GET`** 합니다.

1. URL을로 설정 **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** 합니다. 

   + 인덱스의 documents 컬렉션에는 검색 가능한 모든 콘텐츠가 포함 됩니다. 요청 헤더에 제공 된 쿼리 api 키는 documents 컬렉션을 대상으로 하는 읽기 작업에만 적용 됩니다.

   + **`$count=true`** 검색 조건과 일치 하는 문서 수를 반환 합니다. 빈 검색 문자열에서 개수는 인덱스의 모든 문서 (NYC 작업의 경우 약 2558)가 됩니다.

   + **`search=*`** null 또는 빈 검색에 해당 하는 지정 되지 않은 쿼리입니다. 특히 유용 하지는 않지만 모든 값을 사용 하 여 인덱스에서 검색할 수 있는 모든 필드를 표시 하는 것이 가장 간단 합니다.

1. 확인 단계에서 다음 요청을 GET에 붙여넣고 **보내기** 를 클릭합니다. 결과는 자세한 JSON 문서로 반환됩니다.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>단순 쿼리 구문 분석을 호출하는 방법

대화형 쿼리의 경우 어떤 것도 지정할 필요가 없습니다. simple이 기본값입니다. 코드에서 이전에를 호출한 경우를 사용 하 여 **`queryType=full`** 기본값을 다시 설정할 수 있습니다 **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>예 1: 특정 필드에 대 한 전체 텍스트 검색

이 첫 번째 예제는 파서 특정적이지는 않지만 Azure에서는 이를 사용하여 첫 번째 기본 쿼리 개념인 포함을 소개하려고 합니다. 이 예에서는 쿼리 실행과 응답을 모두 특정 필드에만 제한 합니다. 도구가 Postman 또는 Search 탐색기인 경우 판독 가능한 JSON 응답을 구성하는 방법을 파악하는 것이 중요합니다. 

이 쿼리는  **`searchFields`** 매개 변수를 통해 **`select`** 응답의 동일한 필드를 지정 하 여의 business_title만을 대상으로 합니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Postman 샘플 응답" border="false":::

응답에서 검색 점수를 보았을 수 있습니다. 검색은 전체 텍스트 검색이 아니거나 조건이 제공 되지 않았기 때문에 순위가 없으면 **1** 의 균일 점수가 발생 합니다. 빈 검색의 경우 행이 임의의 순서로 반환 됩니다. 실제 조건을 포함하는 경우 검색 점수가 의미 있는 값으로 바뀌는 것을 볼 수 있습니다.

## <a name="example-2-look-up-by-id"></a>예제 2: ID별 조회

쿼리에서 검색 결과를 반환 하는 경우 논리적 후속 단계는 문서에서 더 많은 필드를 포함 하는 세부 정보 페이지를 제공 하는 것입니다. 이 예에서는 [조회 작업](/rest/api/searchservice/lookup-document) 을 사용 하 여 문서 ID를 전달 하는 단일 문서를 반환 하는 방법을 보여 줍니다.

모든 문서에는 고유 식별자가 있습니다. 조회 쿼리를 위한 구문을 사용하려면 사용할 문서를 찾을 수 있도록 먼저 문서 ID 목록을 반환합니다. NYC Jobs의 경우 식별자는 `id` 필드에 저장됩니다.

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

다음으로 `id` 이전 응답에서 먼저 표시 된 "9E1E3AF9-0660-4E00-AF51-9B654925A2D5"를 기반으로 컬렉션에서 문서를 검색 합니다. 다음 쿼리는 전체 문서에 대 한 모든 검색 가능 필드를 반환 합니다.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>예제 3: 쿼리 필터링

[필터 구문은](./search-query-odata-filter.md) 단독으로 또는에서 사용할 수 있는 OData 식입니다 **`search`** . 검색 매개 변수가 없는 독립 실행형 필터는 필터 식이 관심 있는 문서를 정규화할 수 있을 때 유용합니다. 쿼리 문자열이 없으면 어휘 또는 언어 분석, 점수 매기기(모든 점수는 1) 및 순위 지정 등이 없습니다. 검색 문자열은 비어 있습니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

이러한 항목을 함께 사용하는 경우 필터가 전체 인덱스에 먼저 적용된 다음 필터의 결과에 검색이 수행됩니다. 따라서 필터는 검색 쿼리가 처리해야 하는 일련의 문서를 감소시키기 때문에 쿼리 성능을 개선하는 데 유용한 기술일 수 있습니다.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="쿼리 응답 필터링" border="false":::

필터 및 검색을 결합 하는 또 다른 강력한 방법은 필터 식에서를 사용 하는 것입니다. 여기서 필터는 필터 **`search.ismatch*()`** 내에서 검색 쿼리를 사용할 수 있습니다. 이 필터 식은 plan, planner, planning 등의 용어가 포함된 business_title을 선택하려면 *plan* 에 대한 와일드 카드를 사용합니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

함수에 대한 자세한 내용은 ["필터 예제"의 search.ismatch](./search-query-odata-full-text-search-functions.md#examples)를 참조하세요.

## <a name="example-4-range-filters"></a>예제 4: 범위 필터

범위 필터링은 **`$filter`** 모든 데이터 형식의 식을 통해 지원 됩니다. 다음 예제에서는 숫자 및 문자열 필드를 검색합니다. 

데이터 형식은 범위 필터에서 중요하며, 숫자 데이터가 숫자 필드에 있고 문자열 데이터가 문자열 필드에 있으면 가장 잘 작동합니다. 문자열 필드의 숫자 데이터는 Azure Cognitive Search에서 숫자 문자열을 비교할 수 없기 때문에 범위에 적합 하지 않습니다.

다음 쿼리는 숫자 범위입니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="숫자 범위에 대한 범위 필터" border="false":::

이 쿼리에서 범위는 문자열 필드 (business_title)를 벗어납니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="텍스트 범위에 대한 범위 필터" border="false":::

> [!NOTE]
> 값 범위에 대한 패싯은 일반적인 검색 애플리케이션 요구 사항입니다. 자세한 내용 및 예제는 [패싯 필터를 작성 하는 방법](search-filters-facets.md)을 참조 하세요.

## <a name="example-5-geo-search"></a>예제 5: 지리적 검색

샘플 인덱스에는 위도 및 경도 좌표를 사용한 geo_location 필드가 포함됩니다. 이 예제에서는 시작 지점의 원주 내에서 사용자가 지정하는 임의의 거리(킬로미터 단위)에 문서를 필터링하는 [geo.distance 함수](search-query-odata-geo-spatial-functions.md#examples)를 사용합니다. 쿼리 노출 영역을 줄이거나 확장하려면 쿼리(4)에서 마지막 값을 조정할 수 있습니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

보다 읽기 쉬운 결과를 위해 검색 결과가 잘린 후 작업 제목 및 작업 위치를 포함 합니다. 인덱스(Staten 섬에 작업 위치가 있는 경우)의 임의 문서에서 시작 좌표를 가져왔습니다.

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Staten 섬 맵" border="false":::

## <a name="example-6-search-precision"></a>예제 6: 검색 정확도

용어 쿼리는 단일 용어로, 대부분이 개별적으로 평가될 것입니다. 구 쿼리는 따옴표로 묶인 후 약어 문자열로 평가됩니다. 일치의 정확도는 연산자 및 searchMode로 제어됩니다.

예제 1: `search=fire`  모든 일치 항목은 문서의 어딘가에 발생 하는 단어를 포함 하는 140 결과에 일치 합니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

예제 2: `search=fire department`는 2002개 결과를 반환합니다. fire 또는 department를 포함하는 문서가 일치하는 것으로 반환됩니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

예제 3: `search="fire department"` 77 결과를 반환 합니다. 문자열을 따옴표로 묶으면 두 용어로 구성 된 구 검색을 만들고 일치 하는 용어로 구성 된 인덱스의 토큰화 된 용어에서 일치 항목을 찾을 수 있습니다. 이것은 `search=+fire +department`와 같은 검색이 동일하지 않은 이유를 설명합니다. 두 용어는 필수하지만 별도로 검색됩니다. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> 따옴표를 통해 구 쿼리를 지정 하기 때문에이 예제에서는 `\` 구문을 유지 하기 위해 이스케이프 문자 ()를 추가 합니다.

## <a name="example-7-booleans-with-searchmode"></a>예제 7: searchMode를 사용한 부울

간단한 구문은 문자 형태의 부울 연산자를 지원합니다(`+, -, |`). SearchMode 매개 변수는 전체 자릿수와 회수를 **`searchMode=any`** 찾은 다음 리콜 (조건에 따라 일치 하는 조건에 대해 일치) 및 **`searchMode=all`** 찾은 다음 전체 자릿수 (모든 조건이 일치 해야 함)를 사용 하 여 절충을 알립니다. 

기본값은 이며 **`searchMode=any`** ,이는 여러 연산자를 사용 하 여 쿼리를 누적 하 고 좁은 결과 대신 광범위 하 게 사용 하는 경우 혼동 될 수 있습니다. 결과에 특정 용어를 “포함하지 않는” 모든 문서를 포함하게 되는 NOT을 사용할 경우에는 특히 이러한 현상이 두드러집니다.

기본 searchMode (any)를 사용 하는 경우 "화재 학과" 라는 구가 포함 된 문서와 "Metrotech Center" 구가 없는 모든 문서 2800를 반환 합니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="모든 검색 모드" border="false":::

을로 변경 하면 **`searchMode=all`** 조건에 대 한 누적 효과가 적용 되 고, 전체 구가 "화재 학과" 인 문서로 구성 된 작은 결과 집합-21 개의 문서와 Metrotech 센터 주소에서 해당 작업을 뺀 값이 반환 됩니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="모든 검색 모드" border="false":::

## <a name="example-8-structuring-results"></a>예제 8: 결과 구성

여러 매개 변수가 결과 집합에 포함된 필드, 각 일괄 처리로 반환되는 문서의 수 및 정렬 순서를 제어합니다. 이 예에서는 이전 예제 중 몇 가지를 resurfaces 문과 축 자 검색 조건을 사용 하 여 결과를 특정 필드로 제한 하 고 **`$select`** 82 일치를 반환 합니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

이전 예제에 덧붙여, 제목을 기준으로 정렬할 수 있습니다. civil_service_title이 인덱스에서 *정렬 가능* 하므로 이 정렬을 사용할 수 있습니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

페이징 결과는 매개 변수를 사용 하 여 구현 됩니다 **`$top`** .이 경우 상위 5 개 문서를 반환 합니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

다음 5개 문서를 가져오려면 첫 번째 일괄 처리를 건너뜁니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>다음 단계

코드에서 쿼리를 지정 해 보세요. 다음 링크는 Azure Sdk를 사용 하 여 검색 쿼리를 설정 하는 방법을 설명 합니다.

+ [.NET SDK를 사용 하 여 인덱스 쿼리](search-get-started-dotnet.md)
+ [Python SDK를 사용 하 여 인덱스 쿼리](search-get-started-python.md)
+ [JavaScript SDK를 사용 하 여 인덱스 쿼리](search-get-started-javascript.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](query-simple-syntax.md)
+ [Full Lucene 쿼리 구문](query-lucene-syntax.md)
+ [필터 구문](search-query-odata-filter.md)