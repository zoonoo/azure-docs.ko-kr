---
title: 검색 결과 작업 방법
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 검색 결과에 검색 결과를 구조화 및 정렬하고, 문서 수를 얻고, 콘텐츠 탐색을 추가합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 451e83fa6ab547536a4cfd85304930e749a8247f
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998388"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Azure 인지 검색에서 검색 결과 작업 하는 방법

이 문서에서는 일치하는 문서, 페이지가 있는 결과, 정렬된 결과 및 적중 강조 표시된 용어의 총 수로 돌아오는 쿼리 응답을 얻는 방법을 설명합니다.

응답의 구조는 쿼리의 매개 변수( REST API의 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 또는 .NET SDK의 [DocumentSearchResult 클래스)에](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) 의해 결정됩니다.

## <a name="result-composition"></a>결과 구성

검색 문서는 많은 수의 필드로 구성될 수 있지만 일반적으로 결과 집합의 각 문서를 나타내는 데 필요한 필드는 몇 개뿐입니다. 쿼리 요청에서 응답에 `$select=<field list>` 표시되는 필드를 지정하기 위해 부가합니다. 필드에 포함하려면 인덱스에서 **검색 가능으로** 어트리뷰트해야 합니다. 

가장 적합한 필드에는 문서 간에 대비하고 구별되는 필드가 포함되며, 사용자 쪽에서 클릭을 통한 응답을 초대하기에 충분한 정보를 제공합니다. 전자 상거래 사이트에서는 제품 이름, 설명, 브랜드, 색상, 크기, 가격 및 등급일 수 있습니다. 호텔 샘플 인덱스 기본 제공 샘플의 경우 다음 예제의 필드일 수 있습니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 제품 사진이나 로고와 같은 결과에 이미지 파일을 포함하려면 Azure Cognitive Search 외부에 저장하지만 인덱스에 이미지 URL을 참조하는 필드를 포함합니다. 결과에서 이미지를 지원하는 샘플 인덱스에는 이 [빠른 시작에](search-create-app-portal.md)등장하는 **부동산 샘플-us** 데모와 [뉴욕시 채용 데모 앱이](https://aka.ms/azjobsdemo)포함됩니다.

## <a name="paging-results"></a>페이징 결과

기본적으로 검색 엔진은 쿼리가 전체 텍스트 검색인 경우 검색 점수에 따라 결정된 대로 처음 50개 일치 항목까지 반환하거나 정확한 일치 쿼리에 대해 임의순서로 반환합니다.

다른 수의 일치하는 문서를 반환하려면 `$skip` 쿼리 요청에 및 매개 변수를 추가합니다. `$top` 다음 목록에서는 논리를 설명합니다.

+ 인덱스 `$count=true` 내에서 일치하는 문서의 총 수를 구하려면 추가합니다.

+ 일치하는 문서 15개에 총 일치 항목 수를 반환합니다.`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ 두 번째 세트를 반환하고 처음 15를 건너뛰고 `$top=15&$skip=15`다음 15를 가져옵니다. 15의 세 번째 세트에 대해 동일한 작업을 수행합니다.`$top=15&$skip=30`

페이그(paginated) 쿼리의 결과는 기본 인덱스가 변경되는 경우 안정적이지 않습니다. 페이징은 각 `$skip` 페이지의 값을 변경하지만 각 쿼리는 독립적이며 쿼리 시간에 인덱스에 있는 데이터의 현재 보기에서 작동합니다(즉, 범용 데이터베이스에 있는 것과 같은 결과의 캐싱 또는 스냅숏이 없음).
 
다음은 중복을 얻을 수있는 방법의 예입니다. 다음 4개의 문서가 있는 인덱스를 가정합니다.

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
이제 평가에 따라 정렬된 결과를 한 번에 두 개씩 반환한다고 가정합니다. 이 쿼리를 실행하여 결과의 첫 번째 `$top=2&$skip=0&$orderby=rating desc`페이지를 가져옵니다.

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
서비스에서 쿼리 호출 사이에 다섯 번째 문서가 인덱스에 추가된다고 `{ "id": "5", "rating": 4 }`가정합니다.  그 후 곧 두 번째 페이지를 `$top=2&$skip=2&$orderby=rating desc`가져오고 다음 결과를 가져오는 쿼리를 실행합니다.

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
문서 2가 두 번 가져온다는 것을 알 수 있습니다. 이는 새 문서 5가 등급에 대한 더 큰 값을 가지므로 문서 2 앞에 정렬하고 첫 페이지에 도착하기 때문입니다. 이 동작은 예기치 않은 것일 수 있지만 검색 엔진이 동작하는 방식은 일반적입니다.

## <a name="ordering-results"></a>결과 정렬

전체 텍스트 검색 쿼리의 경우 결과는 문서의 용어 빈도 및 근접성을 기준으로 계산된 검색 점수에 따라 자동으로 순위가 매겨지며, 점수가 높을수록 검색어에서 일치하는 항목이 더 많거나 더 강한 문서가 됩니다. 

검색 점수는 동일한 결과 집합의 다른 문서와 비교하여 일치 강도를 반영하여 일반적인 관련성 감각을 전달합니다. 점수가 한 쿼리에서 다음 쿼리로 항상 일치하지는 않으므로 쿼리를 사용할 때 검색 문서의 순서가 작은 불일치를 알 수 있습니다. 이 문제가 발생할 수 있는 이유에 대한 몇 가지 설명이 있습니다.

| 원인 | 설명 |
|-----------|-------------|
| 데이터 변동성 | 문서를 추가, 수정 또는 삭제할 때 인덱스 콘텐츠가 달라집니다. 기간 빈도는 인덱스 업데이트가 시간이 지남에 따라 처리됨에 따라 변경되어 일치하는 문서의 검색 점수에 영향을 미칩니다. |
| 여러 복제본 | 여러 복제본을 사용하는 서비스의 경우 각 복제본에 대해 쿼리가 병렬로 발급됩니다. 검색 점수를 계산하는 데 사용되는 인덱스 통계는 복제본별로 계산되며 결과는 쿼리 응답에서 병합되고 정렬됩니다. 복제본은 대부분 서로의 미러이지만 상태의 작은 차이로 인해 통계가 다를 수 있습니다. 예를 들어 한 복제본이 다른 복제본에서 병합된 해당 통계에 기여하는 문서를 삭제했을 수 있습니다. 일반적으로 복제본별 통계의 차이는 작은 인덱스에서 더 두드러지다. |
| 동일한 점수 | 여러 문서의 점수가 같은 경우 그 중 하나가 먼저 나타날 수 있습니다.  |

### <a name="consistent-ordering"></a>일관된 순서

결과 순서지정의 유연성을 감안할 때 일관성이 응용 프로그램 요구 사항인 경우 다른 옵션을 탐색할 수 있습니다. 가장 쉬운 방법은 등급이나 날짜와 같은 필드 값별로 정렬하는 것입니다. 등급이나 날짜와 같은 특정 필드별로 정렬하려는 시나리오의 경우 **정렬 가능으로**인덱싱된 모든 필드에 적용할 수 있는 [ `$orderby` 식을](query-odata-filter-orderby-syntax.md)명시적으로 정의할 수 있습니다.

또 다른 옵션은 [사용자 지정 점수 매기기 프로필을](index-add-scoring-profiles.md)사용하는 것입니다. 점수 매기기 프로필을 사용하면 특정 필드에 있는 일치 항목을 높일 수 있으므로 검색 결과의 항목 순위를 보다 세게 제어할 수 있습니다. 추가 점수 매기기 논리는 각 문서에 대한 검색 점수가 더 멀리 떨어져 있기 때문에 복제본 간의 사소한 차이를 재정의하는 데 도움이 될 수 있습니다. 이 접근 방식의 [순위 알고리즘을](index-ranking-similarity.md) 권장합니다.

## <a name="hit-highlighting"></a>적중 항목 강조 표시

히트 하이라이트는 일치하는 용어에 적용된 텍스트 서식(예: 굵게 또는 노란색 강조 표시)을 말하며, 일치되는 용어를 쉽게 찾아볼 수 있습니다. 조회 강조 표시 명령은 [쿼리 요청에](https://docs.microsoft.com/rest/api/searchservice/search-documents)제공됩니다. 검색 엔진은 일치하는 용어를 `highlightPreTag` 태그로 둘러싸고, `highlightPostTag`은 코드는 응답을 처리합니다(예: 굵은 글꼴 적용).

서식은 전체 용어 쿼리에 적용됩니다. 다음 예제에서는 설명 필드에 있는 "모래", "모래", "해변", "해변"이라는 용어가 강조 표시를 위해 태그가 지정됩니다. 퍼지 및 와일드카드 검색과 같이 엔진에서 쿼리 확장을 트리거하는 쿼리는 히트 강조 표시에 대한 지원이 제한되어 있습니다.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

> [!IMPORTANT]
> 2020년 7월 15일 이후에 생성된 서비스는 다른 하이라이트 환경을 제공합니다. 해당 날짜 이전에 생성된 서비스는 강조 표시 동작에서 변경되지 않습니다. 이 변경 을 통해 전체 구 쿼리와 일치하는 구문만 반환됩니다. 또한 강조 표시에 대해 반환되는 조각 크기를 지정할 수 있습니다.
>
> 히트 강조 표시를 구현하는 클라이언트 코드를 작성할 때는 이 변경 사항을 알고 있어야 합니다. 완전히 새로운 검색 서비스를 만들지 않는 한 이 서비스는 영향을 미치지 않습니다.

## <a name="next-steps"></a>다음 단계

클라이언트에 대한 검색 페이지를 빠르게 생성하려면 다음 옵션을 고려하십시오.

+ [응용 프로그램 생성기는](search-create-app-portal.md)포털에서 검색 표시줄, 패싯 탐색 및 이미지가 포함된 결과 영역이 있는 HTML 페이지를 만듭니다.
+ [C#에서 첫 번째 앱을 만드는](tutorial-csharp-create-first-app.md) 것은 기능 클라이언트를 빌드하는 자습서입니다. 샘플 코드는 페이그된 쿼리, 적중 강조 표시 및 정렬을 보여 줍니다.

여러 코드 샘플에는 여기에서 찾을 수 있는 웹 프런트 엔드 인터페이스가 포함되어 있습니다: [뉴욕시 채용 데모 앱,](https://aka.ms/azjobsdemo) [라이브 데모 사이트가 있는 JavaScript 샘플 코드](https://github.com/liamca/azure-search-javascript-samples)및 [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).