---
title: Azure Search의 쿼리 기본 사항 | Microsoft Docs
description: Azure Search에서 검색 쿼리 작성, 매개 변수를 사용하여 결과 필터링, 선택 및 정렬에 대한 기본 사항입니다.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366449"
---
# <a name="query-fundamentals-in-azure-search"></a>Azure Search의 쿼리 기본 사항

Azure Search의 쿼리 정의는 서비스 URL 끝점, 대상 인덱스, 요청을 인증하는 데 사용되는 api-key, api-version 및 쿼리 문자열을 포함하여 요청에서 지정되는 전체 내용입니다. 

쿼리 문자열 구성은 [Search Documents API](https://docs.microsoft.com/rest/api/searchservice/search-documents)에 정의된 매개 변수로 이루어집니다. 가장 중요한 항목은 정의되지 않을 수 있지만(`search=*`) 다음 예제와 비슷한 용어, 구 및 연산자로 구성되는 경우가 많은 **search=** 매개 변수입니다.

```
"search": "seattle townhouse +\"lake\""
```

다른 매개 변수는 쿼리 처리를 지시하거나 응답을 향상시키는 데 사용됩니다. 매개 변수가 사용되는 방법의 예로 특정 필드로 검색 범위 지정, 검색 모드를 설정하여 정밀도-재현율 편차 변조, 결과를 추적할 수 있게 개수 추가 등이 포함됩니다. 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

쿼리 정의는 필수적이지만 인덱스 스키마도 필드별로 허용되는 작업을 지정하는 방식에서 동일하게 중요합니다. 인덱스 개발 동안 필드의 특성에 따라 허용되는 작업이 결정됩니다. 예를 들어, 전체 텍스트 검색 및 검색 결과에 포함을 둘 다 지정하려면 필드를 *검색 가능* 및 *조회 가능*으로 표시해야 합니다.

쿼리 시 항상 실행은 요청에 제공된 api-key를 사용하여 인증되는 하나의 인덱스를 대상으로 합니다. 인덱스를 조인할 수도 없고 사용자 지정 또는 임시 데이터 구조를 쿼리 대상으로 만들 수도 없습니다.  

.NET API를 사용하면 serialization이 기본으로 제공되지만 쿼리 결과는 REST API에서 JSON 문서로 스트리밍됩니다. 쿼리에 대해 매개 변수를 설정하고 결과에 대해 특정 필드를 선택하여 결과를 구체화할 수 있습니다.

요약하자면, 쿼리 요청은 본질적으로 범위 및 작업, 즉 검색에 포함할 필드, 결과 집합에 포함할 필드, 정렬할지 또는 필터링할지 등을 지정합니다. 지정하지 않을 경우, 쿼리는 검색 가능한 모든 필드에 대해 전체 텍스트 검색 작업으로 실행되고, 점수가 매겨지지 않은 결과 집합을 임의의 순서로 반환합니다.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>쿼리 유형: 검색 및 필터링

Azure Search는 매우 강력한 쿼리를 만드는 다양한 옵션을 제공합니다. 사용할 두 가지 기본 유형 쿼리는 `search` 및 `filter`입니다. 

+ `search` 쿼리는 인덱스의 *검색 가능한* 모든 필드에서 하나 이상의 단어를 검색하고 Google 또는 Bing과 같은 검색 엔진이 작동하리라고 예상되는 방법으로 작동합니다. 소개에 포함된 예제에서는 `search` 매개 변수가 사용됩니다.

+ `filter` 쿼리는 인덱스의 *필터링 가능한* 모든 필드에 걸쳐 부울 식을 계산합니다. `search`와 달리 `filter` 쿼리는 문자열 필드에서 대/소문자 구분을 포함하여 필드의 정확한 내용을 검색합니다.

검색 및 필터를 함께 또는 별도로 사용할 수 있습니다. 쿼리 문자열이 없는 독립 실행형 필터는 필터 식이 관심 있는 문서를 정규화할 수 있을 때 유용합니다. 쿼리 문자열이 없으면 어휘 또는 언어 분석, 점수 매기기 및 순위 지정 등이 없으며 검색 문자열은 비어 있습니다.

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

이러한 항목을 함께 사용하는 경우 필터가 전체 인덱스에 먼저 적용된 다음 필터의 결과에 검색이 수행됩니다. 따라서 필터는 검색 쿼리가 처리해야 하는 일련의 문서를 감소시키기 때문에 쿼리 성능을 개선하는 데 유용한 기술일 수 있습니다.

필터 식에 대한 구문은 [OData 필터 언어](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)의 하위 집합입니다. 검색 쿼리의 경우 [간단한 구문](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) 또는 [Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)을 사용할 수 있으며 아래에서 설명합니다.


## <a name="choose-a-syntax-simple-or-full"></a>구문 선택: 단순 또는 전체

Azure Search는 Apache Lucene을 기반으로 하고 일반적인 쿼리와 특수화된 쿼리를 처리하기 위한 두 개의 쿼리 파서 중에서 선택할 수 있도록 합니다. 일반적인 검색 요청은 기본 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)을 사용하여 공식화됩니다. 이 구문은 AND, OR, NOT, 구, 접미사 및 우선 순위 연산자를 포함한 여러 일반 검색 연산자를 지원합니다.

