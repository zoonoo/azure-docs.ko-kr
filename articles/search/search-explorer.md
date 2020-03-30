---
title: Azure 포털의 검색 탐색기 쿼리 도구
titleSuffix: Azure Cognitive Search
description: 이 Azure 포털 빠른 시작에서 검색 탐색기를 사용하여 쿼리 구문을 학습하거나 쿼리 식을 테스트하거나 검색 문서를 검사합니다. 탐색기 쿼리 Azure 인지 검색에서 인덱스를 검색합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369699"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>빠른 시작: 검색 탐색기를 사용하여 포털에서 쿼리를 실행합니다.

**검색 탐색기는** Azure Cognitive Search의 검색 인덱스에 대해 쿼리를 실행하는 데 사용되는 기본 제공 쿼리 도구입니다. 이 도구를 사용하면 쿼리 구문을 쉽게 학습하거나 쿼리 또는 필터 식을 테스트하거나 최신 콘텐츠가 있는지 확인하여 인덱스 새로 고침 결과를 확인할 수 있습니다.

이 빠른 시작은 검색 탐색기를 보여 주기 위해 **부동산-us-샘플-인덱스를** 사용합니다. 요청은 [검색 REST API를](https://docs.microsoft.com/rest/api/searchservice/)사용하여 공식화되며 응답은 JSON 문서로 반환됩니다.

## <a name="prerequisites"></a>사전 요구 사항

+ [Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다.

+ **부동산 - 우리 - 샘플 인덱스는이** 빠른 시작에 사용됩니다. [**데이터 가져오기**](search-import-data-portal.md) 마법사를 단계별로 기본 제공 샘플 데이터 원본에서 인덱스를 생성합니다.

## <a name="start-search-explorer"></a>검색 탐색기 시작

1. Azure [포털에서](https://portal.azure.com)대시보드에서 검색 서비스 페이지를 열거나 [서비스를 찾습니다.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

1. 명령 모음에서 검색 탐색기를 엽니다.

   ![포털에서 탐색기 명령 검색](./media/search-explorer/search-explorer-cmd2.png "포털에서 탐색기 명령 검색")

    또는 열린 인덱스에 포함된 **검색 탐색기** 탭을 사용합니다.

   ![탐색기 탭 검색](./media/search-explorer/search-explorer-tab.png "탐색기 탭 검색")

## <a name="unspecified-query"></a>지정되지 않은 쿼리

콘텐츠를 먼저 보려면 제공된 용어없이 **검색을** 클릭하여 빈 검색을 실행합니다. 빈 검색은 전체 문서를 반환하여 문서 컴퍼지션을 검토할 수 있도록 하므로 첫 번째 쿼리로서 유용합니다. 빈 검색에서는 검색 순위가 없으며 문서가 임의 순서로 반환됩니다(모든 문서의 경우 `"@search.score": 1`). 기본적으로 하나의 검색 요청에서 50개의 문서가 반환됩니다.

빈 검색에 해당하는 구문은 `*` 또는 `search=*`입니다.
   
   ```http
   search=*
   ```

   **결과**
   
   ![빈 쿼리 예제](./media/search-explorer/search-explorer-example-empty.png "정규화되지 않았거나 비어 있는 쿼리 예제")

## <a name="free-text-search"></a>무료 텍스트 검색

운영자가 있거나 없는 자유 형식 쿼리는 사용자 지정 앱에서 Azure Cognitive Search로 전송된 사용자 정의 쿼리를 시뮬레이션하는 데 유용합니다. 인덱스 정의에서 **검색 가능으로** 인트된 필드만 일치 항목으로 검색됩니다. 

검색 어용어 나 표현식과 같은 검색 기준을 제공하면 검색 순위가 재생됩니다. 다음 예제에서는 자유 텍스트 검색을 보여 줍니다.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **결과**

   특정 관심 용어의 경우 Ctrl+F를 사용하여 결과 내에서 검색을 수행할 수 있습니다.

   ![자유 텍스트 쿼리 예제](./media/search-explorer/search-explorer-example-freetext.png "자유 텍스트 쿼리 예제")

## <a name="count-of-matching-documents"></a>일치하는 문서 수 

**$count=true를** 추가하여 인덱스에 있는 일치 항목 수를 가져옵니다. 빈 검색에서 count는 인덱스의 총 문서 수입니다. 정규화된 검색에서는 쿼리 입력과 일치하는 문서의 수입니다.

   ```http
   $count=true
   ```

   **결과**

   ![문서 수 예제](./media/search-explorer/search-explorer-example-count.png "인덱스에서 일치하는 문서 수")

## <a name="limit-fields-in-search-results"></a>검색 결과의 필드 제한

$select [**추가하여**](search-query-odata-select.md) **Search 탐색기에서**더 읽기 쉬운 출력을 위해 명시적으로 명명된 필드에 결과를 제한합니다. 검색 문자열 및 **$count=true**를 유지하려면 인수에 **&** 접두사를 적용합니다. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **결과**

   ![필드 제한 예제](./media/search-explorer/search-explorer-example-selectfield.png "검색 결과의 필드 제한")

## <a name="return-next-batch-of-results"></a>결과의 다음 일괄 처리 반환

Azure Cognitive Search는 검색 순위에 따라 상위 50개 일치 항목을 반환합니다. 일치하는 문서의 다음 세트를 가져오려면 **$top=100,&$skip=50**을 추가하여 결과 세트를 100개 문서로 늘리고(기본값: 50개, 최대값: 1,000개) 처음 50개 문서를 건너뜁니다. 순위가 지정된 결과를 얻으려면 쿼리 용어 또는 식과 같은 검색 조건을 제공해야 합니다. 검색 결과에 더 깊게 도달할수록 검색 점수가 줄어듭니다.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **결과**

   ![일괄 처리 검색 결과](./media/search-explorer/search-explorer-example-topskip.png "검색 결과의 다음 일괄 처리 반환")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>필터 식(보다 큼, 보다 작음, 같음)

자유 텍스트 검색 대신 정확한 기준을 지정하려는 경우 [**$filter**](search-query-odata-filter.md) 매개 변수를 사용합니다. 필드는 인덱스에서 필터링 **가능한** 것으로 어트리뷰트해야 합니다. 이 예제에서는 3보다 큰 침실을 검색합니다. 

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **결과**

   ![필터 표현식](./media/search-explorer/search-explorer-example-filter.png "기준별 필터")

## <a name="order-by-expressions"></a>Order-by 식

[**$orderby**](search-query-odata-orderby.md) 추가하여 검색 점수 외에 다른 필드별로 결과를 정렬합니다. 필드는 인덱스에서 정렬 **가능으로** 특성이 있어야 합니다. 이를 테스트하는 데 사용할 수 있는 예식은 다음과 같은 것입니다.

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **결과**

   ![순서 표현식](./media/search-explorer/search-explorer-example-ordery.png "정렬 순서 변경")

**$filter** 및 **$orderby** 식 둘 다 OData 생성입니다. 자세한 내용은 [OData 필터 구문](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)을 참조하세요.

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>핵심 내용

이 빠른 시작에서는 **검색 탐색기를** 사용하여 REST API를 사용하여 인덱스를 쿼리했습니다.

+ 결과는 자세한 JSON 문서로 반환되므로 문서 생성 및 콘텐츠를 완전히 볼 수 있습니다. 예제에 표시된 쿼리 식을 사용하여 반환되는 필드를 제한할 수 있습니다.

+ 문서는 인덱스에서 **검색 가능**으로 표시되는 모든 필드로 구성됩니다. 포털에서 인덱스 특성을 보려면 검색 개요 페이지의 *인덱스* 목록에서 **realestate-us-sample**을 클릭합니다.

+ 상용 웹 브라우저에서 입력할 수 있는 것과 비슷한 자유 형식 쿼리는 최종 사용자 환경을 테스트하는 데 유용합니다. 예를 들어, 기본 제공 부동산 샘플 인덱스를 가정할 경우 "Seattle apartments lake washington"을 입력한 후 Ctrl+F를 사용하여 검색 결과 내에서 용어를 찾을 수 있습니다. 

+ 쿼리 및 필터 표현식은 Azure Cognitive Search에서 지원하는 구문으로 표시됩니다. 기본 구문은 [간단한 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)이지만 필요에 따라 보다 강력한 쿼리를 위해 [전체 Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)을 사용할 수 있습니다. [필터 식](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)은 OData 구문입니다.

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

쿼리 구조 및 구문에 대해 자세히 알아보려면 Postman 또는 이에 상응하는 도구를 사용하여 API의 더 많은 부분을 활용하는 쿼리 식을 만듭니다. [검색 REST API는](https://docs.microsoft.com/rest/api/searchservice/) 학습 및 탐색에 특히 유용합니다.

> [!div class="nextstepaction"]
> [Postman에서 기본 쿼리 만들기](search-query-simple-examples.md)