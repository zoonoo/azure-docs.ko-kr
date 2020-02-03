---
title: 검색 결과를 사용 하는 방법
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 검색 결과를 구조화 및 정렬 하 고, 문서 수를 가져오고, 검색 결과에 콘텐츠 탐색을 추가 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c32e58a43b5409fd9f8ede536167d185270c6a22
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721577"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Azure Cognitive Search에서 검색 결과를 사용 하는 방법
이 문서에서는 총 개수, 문서 검색, 정렬 순서 및 탐색과 같은 검색 결과 페이지의 표준 요소를 구현하는 방법에 대한 지침을 제공합니다. 검색 결과에 데이터 또는 정보를 제공 하는 페이지 관련 옵션은 Azure Cognitive Search 서비스로 전송 되는 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 요청을 통해 지정 됩니다. 

REST API의 요청에는 GET 명령, 경로 및 서비스에 필요한 것과 응답을 작성하는 방법을 서비스에 알려주는 쿼리 매개 변수가 포함됩니다. .NET SDK에서 해당하는 API는 [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)입니다.

클라이언트에 대 한 검색 페이지를 신속 하 게 생성 하려면 다음 옵션을 탐색 합니다.

+ 포털에서 [응용 프로그램 생성기](search-create-app-portal.md) 를 사용 하 여 검색 표시줄, 패싯 탐색 및 결과 영역을 포함 하는 HTML 페이지를 만듭니다.
+ 자습서 [의 C# 첫 번째 앱 만들기](tutorial-csharp-create-first-app.md) 를 따라 기능 클라이언트를 만듭니다.

몇 가지 코드 샘플에는 [뉴욕 도시 작업 데모 앱](https://azjobsdemo.azurewebsites.net/), [라이브 데모 사이트를 사용 하는 JavaScript 샘플 코드](https://github.com/liamca/azure-search-javascript-samples), [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)에서 찾을 수 있는 웹 프런트 엔드 인터페이스가 포함 되어 있습니다.

> [!NOTE]
> 유효한 요청에는 서비스 URL 및 경로, HTTP 동사, `api-version` 등과 같은 요소의 숫자가 포함됩니다. 요약하자면, 페이지 매김에 관련된 구문만 강조하기 위해 예제를 잘라냈습니다. 요청 구문에 대 한 자세한 내용은 [Azure COGNITIVE SEARCH REST api](https://docs.microsoft.com/rest/api/searchservice)를 참조 하세요.
>

## <a name="total-hits-and-page-counts"></a>총 적중 수 및 페이지 수

쿼리에서 반환된 결과의 총 수를 표시한 후 해당 결과를 더 작은 청크로 반환하는 것은 모든 검색 페이지의 기반이 됩니다.

![][1]

Azure Cognitive Search에서는 `$count`, `$top`및 `$skip` 매개 변수를 사용 하 여 이러한 값을 반환 합니다. 다음 예에서는 `@odata.count`로 반환 되는 "온라인-카탈로그" 라는 인덱스의 총 적중 횟수에 대 한 샘플 요청을 보여 줍니다.

    GET /indexes/online-catalog/docs?$count=true

첫 페이지에서 시작하여 15개 그룹에 대한 문서를 검색하고, 총 적중 수도 표시합니다.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

페이지 매김 결과에는 `$top`이(가) 일괄적으로 반환할 항목의 수를 지정하고 `$skip`은(는) 건너뛸 항목 수를 지정하는 `$top` 및 `$skip`이(가) 모두 필요합니다. 다음 예제에서는 각 페이지에서 `$skip` 매개 변수로 증분 점프하여 증가하는 다음 15개 항목을 보여줍니다.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>레이아웃

검색 결과 페이지에서 축소판 이미지, 필드의 하위 집합 및 전체 제품 페이지에 대한 링크를 표시할 수 있습니다.

 ![][2]

Azure Cognitive Search에서 `$select` 및 [검색 API 요청](https://docs.microsoft.com/rest/api/searchservice/search-documents) 을 사용 하 여이 환경을 구현 합니다.

타일화된 레이아웃에 대한 필드의 하위 집합을 반환하는 방법:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

이미지 및 미디어 파일은 직접 검색할 수 없으며, 비용을 줄이기위해 Azure Blob Storage와 같은 다른 스토리지 플랫폼에 저장해야 합니다. 인덱스 및 문서에서 외부 콘텐츠의 URL 주소를 저장하는 필드를 정의합니다. 그러면 필드를 이미지 참조로 사용할 수 있습니다. 이미지에 대한 URL은 문서에 있어야 합니다.

**onClick** 이벤트에 대한 제품 설명 페이지를 검색하려면 [문서 조회](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) 를 사용하여 검색할 문서의 키에 전달합니다. 키의 데이터 형식은 `Edm.String`입니다. 이 예제에서는 *246810*입니다.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>관련성, 등급, 또는 가격 기준으로 정렬

정렬 순서는 종종 기본적으로 관련도에 따르지만, 고객이 다른 순위 순서로 기존 결과를 신속하게 바꿀 수 있도록 대체 정렬 순서를 준비해 놓는 것이 일반적입니다.

 ![][3]

Azure Cognitive Search에서 정렬은 `$orderby` 식을 기반으로 하며 `"Sortable": true.`로 인덱싱되는 모든 필드에 대해 `$orderby` 절은 OData 식입니다. 구문에 대한 자세한 내용은 [필터 및 order-by 절의 OData 식 구문](query-odata-filter-orderby-syntax.md)을 참조하세요.

관련성은 프로필 점수 매기기와 강력하게 연관됩니다. 검색 단어가 더 많이 또는 더 강력하게 일치되는 문서에 더 높은 점수를 매기는 텍스트 분석 및 통계에 따라 모든 결과의 순서를 정하는 기본 점수를 사용할 수 있습니다.

대체 정렬 순서는 일반적으로 정렬 순서를 작성하는 메서드를 다시 호출하는 **onClick** 이벤트에 관련이 있습니다. 예를 들어 이 페이지 요소를 가정합니다.

 ![][4]

선택한 정렬 옵션을 입력으로 받아 메서드를 생성하고 해당 옵션과 관련된 조건에 대한 정렬된 목록을 반환할 수 있습니다.

 ![][5]

> [!NOTE]
> 기본 점수 매기기는 다양한 시나리오에 적용할 수 있으므로 대신 사용자 지정 점수 매기기 프로필의 관련성에 기반하는 것이 좋습니다. 사용자 지정 점수 매기기 프로필은 비즈니스에 더 많은 이점을 제공하는 항목 강화 방법을 제공합니다. 자세한 내용은 [점수 매기기 프로필 추가](index-add-scoring-profiles.md)를 참조하세요.
>

## <a name="hit-highlighting"></a>적중 항목 강조 표시

검색 결과에서 일치 하는 용어에 서식을 적용 하 여 일치 항목을 쉽게 찾을 수 있습니다. 적중 항목 강조 표시 명령은 [쿼리 요청](https://docs.microsoft.com/rest/api/searchservice/search-documents)에 제공 됩니다. 

서식 지정은 전체 용어 쿼리에 적용 됩니다. 엔진에서 쿼리 확장을 발생 시키는 유사 항목 검색 또는 와일드 카드 검색과 같은 부분 용어에 대 한 쿼리는 적중 항목 강조 표시를 사용할 수 없습니다.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```



## <a name="faceted-navigation"></a>패싯 탐색

종종 페이지의 옆쪽 또는 위쪽에 있는 검색 탐색은 결과 페이지에서 일반적입니다. Azure Cognitive Search에서 패싯 탐색은 미리 정의 된 필터를 기반으로 하는 자체 지향 검색을 제공 합니다. 자세한 내용은 [Azure Cognitive Search의 패싯 탐색](search-faceted-navigation.md) 을 참조 하세요.

## <a name="filters-at-the-page-level"></a>페이지 수준의 필터

솔루션 디자인에 특정 유형의 콘텐츠에 대 한 전용 검색 페이지가 포함 된 경우 (예: 페이지 맨 위에 부서를 포함 하는 온라인 소매점 응용 프로그램), **onClick** 이벤트와 함께 [필터 식을](search-filters.md) 삽입 하 여 미리 필터링 된 상태로 페이지를 열 수 있습니다.

검색 식의 사용 여부에 관계 없이 필터를 보낼 수 있습니다. 예를 들어 다음 요청은 브랜드 이름으로 필터링하고 일치하는 문서만 반환합니다.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

`$filter` 식에 대 한 자세한 내용은 [문서 검색 (Azure COGNITIVE SEARCH API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 을 참조 하세요.

## <a name="see-also"></a>참고 항목

- [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice)
- [인덱스 작업](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [문서 작업](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Azure Cognitive Search의 패싯 탐색](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
