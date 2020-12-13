---
title: 기본 쿼리 만들기
titleSuffix: Azure Cognitive Search
description: Cognitive Search에서 쿼리 요청을 생성 하는 방법, 테스트 및 코드에 사용할 도구와 Api, 쿼리 결정이 인덱스 디자인으로 시작 하는 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371176"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Azure Cognitive Search에서 기본 쿼리 만들기

이 문서에서는 쿼리 생성을 단계별로 설명 합니다. 예제는 REST에 있으므로 포털의 **검색 탐색기** 에 문자열을 복사 하거나 Postman 또는 Visual Studio code를 사용 하 여 대화형으로 쿼리를 작성할 수 있습니다. 이 문서의 예제에서는 모든 계층 또는 버전의 Cognitive Search 사용할 수 있습니다.

## <a name="choose-a-tool-or-api"></a>도구 또는 API 선택

테스트 또는 프로덕션 워크 로드에 대 한 쿼리를 만들려면 다음 도구 및 Api를 선택 합니다.

| 방법 | 설명 |
|-------------|-------------|
| 포털| [검색 탐색기 (포털)](search-explorer.md) 에는 인덱스 및 api-version 선택 항목에 대 한 검색 표시줄과 옵션이 제공 됩니다. 결과는 JSON 문서로 반환됩니다. 초기 조사, 테스트 및 유효성 검사에 권장 됩니다. <br/>[자세한 정보](search-explorer.md) |
| 웹 테스트 도구| [Postman 또는 Visual Studio Code](search-get-started-rest.md) 는 [검색 문서](/rest/api/searchservice/search-documents) REST 호출을 공식화 하기 위한 강력한 선택 항목입니다. REST API는 Azure Cognitive Search의 모든 프로그래밍 방식 작업을 지원 하므로 코드에 투자 하기 전에 대화형으로 요청을 실행 하 여 작동 방식을 이해할 수 있습니다.  |
| Azure SDK | [Searchclient (.net)](/dotnet/api/azure.search.documents.searchclient) 를 사용 하 여 c #에서 검색 인덱스를 쿼리할 수 있습니다.  [자세한 정보](search-howto-dotnet-sdk.md) <br/><br/>[Searchclient (python)](/dotnet/api/azure.search.documents.searchclient) 를 사용 하 여 python에서 검색 인덱스를 쿼리할 수 있습니다. [자세한 정보](search-get-started-python.md) <br/><br/> [Searchclient (javascript)](/dotnet/api/azure.search.documents.searchclient) 를 사용 하 여 javascript에서 검색 인덱스를 쿼리할 수 있습니다. [자세한 정보](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>검색 클라이언트 설정

검색 클라이언트는 검색 서비스를 인증 하 고, 요청을 보내고, 응답을 처리 합니다. 쿼리는 항상 단일 인덱스의 documents 컬렉션으로 전달 됩니다. 인덱스를 조인할 수도 없고 사용자 지정 또는 임시 데이터 구조를 쿼리 대상으로 만들 수도 없습니다.

### <a name="in-the-portal"></a>포털에서

검색 탐색기 및 기타 포털 도구에는 포털 페이지의 직접 액세스 인덱스 및 기타 개체를 사용 하 여 서비스에 대 한 기본 제공 클라이언트 연결이 있습니다. 도구, 마법사 및 개체에 대 한 액세스에는 서비스에 대 한 관리 권한이 있다고 가정 합니다. 검색 탐색기를 사용 하 여 검색 문자열과 기타 매개 변수를 지정 하는 데 집중할 수 있습니다. 

### <a name="using-rest"></a>REST 사용

REST 호출의 경우 [Postman 또는 유사한 도구](search-get-started-rest.md) 를 클라이언트와 함께 사용 하 여 [문서 검색](/rest/api/searchservice/search-documents) 요청을 지정할 수 있습니다. 각 요청은 독립적 이므로 끝점 (서비스에 대 한 URL)과 액세스를 위한 관리자 또는 쿼리 API 키를 제공 해야 합니다. 요청에 따라 URL에는 인덱스 이름, 문서 컬렉션 및 기타 속성이 포함 될 수도 있습니다. 콘텐츠 형식 및 API 키와 같은 몇 가지 속성은 요청 헤더에 전달 됩니다. 다른 매개 변수는 URL 또는 요청의 본문에서 전달 될 수 있습니다. 모든 REST 호출에는 인증을 위한 API 키와 api 버전이 필요 합니다.

