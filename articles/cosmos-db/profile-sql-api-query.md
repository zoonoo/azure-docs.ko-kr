---
title: SQL 쿼리 성능 & 실행 메트릭 받기
description: Azure Cosmos DB 요청의 SQL 쿼리 실행 메트릭 및 프로필 SQL 쿼리 성능을 검색하는 방법을 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998375"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>.NET SDK를 사용하여 SQL 쿼리 실행 메트릭을 얻고 쿼리 성능을 분석합니다.

이 문서에서는 Azure Cosmos DB에서 SQL 쿼리 성능을 프로파일하는 방법을 설명합니다. 이 프로파일링은 .NET `QueryMetrics` SDK에서 검색하여 수행할 수 있으며 여기에 자세히 설명되어 있습니다. [쿼리메트릭은](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) 백 엔드 쿼리 실행에 대한 정보가 있는 강력한 형식의 개체입니다. 이러한 메트릭은 [쿼리 성능 조정](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) 문서에서 자세히 설명합니다.

## <a name="set-the-feedoptions-parameter"></a>피드옵션 매개변수 설정

[DocumentClient.CreateDocumentQuery의](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) 모든 오버로드는 선택적 [피드옵션](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) 매개 변수를 사용합니다. 이 옵션은 쿼리 실행을 조정하고 매개 변수화할 수 있도록 하는 것입니다. 

Sql 쿼리 실행 메트릭을 수집하려면 [피드옵션에서](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) 채우기 `true` [쿼리메트릭변수를](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) 로 설정해야 합니다. true로 설정하면 `PopulateQueryMetrics` `FeedResponse` 관련 `QueryMetrics`이 포함됩니다. 

## <a name="get-query-metrics-with-asdocumentquery"></a>AsDocumentQuery() 로 쿼리 메트릭 받기
다음 코드 샘플에서는 [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) 메서드를 사용할 때 메트릭을 검색하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

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
## <a name="aggregating-querymetrics"></a>쿼리메트릭 집계

이전 섹션에서는 [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) 메서드에 대한 호출이 여러 번 있었습니다. 각 호출은 `FeedResponse` 다음의 사전이 `QueryMetrics`있는 개체를 반환했습니다. 쿼리의 모든 연속에 대해 하나씩. 다음 예제에서는 LINQ를 `QueryMetrics` 사용하여 이러한 것을 집계하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

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

## <a name="grouping-query-metrics-by-partition-id"></a>파티션 ID별로 쿼리 메트릭 그룹화

파티션 `QueryMetrics` ID별로 그룹화할 수 있습니다. 파티션 ID로 그룹화하면 특정 파티션이 다른 파티션과 비교할 때 성능 문제를 일으키는지 확인할 수 있습니다. 다음 예제에서는 LINQ로 그룹화하는 `QueryMetrics` 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여

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

## <a name="linq-on-documentquery"></a>문서 쿼리에 대한 LINQ

메서드를 사용 `FeedResponse` 하 여 LINQ 쿼리에서 얻을 수도 있습니다. `AsDocumentQuery()`

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

각 쿼리에서 사용하는 요청 단위를 캡처하여 높은 처리량을 소비하는 비용이 많이 드는 쿼리 또는 쿼리를 조사할 수 있습니다. 에서 [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) 속성을 사용하여 요청 요금을 `FeedResponse`받을 수 있습니다. Azure 포털 및 다른 SDK를 사용하여 요청 요금을 받는 방법에 대해 자세히 알아보려면 요청 단위 요금 문서 [찾기를](find-request-unit-charge.md) 참조하세요.

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

## <a name="get-the-query-execution-time"></a>쿼리 실행 시간 얻기

클라이언트 쪽 쿼리를 실행하는 데 필요한 시간을 계산할 때는 코드 베이스의 다른 부분이 아닌 `ExecuteNextAsync` 메서드를 호출하는 시간만 포함해야 합니다. 다음 예제와 같이 쿼리 실행이 걸린 기간을 계산하는 데 도움이 됩니다.

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>검색 쿼리(일반적으로 느리고 비용이 많이 드는)

검색 쿼리는 인덱스에서 제공되지 않은 쿼리를 말하며, 이로 인해 결과 집합을 반환하기 전에 많은 문서가 로드됩니다.

다음은 검색 쿼리의 예입니다.

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

이 쿼리의 필터는 인덱스에서 제공되지 않는 시스템 함수 UPPER를 사용합니다. 큰 컬렉션에 대해 이 쿼리를 실행하면 첫 번째 연속에 대한 다음 쿼리 메트릭이 생성되었습니다.

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

쿼리 메트릭 출력의 다음 값을 참고하십시오.

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

이 쿼리는 60,951개의 문서를 로드했으며 총 399,998,938바이트에 해당합니다. 이 많은 바이트를 로드하면 비용이 높거나 단위 요금이 청구됩니다. 또한 쿼리를 실행하는 데 시간이 오래 걸리며, 이는 총 사용 시간이 속성으로 명확합니다.

```
Total Query Execution Time               :        4,500.34 milliseconds
```

즉, 쿼리를 실행하는 데 4.5초가 걸렸으며 이는 하나의 연속일 뿐입니다.

이 예제 쿼리를 최적화하려면 필터에서 UPPER를 사용하지 마십시오. 대신 문서를 만들거나 업데이트할 `c.description` 때 모든 대문자에 값을 삽입해야 합니다. 그런 다음 쿼리는 다음과 같은 상태가 됩니다. 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

이제 이 쿼리를 인덱스에서 제공할 수 있습니다.

쿼리 성능 조정에 대한 자세한 내용은 [쿼리 성능 조정](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) 문서를 참조하세요.

## <a name="references"></a><a id="References"></a>참조

- [Azure Cosmos DB SQL 사양](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>다음 단계

- [쿼리 성능 조정](sql-api-query-metrics.md)
- [인덱싱 개요](index-overview.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
