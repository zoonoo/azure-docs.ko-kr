---
title: Azure Cosmos DB에서 RU(요청 단위) 요금 찾기
description: Azure Cosmos 컨테이너에 대해 실행한 작업의 요청 단위 요금을 찾는 방법 알아보기
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2019
ms.author: thweiss
ms.openlocfilehash: e3175ee136057c695ceef3cd1976b447a529c803
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59053043"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Azure Cosmos DB에서 RU(요청 단위) 요금 찾기

이 문서에서는 Azure Cosmos 컨테이너에 대해 실행한 작업의 [요청 단위](request-units.md) 사용량을 찾는 다양한 방법을 제공합니다. 현재 Azure Portal을 사용하거나 SDK 중 하나를 통해 Azure Cosmos DB에서 다시 보낸 응답을 검사하여 이 사용량을 측정할 수 있습니다.

## <a name="core-api"></a>코어 API

### <a name="use-the-azure-portal"></a>Azure Portal 사용

현재 Azure Portal에서는 SQL 쿼리에 대한 요청 요금만 찾을 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos DB 계정을 만들고](create-sql-api-dotnet.md#create-account) 해당 계정을 데이터와 함께 피드하거나 이미 데이터를 포함하는 기존 계정을 선택할 수 있습니다.

1. **Data Explorer** 창을 열고 작업할 컨테이너를 선택합니다.

1. **새 SQL 쿼리**를 클릭합니다.

1. 올바른 쿼리를 입력한 다음, **쿼리 실행**을 클릭합니다.

1. **쿼리 통계**를 클릭하여 방금 실행한 요청에 대한 실제 요청 요금을 표시합니다.

![Azure Portal의 SQL 쿼리 요청 요금 스크린샷](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>.NET SDK V2 사용

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(사용법은 [이 빠른 시작](create-sql-api-dotnet.md) 참조)에서 반환된 개체는 `RequestCharge` 속성을 표시합니다.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>Java SDK 사용

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)(사용법은 [이 빠른 시작](create-sql-api-java.md) 참조)에서 반환된 개체는 `getRequestCharge()` 메서드를 표시합니다.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>Node.js SDK 사용

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)(사용법은 [이 빠른 시작](create-sql-api-nodejs.md) 참조)에서 반환된 개체는 기본 HTTP API에서 반환된 모든 헤더를 매핑하는 `headers` 하위 개체를 표시합니다. 요청 요금은 `x-ms-request-charge` 키 아래에서 사용할 수 있습니다.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>Python SDK 사용