### <a name="using-azure-sdks"></a>Azure Sdk 사용

Azure Sdk는 상태를 유지할 수 있는 검색 클라이언트를 제공 하 여 연결을 다시 사용할 수 있도록 합니다. 쿼리 작업의 경우 SearchClient를 인스턴스화하고 끝점, 키, 인덱스의 속성에 대 한 값을 제공 합니다. 그런 다음 검색 메서드를 호출 하 여 쿼리 문자열을 제공할 수 있습니다. 

| 언어 | 클라이언트 | 예제 |
|----------|--------|---------|
| C # 및 .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [첫 번째 검색 쿼리를 C로 보냅니다. #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Python에서 첫 번째 검색 쿼리 보내기](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Java에서 첫 번째 검색 쿼리 보내기](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [JavaScript에서 첫 번째 검색 쿼리 보내기](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>파서 선택: 단순 | 전체

Azure Cognitive Search는 일반적인 쿼리 및 특수 쿼리를 처리 하기 위해 두 개의 쿼리 파서 사이에서 선택할 수 있습니다. 단순 파서를 사용 하는 요청은 일반적으로 [단순 쿼리 구문을](query-simple-syntax.md)사용 하 여 작성 된 전체 텍스트 검색 쿼리로, 자유 형식 텍스트 쿼리에서 속도 및 효율성을 위해 기본값으로 선택 됩니다. 이 구문은 AND, OR, NOT, 구, 접미사 및 우선 순위 연산자를 포함한 여러 일반 검색 연산자를 지원합니다.

요청에 `queryType=full`을 추가하면 사용되도록 설정되는 [전체 Lucene 쿼리 구문](query-Lucene-syntax.md#bkmk_syntax)은 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)의 일부로 개발된 널리 채택되고 표현적인 쿼리 언어를 공개합니다. 전체 구문은 단순 구문을 확장합니다. 단순 구문에 대해 작성한 쿼리는 전체 Lucene 파서에서 실행됩니다. 

다음 예에서는 동일한 쿼리를 보여 주지만 다른 설정을 사용 하 **`queryType`** 는 경우 다른 결과를 생성 합니다. 첫 번째 쿼리에서 `^3` after는 `historic` 검색 용어의 일부로 처리 됩니다. 이 쿼리에 대해 가장 순위가 높은 결과는 "Plaza & 도구 모음" 이며, 해당 설명에는 *바다* 가 있습니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

전체 Lucene 파서를 사용 하는 동일한 쿼리는 `^3` 필드 내 용어 부스터로 해석 됩니다. 파서를 전환 하면 순위가 변경 되 고 맨 *위로 이동 하* 는 용어를 포함 하는 결과가 변경 됩니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>인덱스에서 쿼리 동작 사용

인덱스 디자인 및 쿼리 디자인은 Azure Cognitive Search와 긴밀 하 게 연관 되어 있습니다. 각 필드에 대 한 특성이 있는 *인덱스 스키마* 는 작성할 수 있는 쿼리의 종류를 결정 합니다.

필드의 인덱스 특성은 허용된 작업(예: 인덱스에서 필드를 검색할 수 있는지(*searchable*), 결과에서 검색이 가능한지(*retrievable*), 정렬이 가능한지(*sortable*), 필터링이 가능한지(*filterable*) 등)을 설정합니다. 예제 쿼리에서는 `"$orderby": "Rating desc"` 등급 필드가 인덱스 스키마에서 *정렬할* 수 있는 것으로 표시 되어 있기 때문에만 작동 합니다.

![호텔 샘플에 대 한 인덱스 정의](./media/search-query-overview/hotel-sample-index-definition.png "호텔 샘플에 대 한 인덱스 정의")

위의 스크린샷은 [호텔 샘플 인덱스](search-get-started-portal.md)의 인덱스 특성에 대 한 일부 목록입니다. 포털에서 전체 인덱스 스키마를 만들고 볼 수 있습니다. 인덱스 특성에 대 한 자세한 내용은 [Create index (REST API)](/rest/api/searchservice/create-index)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 요청이 생성 되는 방식을 이해 했으므로 단순 및 전체 구문을 사용 하 여 예제를 사용해 보세요.

+ [단순 쿼리 예제](search-query-simple-examples.md)
+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)