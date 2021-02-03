---
title: 인덱서 만들기
titleSuffix: Azure Cognitive Search
description: 인덱서의 속성을 설정 하 여 데이터 원본 및 대상을 확인 합니다. 매개 변수를 설정 하 여 런타임 동작을 수정할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 5fc47599d09e5be60311dbda15868d87de4d91d2
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509387"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱서 만들기

검색 인덱서는 외부 데이터 원본에서 검색 서비스의 검색 인덱스로 문서와 콘텐츠를 전송 하기 위한 자동화 된 워크플로를 제공 합니다. 원래 설계 된 대로, Azure 데이터 소스에서 텍스트 및 메타 데이터를 추출 하 고, 문서를 JSON으로 serialize 하 고, 결과 문서를 검색 엔진에 전달 하 여 인덱싱 합니다. 이는 심층 콘텐츠 처리를 위한 [AI 보강](cognitive-search-concept-intro.md) 을 지원 하도록 확장 되었습니다. 

인덱서를 사용 하면 작성 해야 하는 코드의 양과 복잡성이 크게 줄어듭니다. 이 문서에서는 소스 관련 인덱서 및 [기술력과](cognitive-search-working-with-skillsets.md)를 사용 하 여 고급 작업을 위한 준비로 인덱서를 만드는 방법에 중점을 둔 문서입니다.

## <a name="whats-an-indexer-definition"></a>인덱서 정의는 무엇 인가요?

인덱서는 원본 필드의 영숫자 콘텐츠를 인덱스 필드로 끌어오는 텍스트 기반 인덱싱 또는 구조에 대 한 구분 되지 않은 텍스트를 분석 하는 AI 기반 처리 또는 텍스트와 정보의 이미지를 분석 하 여 해당 내용을 인덱스에 추가 하는 데 사용 됩니다. 다음 인덱스 정의는 시나리오 중 하나에 대해 만들 수 있는 일반적인 항목입니다.

### <a name="indexers-for-text-content"></a>텍스트 콘텐츠의 인덱서

인덱서의 원래 용도는 데이터 원본의 필드에서 텍스트 및 숫자 콘텐츠를 연결 하 고 읽을 수 있는 메커니즘을 제공 하 여 인덱스를 로드 하는 복잡 한 프로세스를 간소화 하는 것 이었습니다. 이는 여전히 기본 사용 사례 이며,이 작업의 경우 다음 예제에 정의 된 속성을 사용 하 여 인덱서를 만들어야 합니다.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

**`name`**, **`dataSourceName`** 및 속성은 **`targetIndexName`** 필수 항목이 며 인덱서를 실행 하려면 먼저 데이터 원본 및 인덱스가 모두 서비스에 있어야 합니다. 

