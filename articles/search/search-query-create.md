---
title: 쿼리 만들기
titleSuffix: Azure Cognitive Search
description: Cognitive Search에서 쿼리 요청을 생성 하는 방법, 테스트 및 코드에 사용할 도구와 Api, 쿼리 결정이 인덱스 디자인으로 시작 하는 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 4f5cc0d5eefd5969566040e4148ca7358d348736
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951507"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Azure Cognitive Search에서 쿼리 만들기

처음으로 쿼리를 작성 하는 경우이 문서에서는 쿼리를 설정 하는 방법과 방법을 설명 합니다. 또한 쿼리 요청을 소개 하 고 필드 특성과 언어적 분석기에서 쿼리 결과에 영향을 줄 수 있는 방법을 설명 합니다.

## <a name="whats-a-query-request"></a>쿼리 요청은 무엇 인가요?

쿼리는 단일 검색 인덱스의 docs 컬렉션에 대 한 읽기 전용 요청입니다. ' Search ' 매개 변수에 용어, 인용 부호로 묶인 구 및 연산자로 구성 된 쿼리 식을 포함 하도록 지정 합니다.

추가 매개 변수는 쿼리 및 응답에 대 한 추가 정의를 제공 합니다. 예를 들어 ' searchFields '는 쿼리 실행 범위를 특정 필드로 지정 하 고, ' select '는 결과에서 반환 되는 필드를 지정 하며, ' count '는 인덱스에서 찾은 일치 항목 수를 반환 합니다.

다음 예에서는 사용 가능한 매개 변수의 하위 집합을 표시 하 여 쿼리 요청에 대 한 일반적인 개념을 제공 합니다. 쿼리 컴포지션에 대 한 자세한 내용은 [쿼리 유형 및 컴포지션](search-query-overview.md) 및 [문서 검색 (REST)](/rest/api/searchservice/search-documents)을 참조 하세요.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>클라이언트 선택

Azure Portal 또는 Postman과 같은 도구 또는 Api를 사용 하 여 쿼리 클라이언트를 인스턴스화하는 코드를 사용 해야 합니다. 초기 개발 및 개념 증명 테스트를 위해 Azure Portal 또는 REST Api를 권장 합니다.

### <a name="permissions"></a>사용 권한

