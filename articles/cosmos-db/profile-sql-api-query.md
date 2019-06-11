---
title: SQL 쿼리 성능 및 실행 메트릭 가져오기
description: SQL 쿼리 실행 메트릭 및 Azure Cosmos DB 요청의 SQL 쿼리 성능 프로필을 검색 하는 방법에 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481566"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>.NET SDK를 사용 하 여 쿼리 성능을 분석 및 SQL 쿼리 실행 메트릭을 가져옵니다.

이 문서에서는 Azure Cosmos DB에서 SQL 쿼리 성능 프로 파일링 하는 방법을 제공 합니다. 이 프로 파일링을 수행할 수 있습니다 사용 하 여 `QueryMetrics` .NET SDK에서 검색 하 고 여기 자세히 설명 되어 있습니다. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) 는 백 엔드 쿼리 실행에 대 한 정보를 사용 하 여 강력한 형식의 개체입니다. 이러한 메트릭은에서 자세히 설명 되어는 [쿼리 성능 튜닝](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) 문서.

## <a name="set-the-feedoptions-parameter"></a>FeedOptions 매개 변수를 설정 합니다.

에 대 한 모든 오버 로드 [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) 선택적인 걸릴 [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) 매개 변수입니다. 이 옵션은 쿼리 실행을 조정 하 고 매개 변수가 있습니다. 

Sql 쿼리 실행 메트릭 수집, 매개 변수를 설정 해야 합니다 [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) 에 [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) 에 `true`입니다. 설정 `PopulateQueryMetrics` 를 true 되도록 되도록 합니다 `FeedResponse` 관련 될 `QueryMetrics`합니다. 

## <a name="get-query-metrics-with-asdocumentquery"></a>AsDocumentQuery() 사용 하 여 쿼리 메트릭 가져오기
다음 코드 샘플을 사용 하는 경우 메트릭 검색 수행 하는 방법을 보여 줍니다 [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) 메서드:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>QueryMetrics 집계

이전 섹션에서 여러 번 호출 되었는지 확인할 수 있습니다. [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) 메서드. 각 호출에서 반환 되는 `FeedResponse` 의 사전에 있는 개체 `QueryMetrics`즉 쿼리의 모든 연속에 대 한 합니다. 다음 예제에서는이 집계 하는 방법을 보여 줍니다 `QueryMetrics` LINQ를 사용 하 여:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>파티션 ID 별로 그룹화 쿼리 메트릭

그룹화 할 수 있습니다는 `QueryMetrics` 에서 파티션 id입니다. 파티션 ID 별로 그룹화를 사용 하면 특정 파티션을 다른에 비해 성능 문제를 일으키는지를 확인할 수 있습니다. 다음 예제에서는 그룹화 하는 방법을 보여 줍니다 `QueryMetrics` LINQ를 사용 하 여:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>DocumentQuery의 LINQ

가져올 수도 있습니다는 `FeedResponse` 사용 하 여 LINQ 쿼리는 `AsDocumentQuery()` 메서드:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>비용이 높은 쿼리

비용이 많이 드는 쿼리 또는 높은 처리량을 사용 하는 쿼리를 조사 하려면 각 쿼리에 사용 된 요청 단위를 캡처할 수 있습니다. 요청 요금을 사용 하 여 가져올 수 있습니다 합니다 [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) 속성에서 `FeedResponse`합니다. Azure portal 및 다양 한 Sdk를 사용 하 여 요청 요금을 가져오는 방법에 대 한 자세한 내용은 참조 하세요 [요청 단위 요금을 찾을](find-request-unit-charge.md) 문서.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>쿼리 실행 시간 가져오기

클라이언트 쪽 쿼리를 실행 하는 데 필요한 시간을 계산할 때 호출에 포함 되었는지 확인 합니다 `ExecuteNextAsync` 메서드 및 코드 베이스의 다른 부분에 없습니다. 이 호출에서 쿼리 실행에 걸린 다음 예와에서 같이 계산 하는 데 도움이:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>(일반적으로 느리고 비용이 많이 드는) 쿼리를 검색 합니다.

검색 쿼리 되지 않은에서 제공 하는으로 인해 인덱스를 여러 쿼리를 참조 문서 결과 집합을 반환 하기 전에 로드 됩니다.

다음은 검색 쿼리의 예가입니다.

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

이 쿼리의 필터 위 인덱스에서 제공 되지 않습니다는 시스템 함수를 사용 합니다. 첫 번째 연속 작업에 대 한 다음 쿼리 메트릭을 생성 큰 컬렉션에 대해이 쿼리를 실행 합니다.

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

메트릭 쿼리 출력에서 다음 값을 note:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

이 쿼리 399,998,938 바이트 합산 60,951 문서를 로드 합니다. 높은 비용 또는 요청 단위 요금 발생이 많은 바이트를 로드 합니다. 하는 데 걸린 총 시간 속성의 선택을 취소 하는 쿼리를 실행 하는 데 시간이 오래 걸립니다.

```
Total Query Execution Time               :        4,500.34 milliseconds
```

즉 쿼리에서 실행할 수 초가 4.5 (및이 하나의 연속)입니다.

이 예제에서는 쿼리를 최적화 하려면 위 필터에 사용 하지 않도록 합니다. 대신 경우 문서 만들어지거나 업데이트는 `c.description` 모든 대문자에 값을 삽입 해야 합니다. 쿼리가 됩니다. 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

이 쿼리는 이제 인덱스에서 제공 될 수 있습니다.

쿼리 성능을 튜닝 하는 방법에 대 한 자세한 내용은 참조는 [쿼리 성능 튜닝](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) 문서.

## <a id="References"></a>참조

- [Azure Cosmos DB SQL 사양](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>다음 단계

- [쿼리 성능 조정](sql-api-query-metrics.md)
- [인덱싱 개요](index-overview.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