[Python SDK](https://pypi.org/project/azure-cosmos/)(사용법은 [이 빠른 시작](create-sql-api-python.md) 참조)의 `CosmosClient` 개체는 마지막 실행한 작업에 대해 기본 HTTP API에서 반환된 모든 헤더를 매핑하는 `last_response_headers` 사전을 표시합니다. 요청 요금은 `x-ms-request-charge` 키 아래에서 사용할 수 있습니다.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB

요청 단위 요금은 `getLastRequestStatistics`라는 사용자 지정 [데이터베이스 명령](https://docs.mongodb.com/manual/reference/command/)에 의해 표시됩니다. 이 명령은 마지막 실행한 작업의 이름, 해당 요청 요금 및 해당 기간을 포함하는 문서를 반환합니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

현재 Azure Portal에서는 쿼리에 대한 요청 요금만 찾을 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos DB 계정을 만들고](create-mongodb-dotnet.md#create-a-database-account) 해당 계정을 데이터와 함께 피드하거나 이미 데이터를 포함하는 기존 계정을 선택할 수 있습니다.

1. **Data Explorer** 창을 열고 작업할 컬렉션을 선택합니다.

1. **새 쿼리**를 클릭합니다.

1. 올바른 쿼리를 입력한 다음, **쿼리 실행**을 클릭합니다.

1. **쿼리 통계**를 클릭하여 방금 실행한 요청에 대한 실제 요청 요금을 표시합니다.

![Azure Portal의 MongoDB 쿼리 요청 요금 스크린샷](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>MongoDB .NET 드라이버 사용

[공식 MongoDB .NET 드라이버](https://docs.mongodb.com/ecosystem/drivers/csharp/)(사용법은 [이 빠른 시작](create-mongodb-dotnet.md) 참조)를 사용하는 경우 `IMongoDatabase` 개체에 대해 `RunCommand` 메서드를 호출하여 명령을 실행할 수 있습니다. 이 메서드를 호출하려면 `Command<>` 추상 클래스를 구현해야 합니다.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>MongoDB Java 드라이버 사용

[공식 MongoDB Java 드라이버](http://mongodb.github.io/mongo-java-driver/)(사용법은 [이 빠른 시작](create-mongodb-java.md) 참조)를 사용하는 경우 `MongoDatabase` 개체에 대해 `runCommand` 메서드를 호출하여 명령을 실행할 수 있습니다.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js 드라이버 사용

[공식 MongoDB Node.js 드라이버](https://mongodb.github.io/node-mongodb-native/)(사용법은 [이 빠른 시작](create-mongodb-nodejs.md) 참조)를 사용하는 경우 `Db` 개체에 대해 `command` 메서드를 호출하여 명령을 실행할 수 있습니다.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>Cassandra API

Azure Cosmos DB의 Cassandra API에 대해 작업을 수행하는 경우 요청 단위 요금은 수신 페이로드에 `RequestCharge`라는 필드로 반환됩니다.

### <a name="use-the-net-sdk"></a>.NET SDK 사용

[.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)(사용법은 [이 빠른 시작](create-cassandra-dotnet.md) 참조)를 사용하는 경우 `RowSet` 개체의 `Info` 속성 아래에서 수신 페이로드를 검색할 수 있습니다.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Java SDK 사용

[Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)(사용법은 [이 빠른 시작](create-cassandra-java.md) 참조)를 사용하는 경우 `ResultSet` 개체의 `getExecutionInfo()` 메서드를 호출하여 수신 페이로드를 검색할 수 있습니다.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>Gremlin API

### <a name="use-drivers-and-sdk"></a>드라이버 및 SDK 사용

Gremlin API에서 반환되는 헤더는 현재 Gremlin.NET 및 Java SDK에서 표시하는 사용자 지정 상태 특성에 매핑됩니다. 요청 요금은 `x-ms-request-charge` 키 아래에서 사용할 수 있습니다.

### <a name="use-the-net-sdk"></a>.NET SDK 사용

[Gremlin .NET SDK](https://www.nuget.org/packages/Gremlin.Net/)(사용법은 [이 빠른 시작](create-graph-dotnet.md) 참조)를 사용하는 경우 `ResultSet<>` 개체의 `StatusAttributes` 속성 아래에서 상태 특성을 사용할 수 있습니다.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Java SDK 사용

[Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)(사용법은 [이 빠른 시작](create-graph-java.md) 참조)를 사용하는 경우 `ResultSet` 개체에 대해 `statusAttributes()` 메서드를 호출하여 상태 특성을 검색할 수 있습니다.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>테이블 API

현재 테이블 작업에 대한 요청 단위 요금을 반환하는 유일한 SDK는 [.NET 표준 SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)(사용법은 [이 빠른 시작](create-table-dotnet.md) 참조)뿐입니다. `TableResult` 개체는 Azure Cosmos DB의 Table API에 대해 사용하는 경우 SDK에 의해 채워지는 `RequestCharge` 속성을 표시합니다.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>다음 단계

요청 단위 사용의 최적화에 대해 알아보려면 다음 문서를 참조하세요.

* [Azure Cosmos DB에서 프로비전된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB에서 쿼리 비용 최적화](optimize-cost-queries.md)