쿼리 요청을 비롯 한 모든 작업은 [관리 API 키](search-security-api-keys.md)로 작동 하지만 쿼리 요청은 선택적으로 [쿼리 API 키](search-security-api-keys.md#create-query-keys)를 사용할 수 있습니다. 쿼리 API 키를 적극 권장 합니다. 서비스 당 최대 50을 만들고 다른 응용 프로그램에 다른 키를 할당할 수 있습니다.

Azure Portal 도구, 마법사 및 개체에 액세스 하려면 서비스에서 참가자 역할의 멤버 자격이 필요 합니다. 

### <a name="use-azure-portal-to-query-an-index"></a>Azure Portal를 사용 하 여 인덱스 쿼리

[검색 탐색기 (포털)](search-explorer.md) 는 기본 검색 서비스의 인덱스에 대해 쿼리를 실행 하는 Azure Portal의 쿼리 인터페이스입니다. 내부적으로 포털은 [문서 검색](/rest/api/searchservice/search-documents) 요청을 수행 하지만 자동 완성, 제안 또는 문서 조회를 호출할 수 없습니다. 

미리 보기를 포함 하 여 인덱스 및 REST API 버전을 선택할 수 있습니다. 쿼리 문자열은 단순 또는 전체 구문을 사용할 수 있으며 모든 쿼리 매개 변수 (filter, select, searchFields 등)를 지원 합니다. 포털에서 인덱스를 열 때 필드 특성에 쉽게 액세스할 수 있도록 나란히 있는 탭의 인덱스 JSON 정의와 함께 검색 탐색기를 사용할 수 있습니다. 쿼리를 테스트 하는 동안 검색 가능, 정렬 가능, 필터링 가능 및 패싯 가능 필드를 확인 합니다.

### <a name="use-a-rest-client"></a>REST 클라이언트 사용

Postman과 Visual Studio Code (Azure Cognitive Search 용 확장 포함)는 모두 쿼리 클라이언트로 작동할 수 있습니다. 두 가지 도구를 사용 하 여 검색 서비스에 연결 하 고 [검색 문서 (REST)](/rest/api/searchservice/search-documents) 요청을 보낼 수 있습니다. 다양 한 자습서 및 예제에서는 인덱싱을 쿼리 하는 REST 클라이언트를 보여 줍니다. 

이러한 문서 중 하나를 사용 하 여 각 클라이언트에 대해 알아보세요 (둘 다 쿼리에 대 한 지침 포함).

+ [REST 및 Postman을 사용 하 여 검색 인덱스 만들기](search-get-started-rest.md)
+ [Visual Studio Code 및 Azure Cognitive Search 시작](search-get-started-vs-code.md)

각 요청은 독립 실행형 이므로 모든 요청에서 끝점, 인덱스 이름 및 API 버전을 제공 해야 합니다. 다른 속성, 콘텐츠 형식 및 API 키가 요청 헤더에 전달 됩니다. 자세한 내용은 쿼리 요청을 작성 하는 데 도움이 되는 [문서 검색 (REST)](/rest/api/searchservice/search-documents) 을 참조 하세요.

### <a name="use-an-sdk"></a>SDK 사용

Cognitive Search의 경우 Azure Sdk는 일반적으로 사용 가능한 기능을 구현 합니다. 따라서 Sdk를 사용 하 여 인덱스를 쿼리할 수 있습니다. 모든 요소는 검색 문서를 사용 하 여 인덱스를 로드 하 여 쿼리 요청을 작성 하는 등 인덱스와 상호 작용 하는 메서드를 포함 하는 **Searchclient** 를 제공 합니다.

| Azure SDK | 클라이언트 | 예제 |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | 보류 중. |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>쿼리 유형 선택: 단순 | 차지

쿼리가 전체 텍스트 검색 인 경우 쿼리 파서는 검색 용어 및 구로 전달 되는 텍스트를 처리 하는 데 사용 됩니다. Azure Cognitive Search는 두 개의 쿼리 파서를 제공 합니다. 

+ 단순 파서는 [간단한 쿼리 구문을](query-simple-syntax.md)이해 합니다. 자유 형식 텍스트 쿼리에서이 파서가 속도 및 효율성에 대 한 기본값으로 선택 되었습니다. 구문은 용어 및 구 검색에 대 한 일반적인 검색 연산자 (AND, OR, NOT) 및 prefix ( `*` ) 검색을 지원 합니다 (시애틀 및 seaside의 "해상 *"). 일반적인 권장 사항은 간단한 파서를 먼저 시도한 후 응용 프로그램 요구 사항이 더 강력한 쿼리를 위해를 호출 하는 경우 전체 파서로 이동 하는 것입니다.

+ 요청에 추가할 때 사용 되는 [전체 Lucene 쿼리 구문은](query-Lucene-syntax.md#bkmk_syntax) `queryType=full` [Apache Lucene 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)를 기반으로 합니다.

전체 구문 및 단순 구문은 둘 다 동일한 접두사 및 부울 연산을 지 원하는 범위와 중복 되지만 전체 구문은 더 많은 연산자를 제공 합니다. 전체에는 부울 식에 대 한 추가 연산자와 유사 항목 검색, 와일드 카드 검색, 근접 검색, 정규식 등 고급 쿼리에 대 한 추가 연산자가 있습니다.

## <a name="choose-query-methods"></a>쿼리 메서드 선택

검색은 기본적으로 사용자 중심의 연습이 며, 검색 상자에서 용어 또는 구가 수집 되거나 페이지의 click 이벤트에서 수집 됩니다. 다음 표에는 예상 검색 환경과 함께 사용자 입력을 수집할 수 있는 메커니즘이 요약 되어 있습니다.

| 입력 | 환경 |
|-------|---------|
| [검색 방법](/rest/api/searchservice/search-documents) | 사용자는 운영자를 포함 하거나 포함 하지 않고 검색 상자에 용어 또는 구를 입력 하 고 검색을 클릭 하 여 요청을 보냅니다. 검색은 같은 요청에서 필터와 함께 사용할 수 있지만 자동 완성 또는 제안 사항에는 사용할 수 없습니다. |
| [자동 완성 방법](/rest/api/searchservice/autocomplete) | 사용자는 몇 가지 문자를 입력 하 고, 쿼리는 각각의 새 문자를 입력 한 후에 시작 됩니다. 응답은 인덱스에서 완료 된 문자열입니다. 제공 된 문자열이 유효한 경우 사용자는 검색을 클릭 하 여 해당 쿼리를 서비스로 보냅니다. |
| [제안 방법](/rest/api/searchservice/suggestions) | 자동 완성과 마찬가지로 사용자는 몇 가지 문자를 입력 하 고 증분 쿼리를 생성 합니다. 응답은 일반적으로 고유 하거나 설명 필드로 표시 되는 일치 하는 문서의 드롭다운 목록입니다. 선택 항목이 올바르면 사용자가 하나를 클릭 하면 일치 하는 문서가 반환 됩니다. |
| [패싯 탐색](/rest/api/searchservice/search-documents#query-parameters) | 페이지에는 클릭 가능한 탐색 링크나 검색 범위를 좁히는 이동 경로가 표시 됩니다. 패싯 탐색 구조는 초기 쿼리를 기반으로 하 여 동적으로 구성 됩니다. 예를 들어 `search=*` 가능한 모든 범주로 구성 된 패싯 탐색 트리를 채울 수 있습니다. 패싯 탐색 구조는 쿼리 응답에서 만들어지지만 다음 쿼리를 표현 하는 메커니즘 이기도 합니다. n REST API 참조 `facets` 는 문서 검색 작업의 쿼리 매개 변수로 설명 되지만 매개 변수 없이 사용할 수 있습니다 `search` .|
| [Filter 메서드](/rest/api/searchservice/search-documents#query-parameters) | 필터는 패싯에서 결과의 범위를 좁히는 데 사용 됩니다. 페이지 뒤에 필터를 구현할 수도 있습니다. 예를 들어 언어별 필드를 사용 하 여 페이지를 초기화할 수 있습니다. REST API 참조에서 `$filter` 는 문서 검색 작업의 쿼리 매개 변수로 문서화 되지만 매개 변수 없이 사용할 수 있습니다 `search` .|

## <a name="know-your-field-attributes"></a>필드 특성 확인

이전에 [쿼리 유형과 컴퍼지션을](search-query-overview.md)검토 한 경우 쿼리 요청의 매개 변수는 인덱스에서 필드의 특성을 지정 하는 방법에 따라 달라 집니다. 예를 들어 쿼리, 필터 또는 정렬 순서에서 사용 하려면 필드가 *검색* 가능 하 고, 필터링 가능 하 고 *, 정렬* 가능 해야 *합니다.* 마찬가지로 검색 가능으로 표시 된 필드만 결과에 *나타날 수 있습니다* . 요청에서, 및 매개 변수를 지정 하기 시작 하는 동안 `search` `filter` `orderby` 예기치 않은 결과를 방지 하기 위해 이동할 때 특성을 확인 해야 합니다.

[호텔 샘플 인덱스](search-get-started-portal.md)아래의 포털 스크린샷에서 마지막 두 필드 "LastRenovationDate" 및 "등급"만 유일한 절에서 사용할 수 있습니다 `"$orderby"` .

![호텔 샘플에 대 한 인덱스 정의](./media/search-query-overview/hotel-sample-index-definition.png "호텔 샘플에 대 한 인덱스 정의")

필드 특성에 대 한 설명은 [Create Index (REST API)](/rest/api/searchservice/create-index)를 참조 하세요.

## <a name="know-your-tokens"></a>토큰 파악

인덱싱 중에 검색 엔진은 분석기를 사용 하 여 문자열에 대 한 텍스트 분석을 수행 하 고 쿼리 시간에 일치 시킬 수 있는 가능성을 최대화 합니다. 최소한의 경우 문자열의 대/소문자는 동일 하지만, 분류 정리 및 word 제거를 중지 하기도 합니다. 일반적으로 큰 문자열이 나 복합 단어는 공백, 하이픈 또는 대시로 구분 되며 별도의 토큰으로 인덱싱됩니다. 

여기서는 인덱스에 포함 된 것으로 생각 하 고 실제로는 무엇이 든 다를 수 있습니다. 쿼리에서 예상 된 결과가 반환 되지 않는 경우 [분석 텍스트 (REST API)](/rest/api/searchservice/test-analyzer)를 통해 분석기에서 만든 토큰을 검사할 수 있습니다. 토큰화 및 쿼리에 미치는 영향에 대 한 자세한 내용은 [부분 용어 검색 및 특수 문자가 포함 된 패턴](search-query-partial-matching.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 쿼리 요청의 작동 방식을 더 잘 이해 했으므로 실습 환경에 대해 다음 빠른 시작을 시도 합니다.

+ [검색 탐색기](search-explorer.md)
+ [REST에서 쿼리를 수행하는 방법](search-get-started-rest.md)
+ [.NET에서 쿼리를 수행하는 방법](search-get-started-dotnet.md)
+ [Python에서 쿼리 하는 방법](search-get-started-python.md)
+ [JavaScript에서 쿼리 하는 방법](search-get-started-javascript.md)