요청에 **queryType=full**을 추가하면 사용되도록 설정되는 [Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax)은 [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)의 일부로 개발된 널리 채택되고 표현적인 쿼리 언어를 공개합니다. 이 쿼리 구문을 사용하여 특수화된 쿼리를 사용할 수 있습니다.

+ [필드 범위 쿼리](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [유사 항목 검색](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [근접 검색](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [용어 상승](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [정규식 검색](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [와일드카드 검색](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

부울 연산자는 두 구문에서 거의 동일하며, 전체 Lucene에서 추가 형식을 갖습니다.

+ [단순 구문의 부울 연산자](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [전체 Lucene 구문의 부울 연산자](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>필수 및 선택적 요소

Azure Search에 검색 요청을 제출할 때 응용 프로그램의 검색 상자에 입력하는 실제 단어와 함께 지정할 수 있는 매개 변수는 여러 가지가 있습니다. 이러한 쿼리 매개 변수를 사용하여 [전체 텍스트 검색 환경](search-lucene-query-architecture.md)을 보다 자세히 제어할 수 있습니다.

쿼리 요청의 필요한 요소에는 다음 구성 요소가 포함됩니다.

+ 서비스 끝점 및 인덱스 문서 컬렉션(여기에서 URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`로 표시)
+ API 버전(REST 전용)(**api-version=** 으로 표시)
+ 쿼리 또는 관리 api-key(**api-key=** 로 표시)
+ 쿼리 문자열(**search=**)로 표시. 빈 검색을 수행 하려는 경우 지정하지 않을 수 있습니다. 필터 식만은 **$filter=** 로 전송할 수 있습니다.
+ **queryType=**(단순 또는 전체). 기본 단순 구문을 사용하려는 경우 생략할 수 있습니다.

모든 다른 검색 매개 변수는 선택 사항입니다.

## <a name="apis-and-tools-for-testing"></a>테스트를 위한 API 및 도구

다음 표에는 쿼리를 제출하기 위한 API 및 도구 기반 접근 방법이 나와 있습니다.

| 방법 | 설명 |
|-------------|-------------|
| [SearchIndexClient(.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Azure Search 인덱스를 쿼리하는 데 사용할 수 있는 클라이언트입니다.  <br/>[자세한 정보](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Search Documents(REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | 인덱스에 대한 GET 또는 POST 메서드로, 추가 입력을 위해 쿼리 매개 변수를 사용합니다.  |
| [Fiddler, Postman 또는 기타 HTTP 테스트 도구](search-fiddler.md) | Azure Search로 쿼리를 전송하기 위해 요청 헤더 및 본문을 설정하는 방법을 설명합니다.  |
| [Azure Portal의 검색 탐색기](search-explorer.md) | 검색 표시줄 및 인덱스와 api-version 선택을 위한 옵션을 제공합니다. 결과는 JSON 문서로 반환됩니다. <br/>[자세한 정보](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>검색 결과 관리

다음과 같은 방법으로 쿼리에 매개 변수를 사용하여 결과 집합을 구성할 수 있습니다.

+ 결과의 문서 수 제한 또는 일괄 처리(기본적으로 50개)
+ 결과에 포함할 필드 선택
+ 정렬 순서 설정
+ 적중 강조 표시를 추가하여 검색 결과 본문에는 일치하는 용어 표시

### <a name="tips-for-unexpected-results"></a>예기치 않은 결과 대한 팁

경우에 따라, 결과의 구조가 아닌 본질을 예상할 수 없습니다. 쿼리 결과가 예상과 다른 경우 다음과 같이 쿼리를 수정하여 결과가 개선되는지 확인할 수 있습니다.

+ `searchMode=any`(기본값)를 `searchMode=all`로 변경하여 조건 중 하나가 아닌 모든 조건에 일치하도록 합니다. 부울 연산자가 쿼리에 포함되는 경우 특히 이렇게 합니다.

+ 텍스트 또는 어휘 분석이 필요한데, 쿼리 형식이 언어 처리를 금지하는 경우 쿼리 방법을 변경합니다. 전체 텍스트 검색에서 텍스트 또는 어휘 분석은 맞춤법 오류, 단어의 단수-복수형 및 불규칙 동사 또는 명사를 자동으로 수정합니다. 유사 항목 또는 와일드카드 검색과 같은 일부 쿼리의 경우 텍스트 분석이 쿼리 구문 분석 파이프라인에 속하지 않습니다. 일부 시나리오에서는 정규식이 해결 방법으로 사용되고 있습니다. 

### <a name="paging-results"></a>페이징 결과
Azure Search를 사용하면 검색 결과의 페이징을 구현하기가 쉽습니다. `top` 및 `skip` 매개 변수를 사용하면 적절한 검색 UI 사례를 쉽게 사용할 수 있도록 모든 검색 결과 집합을 관리 가능하며 정렬된 하위 집합으로 수신할 수 있는 검색 요청을 원활하게 실행할 수 있습니다. 이러한 작은 하위 집합 결과를 받을 때 총 검색 결과 집합에서 문서 수도 수신할 수 있습니다.

[Azure Search에서 검색 결과를 페이징하는 방법](search-pagination-page-layout.md)문서에서 페이징 검색 결과에 대해 자세히 알아볼 수 있습니다.

### <a name="ordering-results"></a>결과 정렬
검색 쿼리에 대한 결과를 받을 때 Azure Search에서 특정 필드의 값을 기준으로 결과를 제공하도록 요청할 수 있습니다. 기본적으로 Azure Search는 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)에서 파생되는 각 문서의 검색 점수 순위에 따라 검색 결과를 정렬합니다.

Azure Search에서 검색 점수 이외의 값으로 결과를 정렬하여 반환하려면 `orderby` 검색 매개 변수를 사용할 수 있습니다. 지리 공간 값에 대한 필드 이름 및 [`geo.distance()` 함수](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)에 대한 호출을 포함하도록 `orderby` 매개 변수 값을 지정할 수 있습니다. 각 식 뒤에는 결과가 오름차순으로 요청됨을 나타내는 `asc`와 결과가 내림차순으로 요청되는 `desc`가 나타날 수 있습니다. 기본 순위는 오름차순입니다.


### <a name="hit-highlighting"></a>적중 항목 강조 표시
Azure Search에서는 `highlight`, `highlightPreTag` 및 `highlightPostTag` 매개 변수를 사용하여 검색 쿼리와 일치하는 검색 결과의 정확한 부분을 쉽게 강조할 수 있습니다. 어떤 *검색 가능한* 필드의 일치 텍스트를 강조할지를 지정할 수 있으며 Azure Search에서 반환하는 일치 텍스트의 시작 및 끝부분에 추가할 정확한 문자열 태그를 지정할 수 있습니다.

## <a name="see-also"></a>참고 항목

+ [Azure Search의 전체 텍스트 검색 작동 방식(쿼리 구문 분석 아키텍처)](search-lucene-query-architecture.md)
+ [검색 탐색기](search-explorer.md)
+ [.NET에서 쿼리를 수행하는 방법](search-query-dotnet.md)
+ [REST에서 쿼리를 수행하는 방법](search-query-rest-api.md)