**`parameters`** 속성은 전체 작업에 실패 하기 전까지 허용 되는 오류 수와 같은 런타임 동작을 수정 합니다. 매개 변수는 소스 관련 동작을 지정 하는 방법 이기도 합니다. 예를 들어 소스가 Blob storage 인 경우 파일 확장명을 기준으로 필터링 하는 매개 변수를 설정할 수 있습니다 `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

**`field mappings`** 속성은 이름이 나 형식이 다른 필드의 경우 원본-대상 필드를 명시적으로 매핑하는 데 사용 됩니다. 다른 속성 (표시 되지 않음)은 일정을 [지정](search-howto-schedule-indexers.md)하거나, 사용 하지 않도록 설정 된 상태에서 인덱서를 만들거나, 미사용 데이터의 보충 암호화를 위한 [암호화 키](search-security-manage-encryption-keys.md) 를 지정 하는 데 사용 됩니다.

### <a name="indexers-for-ai-indexing"></a>AI 인덱싱의 인덱서

인덱서는 검색 서비스에서 아웃 바운드 요청을 수행 하는 메커니즘 이므로 AI 강화을 지원 하도록 인덱서가 확장 되어이 사용 사례를 구현 하는 인프라 및 개체를 추가 합니다.

위의 모든 속성 및 매개 변수는 AI 보강를 수행 하는 인덱서에 적용 됩니다. 다음 속성은 AI 보강에만 해당 됩니다. **`skillSets`** , **`outputFieldMappings`** , **`cache`** (미리 보기 및 REST 전용) 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

AI 보강는이 문서의 범위를 벗어났습니다. 자세한 내용은 [AI 보강](cognitive-search-concept-intro.md), [Azure Cognitive Search 기술력과](cognitive-search-working-with-skillsets.md)및 [기술 만들기 (REST)](/rest/api/searchservice/create-skillset)문서를 시작 하세요.

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>인덱서 클라이언트를 선택 하 고 인덱서를 만듭니다.

원격 검색 서비스에서 인덱서를 만들 준비가 되 면 Azure Portal 또는 Postman과 같은 도구 형식 또는 인덱서 클라이언트를 인스턴스화하는 코드와 같은 검색 클라이언트가 필요 합니다. 초기 개발 및 개념 증명 테스트를 위해 Azure Portal 또는 REST Api를 권장 합니다.

### <a name="permissions"></a>사용 권한

상태 또는 정의에 대 한 GET 요청을 포함 하 여 인덱서와 관련 된 모든 작업에는 요청에 대 한 [관리 api 키](search-security-api-keys.md) 가 필요 합니다.

### <a name="limits"></a>제한

모든 [서비스 계층](search-limits-quotas-capacity.md#indexer-limits) 은 만들 수 있는 개체의 수를 제한 합니다. 무료 계층을 사용 하는 경우 각 유형과 2 분의 인덱서 처리 (기술 처리 포함 안 함)의 3 개 개체만 있을 수 있습니다.

### <a name="use-azure-portal-to-create-an-indexer"></a>Azure Portal를 사용 하 여 인덱서 만들기

포털은 인덱서 정의를 지정 하기 위한 필드를 제공 하는 인덱서: [**데이터 가져오기 마법사**](search-import-data-portal.md) 및 **새 인덱서** 를 만드는 두 가지 옵션을 제공 합니다. 마법사는 모든 필수 요소를 만들기 때문에 고유 합니다. 다른 방법으로는 데이터 원본 및 인덱스를 미리 정의 해야 합니다.

다음 스크린샷은 포털에서 이러한 기능을 찾을 수 있는 위치를 보여 줍니다. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="호텔 인덱서" border="true":::

### <a name="use-a-rest-client"></a>REST 클라이언트 사용

Postman과 Visual Studio Code (Azure Cognitive Search 용 확장 포함)는 인덱서 클라이언트로 작동할 수 있습니다. 이러한 도구 중 하나를 사용 하 여 검색 서비스에 연결 하 고 [Create 인덱서 (REST)](/rest/api/searchservice/create-indexer) 요청을 보낼 수 있습니다. 개체를 만들기 위한 REST 클라이언트를 보여 주는 다양 한 자습서와 예제가 있습니다. 

각 클라이언트에 대해 알아보려면 다음 문서 중 하나를 시작 합니다.

+ [REST 및 Postman을 사용 하 여 검색 인덱스 만들기](search-get-started-rest.md)
+ [Visual Studio Code 및 Azure Cognitive Search 시작](search-get-started-vs-code.md)

인덱서 요청을 작성 하는 데 도움이 필요한 경우 [인덱서 작업 (REST)](/rest/api/searchservice/Indexer-operations) 을 참조 하세요.

### <a name="use-an-sdk"></a>SDK 사용

Cognitive Search의 경우 Azure Sdk는 일반적으로 사용 가능한 기능을 구현 합니다. 따라서 모든 Sdk를 사용 하 여 인덱서 관련 개체를 만들 수 있습니다. 이러한 모든 항목은 기술력과를 포함 하 여 인덱서 및 관련 개체를 만드는 메서드를 포함 하는 **Searchindexerclient** 를 제공 합니다.

| Azure SDK | 클라이언트 | 예 |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [인덱서](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>인덱서 실행

인덱서는 서비스에서 인덱서를 만들 때 자동으로 실행 됩니다. 이는 데이터 원본 연결 오류, 필드 매핑 문제 또는 기술 문제가 있는지 확인 하는 순간입니다. 

인덱서를 실행 하는 방법에는 여러 가지가 있습니다.

+ [Create 인덱서](/rest/api/searchservice/create-indexer) 또는 [Update 인덱서에](/rest/api/searchservice/update-indexer) 대 한 HTTP 요청을 보내 정의를 추가 또는 변경 하 고 인덱서를 실행 합니다.

+ 정의를 변경 하지 않고 인덱서를 실행 하는 [인덱서 실행](/rest/api/searchservice/run-indexer) 에 대 한 HTTP 요청을 보냅니다.

+ 생성, 업데이트 또는 실행을 위해 SearchIndexerClient 메서드를 호출 하는 프로그램을 실행 합니다.

> [!NOTE]
> 생성 시 인덱서를 즉시 실행 하지 않도록 하려면 **`disabled=true`** 인덱서 정의에을 포함 합니다.

또는 정기적인 간격으로 처리를 호출 하기 위해 인덱서를 [일정에](search-howto-schedule-indexers.md) 배치 합니다. 

예약 된 처리는 일반적으로 변경 된 콘텐츠의 증분 인덱싱이 필요 합니다. 변경 검색 논리는 원본 플랫폼에 기본 제공 되는 기능입니다. Blob 컨테이너의 변경 내용은 인덱서에 의해 자동으로 검색 됩니다. 다른 데이터 원본에서 변경 내용 검색을 활용 하는 방법에 대 한 지침은 특정 데이터 원본에 대 한 인덱서 문서를 참조 하세요.

+ [Azure SQL 데이터베이스](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>데이터 파악

인덱서는 테이블 형식 행 집합을 필요로 하는데, 여기서 각 행은 인덱스에서 전체 또는 부분 검색 문서가 됩니다. 행과 결과 검색 문서 사이에 일 대 일 관계가 있는 경우가 많으므로 행 집합의 모든 필드가 각 문서를 완전히 채웁니다. 그러나 인덱서를 사용 하 여 문서의 일부만 생성할 수 있습니다. 예를 들어 여러 인덱서 또는 방법을 사용 하 여 인덱스를 작성할 수 있습니다. 

관계형 데이터를 행 집합으로 평면화 하려면 SQL 뷰를 만들거나 동일한 행에 부모 및 자식 레코드를 반환 하는 쿼리를 작성 해야 합니다. 예를 들어 기본 제공 호텔 샘플 데이터 집합은 관련 테이블의 대화방 레코드에 연결 된 50 레코드 (각 호텔 마다 하나씩)가 있는 SQL 데이터베이스입니다. 행 집합으로 데이터를 평면화 하는 쿼리는 각 호텔 레코드의 JSON 문서에 모든 대화방 정보를 포함 합니다. 포함 된 대화방 정보는 **FOR JSON AUTO** 절을 사용 하는 쿼리에 의해 생성 된입니다. 이 기술에 대 한 자세한 내용은 [포함 된 JSON을 반환 하는 쿼리 정의](index-sql-relational-data.md#define-a-query-that-returns-embedded-json)에서 확인할 수 있습니다. 이는 한 가지 예입니다. 동일한 효과를 얻을 수 있는 다른 방법을 찾을 수 있습니다.

플랫 데이터 외에도 검색 가능한 데이터만 가져오는 것이 중요 합니다. 검색 가능한 데이터는 영숫자입니다. Cognitive Search는 이미지 파일에 대 한 텍스트 설명을 추출 하 고 유추할 수 있지만 ( [AI 보강](cognitive-search-concept-intro.md)참조) 검색 가능한 콘텐츠를 만들 수 있습니다. 마찬가지로 AI 보강을 사용 하 여 자연어 모델에서 큰 텍스트를 분석 하 여 구조 또는 관련 정보를 찾아 검색 문서에 추가할 수 있는 새 콘텐츠를 생성할 수 있습니다.

인덱서는 데이터 문제를 수정 하지 않을 경우 다른 형태의 데이터 정리 또는 조작이 필요할 수 있습니다. 자세한 내용은 [Azure 데이터베이스 제품](/azure/?product=databases)의 제품 설명서를 참조 해야 합니다.

## <a name="know-your-index"></a>인덱스 파악

인덱서는 인덱싱에 대 한 검색 문서를 검색 엔진에 전달 합니다. 인덱서는 실행 동작을 결정 하는 속성을가지고 있는 것 처럼 인덱스 스키마에는 문자열의 인덱싱 방법에 영향을 이보다 하는 속성이 있습니다 (문자열만 분석 및 토큰화 됨). 분석기 할당에 따라 인덱싱된 문자열이 전달 된 문자열과 다를 수 있습니다. [분석 텍스트 (REST)](/rest/api/searchservice/test-analyzer)를 사용 하 여 분석기의 효과를 평가할 수 있습니다. 분석기에 대 한 자세한 내용은 [텍스트 처리 분석기](search-analyzers.md)를 참조 하세요.

인덱서는 인덱스와 상호 작용 하는 방식에 따라 필드 이름과 형식만 검사 합니다. 인덱스의 해당 검색 필드에 대해 들어오는 내용이 올바른지 확인 하는 유효성 검사 단계가 없습니다. 확인 단계로 전체 문서 또는 선택한 필드를 반환 하는 채워진 인덱스에 대해 쿼리를 실행할 수 있습니다. 인덱스의 내용을 쿼리 하는 방법에 대 한 자세한 내용은 [기본 쿼리 만들기](search-query-create.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

+ [인덱서 일정](search-howto-schedule-indexers.md)
+ [필드 매핑 정의](search-indexer-field-mappings.md)
+ [인덱서 상태 모니터링](search-howto-monitor-indexers.md)
+ [관리 ID를 사용하여 연결](search-howto-managed-identities-data-sources.md)