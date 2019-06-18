---
title: 검색 결과 작업 방법 - Azure Search
description: 검색 결과를 구조화 및 정렬하고 문서 수를 가져오고 Azure Search의 검색 결과에 콘텐츠 탐색을 추가합니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 73f0dc98d7d2c3e7aa77f6414cbd58e58599eae7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068821"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Azure Search에서 검색 결과 작업 방법
이 문서에서는 총 개수, 문서 검색, 정렬 순서 및 탐색과 같은 검색 결과 페이지의 표준 요소를 구현하는 방법에 대한 지침을 제공합니다. 데이터 또는 정보를 검색 결과에 적용하는 페이지 관련 옵션은 Azure Search Service에 전송된 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 요청을 통해 지정됩니다. 

REST API의 요청에는 GET 명령, 경로 및 서비스에 필요한 것과 응답을 작성하는 방법을 서비스에 알려주는 쿼리 매개 변수가 포함됩니다. .NET SDK에서 해당하는 API는 [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)입니다.

몇 가지 코드 샘플에는 웹 프런트 엔드 인터페이스가 포함되어 있으며 다음에서 확인할 수 있습니다. [New York City 작업 데모 앱](https://azjobsdemo.azurewebsites.net/) 및 [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)

> [!NOTE]
> 유효한 요청에는 서비스 URL 및 경로, HTTP 동사, `api-version` 등과 같은 요소의 숫자가 포함됩니다. 요약하자면, 페이지 매김에 관련된 구문만 강조하기 위해 예제를 잘라냈습니다. 요청 구문에 대 한 자세한 내용은 참조 하세요. [Azure Search 서비스 REST](https://docs.microsoft.com/rest/api/searchservice)합니다.
>

## <a name="total-hits-and-page-counts"></a>총 적중 수 및 페이지 수

쿼리에서 반환된 결과의 총 수를 표시한 후 해당 결과를 더 작은 청크로 반환하는 것은 모든 검색 페이지의 기반이 됩니다.

![][1]

Azure Search에서는 `$count`, `$top` 및 `$skip` 매개 변수를 사용하여 이러한 값을 반환합니다. 합계에 대 한 샘플 요청 "온라인 카탈로그" 이라는 인덱스에 도달 하는 다음 예제와 반환 `@odata.count`:

    GET /indexes/online-catalog/docs?$count=true

첫 페이지에서 시작하여 15개 그룹에 대한 문서를 검색하고, 총 적중 수도 표시합니다.

    GET /indexes/online-catalog/docs?search=*$top=15&$skip=0&$count=true

페이지 매김 결과에는 `$top`이(가) 일괄적으로 반환할 항목의 수를 지정하고 `$skip`은(는) 건너뛸 항목 수를 지정하는 `$top` 및 `$skip`이(가) 모두 필요합니다. 다음 예제에서는 각 페이지에서 `$skip` 매개 변수로 증분 점프하여 증가하는 다음 15개 항목을 보여줍니다.

    GET /indexes/online-catalog/docs?search=*$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>레이아웃

검색 결과 페이지에서 축소판 이미지, 필드의 하위 집합 및 전체 제품 페이지에 대한 링크를 표시할 수 있습니다.

 ![][2]

Azure Search를 사용 하 여 `$select` 및 [Search API 요청](https://docs.microsoft.com/rest/api/searchservice/search-documents) 이 환경을 구현 합니다.

타일화된 레이아웃에 대한 필드의 하위 집합을 반환하는 방법:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

이미지 및 미디어 파일은 직접 검색할 수 없으며, 비용을 줄이기위해 Azure Blob Storage와 같은 다른 스토리지 플랫폼에 저장해야 합니다. 인덱스 및 문서에서 외부 콘텐츠의 URL 주소를 저장하는 필드를 정의합니다. 그러면 필드를 이미지 참조로 사용할 수 있습니다. 이미지에 대한 URL은 문서에 있어야 합니다.

**onClick** 이벤트에 대한 제품 설명 페이지를 검색하려면 [문서 조회](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) 를 사용하여 검색할 문서의 키에 전달합니다. 키의 데이터 형식은 `Edm.String`입니다. 이 예제에서는 *246810*입니다.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>관련성, 등급, 또는 가격 기준으로 정렬

정렬 순서는 종종 기본적으로 관련도에 따르지만, 고객이 다른 순위 순서로 기존 결과를 신속하게 바꿀 수 있도록 대체 정렬 순서를 준비해 놓는 것이 일반적입니다.

 ![][3]

Azure Search에서 정렬은 `"Sortable": true.`로 인덱싱되는 모든 필드에 대해 `$orderby` 식을 기반으로 합니다. `$orderby` 절은 OData 식입니다. 구문에 대한 자세한 내용은 [필터 및 order-by 절의 OData 식 구문](query-odata-filter-orderby-syntax.md)을 참조하세요.

관련성은 프로필 점수 매기기와 강력하게 연관됩니다. 검색 단어가 더 많이 또는 더 강력하게 일치되는 문서에 더 높은 점수를 매기는 텍스트 분석 및 통계에 따라 모든 결과의 순서를 정하는 기본 점수를 사용할 수 있습니다.

대체 정렬 순서는 일반적으로 정렬 순서를 작성하는 메서드를 다시 호출하는 **onClick** 이벤트에 관련이 있습니다. 예를 들어 이 페이지 요소를 가정합니다.

 ![][4]

선택한 정렬 옵션을 입력으로 받아 메서드를 생성하고 해당 옵션과 관련된 조건에 대한 정렬된 목록을 반환할 수 있습니다.

 ![][5]

> [!NOTE]
> 기본 점수 매기기는 다양한 시나리오에 적용할 수 있으므로 대신 사용자 지정 점수 매기기 프로필의 관련성에 기반하는 것이 좋습니다. 사용자 지정 점수 매기기 프로필은 비즈니스에 더 많은 이점을 제공하는 항목 강화 방법을 제공합니다. 자세한 내용은 [점수 매기기 프로필 추가](index-add-scoring-profiles.md)를 참조하세요.
>

## <a name="faceted-navigation"></a>패싯 탐색

종종 페이지의 옆쪽 또는 위쪽에 있는 검색 탐색은 결과 페이지에서 일반적입니다. Azure Search에서는 미리 정의된 필터에 따라 패싯 탐색이 자기 주도 탐색을 제공합니다. 자세한 내용은 [Azure Search의 패싯 탐색](search-faceted-navigation.md)을 참조하십시오.

## <a name="filters-at-the-page-level"></a>페이지 수준의 필터

솔루션 디자인에서 특정 유형의 콘텐츠 (예를 들어 온라인 소매 응용 프로그램 페이지의 위쪽에 부서 목록이 있는)에 대 한 전용된 검색 페이지를 포함 하는 경우 삽입할 수 있습니다는 [필터 식](search-filters.md) 는와함께**onClick** 이벤트를 미리 필터링 된 상태에서 페이지를 엽니다.

검색 식의 사용 여부에 관계 없이 필터를 보낼 수 있습니다. 예를 들어 다음 요청은 브랜드 이름으로 필터링하고 일치하는 문서만 반환합니다.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

`$filter` 식에 대한 자세한 내용은 [문서 검색(Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)을 참조하십시오.

## <a name="see-also"></a>관련 항목

- [Azure Search 서비스 REST API](https://docs.microsoft.com/rest/api/searchservice)
- [인덱스 작업](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [문서 작업](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Azure Search의 패싯 탐색](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
