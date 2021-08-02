---
title: 쿼리 만들기
titleSuffix: Azure Cognitive Search
description: Cognitive Search에서 쿼리 요청을 생성하는 방법, 테스트 및 코드에 사용할 도구 및 API, 쿼리 결정을 인덱스 디자인으로 시작하는 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 53428a0185b003e22fd0ad68001b2b1588f994b1
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111750752"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Azure Cognitive Search에서 쿼리 만들기

처음으로 쿼리를 작성하는 경우 이 문서에서는 쿼리를 설정하는 접근 방식과 방법을 설명합니다. 또한 쿼리 요청을 소개하고 필드 특성과 언어적 분석기에서 쿼리 결과에 어떤 영향을 줄 수 있는지를 설명합니다.

## <a name="whats-a-query-request"></a>쿼리 요청이란?

쿼리는 단일 검색 인덱스의 docs 컬렉션에 대한 읽기 전용 요청입니다. 'search' 매개 변수에 용어, 인용 부호로 묶인 구 및 연산자로 구성된 쿼리 식이 포함되도록 지정합니다.

요청의 추가 매개 변수에서는 쿼리 및 응답에 대한 추가 정의를 제공합니다. 예를 들어 'searchFields'는 쿼리 실행 범위를 특정 필드로 지정하고, 'select'는 결과에서 반환되는 필드를 지정하며, 'count'는 인덱스에서 찾은 일치 항목 수를 반환합니다.

다음 예에서는 사용 가능한 일부 매개 변수를 표시하여 쿼리 요청에 대한 일반적인 개념을 제공합니다. 쿼리 컴퍼지션에 대한 자세한 내용은 [쿼리 유형 및 컴퍼지션](search-query-overview.md)과 [문서 검색(REST)](/rest/api/searchservice/search-documents)을 참조하세요.

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

Azure Portal 또는 Postman과 같은 도구 또는 API를 사용하여 쿼리 클라이언트를 인스턴스화하는 코드가 있어야 합니다. 초기 개발 및 개념 증명 테스트에는 Azure Portal 또는 REST API를 사용하는 것이 좋습니다.

### <a name="permissions"></a>사용 권한

쿼리 요청에는 읽기 권한이 필요하며 이 권한은 헤더에 전달된 API 키를 통해 부여됩니다. 쿼리 요청을 비롯한 모든 작업은 [관리 API 키](search-security-api-keys.md)로 작동하지만 쿼리 요청은 선택적으로 [쿼리 API 키](search-security-api-keys.md#create-query-keys)를 사용할 수 있습니다. 쿼리 API 키를 적극 권장합니다. 서비스 당 최대 50개를 만들고 다른 애플리케이션에 다른 키를 할당할 수 있습니다.

Azure Portal에서는 도구, 마법사, 개체에 액세스하려면 서비스에서 참가자 역할 이상의 멤버 자격이 필요합니다. 

### <a name="use-azure-portal-to-query-an-index"></a>Azure Portal을 사용하여 인덱스 쿼리

[검색 탐색기(포털)](search-explorer.md)는 기본 검색 서비스의 인덱스에 대해 쿼리를 실행하는 Azure Portal의 쿼리 인터페이스입니다. 내부적으로 포털은 [문서 검색](/rest/api/searchservice/search-documents) 요청을 수행하지만 자동 완성, 제안 또는 문서 조회를 호출할 수 없습니다. 

미리 보기를 포함하여 인덱스 및 REST API 버전을 선택할 수 있습니다. 쿼리 문자열은 단순 또는 전체 구문을 사용할 수 있으며 모든 쿼리 매개 변수(filter, select, searchFields 등)를 지원합니다. 포털에서 인덱스를 열 때 나란히 있는 탭의 인덱스 JSON 정의와 함께 검색 탐색기를 사용함으로써 필드 특성에 쉽게 액세스할 수 있습니다. 쿼리를 테스트하는 동안 검색 가능, 정렬 가능, 필터링 가능 및 패싯 가능한 필드를 확인합니다.

### <a name="use-a-rest-client"></a>REST 클라이언트 사용

Postman과 Visual Studio Code(Azure Cognitive Search용 확장 사용)는 모두 쿼리 클라이언트로 작동할 수 있습니다. 둘 중 어떤 도구를 사용하든 검색 서비스에 연결하고 [검색 문서(REST)](/rest/api/searchservice/search-documents) 요청을 보낼 수 있습니다. 다양한 자습서 및 예제를 통해 인덱싱을 쿼리하는 REST 클라이언트를 확인할 수 있습니다. 

두 문서 중 하나를 사용하여 각 클라이언트에 대해 알아보세요(두 문서 모두 쿼리에 대한 지침 포함).

+ [REST 및 Postman을 사용하여 검색 인덱스 만들기](search-get-started-rest.md)
+ [Visual Studio Code 및 Azure Cognitive Search 시작](search-get-started-vs-code.md)

각 요청은 독립적으로 실행되므로 모든 요청에서 엔드포인트, 인덱스 이름, API 버전을 제공해야 합니다. 다른 속성, 콘텐츠 형식 및 API 키가 요청 헤더에 전달됩니다. 쿼리 요청을 작성하는 방법에 대한 자세한 내용은 [문서 검색(REST)](/rest/api/searchservice/search-documents)을 참조하세요.

### <a name="use-an-sdk"></a>SDK 사용

Cognitive Search의 경우 Azure SDK는 일반적으로 사용 가능한 기능을 구현합니다. 따라서 SDK를 사용하여 인덱스를 쿼리할 수 있습니다. 모든 쿼리는 검색 문서로 인덱스를 로드하여 쿼리 요청을 작성하는 등 인덱스와 상호 작용하는 메서드를 포함하는 **SearchClient** 를 제공합니다.

| Azure SDK | 클라이언트 | 예제 |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | 보류 중. |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query.py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>쿼리 형식 선택: 기본 | 전체

쿼리가 전체 텍스트 검색인 경우 쿼리 파서는 검색 용어 및 구로 전달되는 텍스트를 처리하는 데 사용됩니다. Azure Cognitive Search는 두 개의 쿼리 파서를 제공합니다. 

+ 단순 파서는 [간단한 쿼리 구문](query-simple-syntax.md)을 이해합니다. 이 파서는 자유 형식 텍스트 쿼리에서 속도와 효율성의 기본값으로 선택됩니다. 구문은 용어 및 구 검색에 대한 일반 검색 연산자(AND, OR, NOT)와 접두사(`*`) 검색(Seattle 및 seaside의 "sea*")을 지원합니다. 일반적인 권장 사항은 간단한 파서를 먼저 시도한 후 애플리케이션 요구 사항에 따라 더 강력한 쿼리가 필요한 경우 전체 파서로 이동하는 것입니다.

+ 요청에 `queryType=full`을 추가할 때 사용되는 [전체 Lucene 쿼리 구문](query-Lucene-syntax.md#bkmk_syntax)은 [Apache Lucene 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)를 기반으로 합니다.

전체 구문과 단순 구문은 둘 다 어느 정도 동일한 범위의 접두사 및 부울 연산을 지원하지만 전체 구문은 더 많은 연산자를 제공합니다. 전체 구문에는 부울 식에 대한 추가연산자와 유사 항목 검색, 와일드카드 검색, 근접 검색, 정규식 등 고급 쿼리를 위한 더 많은 연산자가 있습니다.

## <a name="choose-query-methods"></a>쿼리 메서드 선택

검색은 기본적으로 사용자 중심 활동으로 검색 상자에서 용어나 구가 수집되거나 페이지의 클릭 이벤트에서 수집됩니다. 다음 표에는 예상 검색 환경과 함께 사용자 입력을 수집할 수 있는 메커니즘이 요약되어 있습니다.

| 입력 | 환경 |
|-------|---------|
| [Search 메서드](/rest/api/searchservice/search-documents) | 사용자는 운영자를 포함하거나 포함하지 않고 검색 상자에 용어 또는 구를 입력하고 검색을 클릭하여 요청을 보냅니다. 검색은 동일한 요청에 대해 필터와 함께 사용할 수 있지만 자동 완성이나 제안 사항과는 함께 사용할 수 없습니다. |
| [Autocomplete 메서드](/rest/api/searchservice/autocomplete) | 사용자가 몇 개의 문자를 입력하면 각각의 새 문자를 입력한 후에 쿼리가 시작됩니다. 응답은 인덱스에서 완성된 문자열입니다. 제공된 문자열이 유효한 경우 사용자는 검색을 클릭하여 해당 쿼리를 서비스로 보냅니다. |
| [Suggestions 메서드](/rest/api/searchservice/suggestions) | Autocomplete 메서드와 마찬가지로 사용자가 몇 개의 문자를 입력하면 증분 쿼리가 생성됩니다. 응답은 일반적으로 고유하거나 설명 필드로 표시되는 일치하는 문서의 드롭다운 목록입니다. 선택 영역이 유효한 경우 사용자가 하나를 클릭하면 일치하는 문서가 반환됩니다. |
| [패싯 탐색](/rest/api/searchservice/search-documents#query-parameters) | 페이지에는 클릭 가능한 탐색링크나 검색 범위를 좁히는 이동 경로가 표시됩니다. 패싯 탐색구조는 초기 쿼리를 기반으로 하여 동적으로 구성됩니다. 예를 들어 `search=*`를 사용하여 가능한 모든 범주로 구성된 패싯 탐색 트리를 채울 수 있습니다. 패싯 탐색 구조는 쿼리 응답에서 생성되지만 다음 쿼리를 표현하는 메커니즘이기도 합니다. REST API 참조에서 `facets`은 문서 검색 작업의 쿼리 매개 변수로 설명되지만 `search` 매개 변수 없이 사용할 수 있습니다.|
| [Filter 메서드](/rest/api/searchservice/search-documents#query-parameters) | 필터는 패싯에서 결과의 범위를 좁히는 데 사용됩니다. 페이지 이면의 필터를 구현할 수도 있습니다. 예를 들어 언어별 필드를 사용하면 페이지를 초기화할 수 있습니다. REST API 참조에서 `$filter`은 문서 검색 작업의 쿼리 매개 변수로 설명되지만 `search` 매개 변수 없이 사용할 수 있습니다.|

## <a name="know-your-field-attributes"></a>필드 특성 파악하기

이전에 [쿼리 유형과 컴퍼지션](search-query-overview.md)을 검토한 경우 쿼리 요청의 매개 변수는 인덱스에서 필드의 특성을 지정하는 방법에 따라 달라집니다. 예를 들어 필드를 쿼리, 필터 또는 정렬 순서에서 사용하려면 필드가 *검색 가능* 하고, *필터링 가능* 하고, *정렬 가능* 해야 합니다. 마찬가지로 결과에는 *조회 가능* 으로 표시된 필드만 표시될 수 있습니다. 요청에서 `search`, `filter`, `orderby` 매개 변수를 지정하기 시작하는 동안 예기치 않은 결과를 방지하기 위해 실행할 때 특성을 확인해야 합니다.

[호텔 샘플 인덱스](search-get-started-portal.md) 아래의 포털 스크린샷에서 마지막 두 개의 필드 "LastRenovationDate" 및 "등급"만 `"$orderby"` 전용 절에서 사용할 수 있습니다.

![호텔 샘플에 대한 인덱스 정의](./media/search-query-overview/hotel-sample-index-definition.png "호텔 샘플에 대한 인덱스 정의")

필드 특성에 대한 설명은 [인덱스 생성(REST API)](/rest/api/searchservice/create-index)을 참조하세요.

## <a name="know-your-tokens"></a>토큰 파악하기

인덱싱 중에 검색 엔진은 분석기를 사용하여 문자열에 대한 텍스트 분석을 수행하고 쿼리 시간에 맞출 가능성을 최대화합니다. 최소한 소문자 문자열이지만 분류 정리 및 중지 단어 제거를 수행할 수도 있습니다. 일반적으로 큰 문자열이나 복합 단어는 공백, 하이픈 또는 대시로 구분되며 별도의 토큰으로 인덱싱됩니다. 

여기서 알아두어야 할 요점은 인덱스에 포함될 것으로 생각한 요소와 실제 요소가 다를 수 있다는 점입니다. 쿼리에서 예상된 결과가 반환되지 않는 경우 [텍스트 분석(REST API)](/rest/api/searchservice/test-analyzer)을 통해 분석기에서 생성한 토큰을 검사할 수 있습니다. 토큰화와 쿼리에 미치는 영향에 대한 자세한 내용은 [부분 용어 검색 및 특수 문자가 포함된 패턴](search-query-partial-matching.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 쿼리 요청의 작동 방식에 대한 이해도가 높아졌으므로 실습 환경에 대해 다음 빠른 시작을 시도합니다.

+ [검색 탐색기](search-explorer.md)
+ [REST에서 쿼리를 수행하는 방법](search-get-started-rest.md)
+ [.NET에서 쿼리를 수행하는 방법](search-get-started-dotnet.md)
+ [Python에서 쿼리를 수행하는 방법](search-get-started-python.md)
+ [JavaScript에서 쿼리를 수행하는 방법](search-get-started-javascript.md)