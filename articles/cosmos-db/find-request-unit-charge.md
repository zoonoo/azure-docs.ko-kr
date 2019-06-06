---
title: Azure Cosmos DB에서 RU(요청 단위) 요금 찾기
description: Azure Cosmos 컨테이너에 대해 실행한 작업의 RU(요청 단위) 요금을 알아보는 방법을 배웁니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 0671556a1ad049782090ffede509072adbac4c6a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416043"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Azure Cosmos DB에서 요청 단위 요금 알아보기

이 문서에서는 Azure Cosmos DB의 컨테이너에 대해 실행한 작업의 [RU(요청 단위)](request-units.md) 사용량을 알아보는 다양한 방법을 설명합니다. 현재는 Azure Portal을 사용하거나 SDK 중 하나를 통해 Azure Cosmos DB로 다시 전송된 응답을 검사하는 방법으로만 이 사용량을 측정할 수 있습니다.

## <a name="sql-core-api"></a>SQL(Core) API

SQL API를 사용하는 경우 Azure Cosmos 컨테이너에 대해 실행한 작업의 RU 사용량을 알아보는 여러 옵션이 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

현재는 Azure Portal에서 SQL 쿼리에 대한 요청 요금만 알아볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들고](create-sql-api-dotnet.md#create-account) 데이터를 입력하거나, 이미 데이터가 들어 있는 기존 Azure Cosmos 계정을 선택합니다.

1. **Data Explorer** 창으로 이동한 다음, 작업할 컨테이너를 선택합니다.

1. **새 SQL 쿼리**를 선택합니다.

1. 유효한 쿼리를 입력한 다음, **쿼리 실행**을 선택합니다.

1. **쿼리 통계**를 선택하여 방금 실행한 요청의 실제 요청 요금을 표시합니다.

![Azure Portal의 SQL 쿼리 요청 요금 스크린샷](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>.NET SDK V2 사용

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)에서 반환되는 개체는 다음과 같이 `RequestCharge` 속성을 표시합니다.

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

자세한 내용은 [빠른 시작: Azure Cosmos DB의 SQL API 계정을 사용하여 .NET 웹앱 빌드](create-sql-api-dotnet.md)를 참조하세요.

### <a name="use-the-java-sdk"></a>Java SDK 사용

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)에서 반환되는 개체는 다음과 같이 `getRequestCharge()` 메서드를 표시합니다.

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

자세한 내용은 [빠른 시작: Azure Cosmos DB SQL API 계정을 사용하여 Java 애플리케이션 빌드](create-sql-api-java.md)를 참조하세요.

### <a name="use-the-nodejs-sdk"></a>Node.js SDK 사용

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)에서 반환되는 개체는 기본 HTTP API가 반환한 모든 헤더를 매핑하는 `headers` 하위 개체를 표시합니다. 요청 요금은 다음과 같이 `x-ms-request-charge` 키 아래에서 확인할 수 있습니다.

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

자세한 내용은 [빠른 시작: Azure Cosmos DB SQL API 계정을 사용하여 Node.js 앱 빌드](create-sql-api-nodejs.md)를 참조하세요. 

### <a name="use-the-python-sdk"></a>Python SDK 사용

[Python SDK](https://pypi.org/project/azure-cosmos/)의 `CosmosClient` 개체는 마지막으로 실행된 작업의 기본 HTTP API에서 반환된 모든 헤더를 매핑하는 `last_response_headers` 사전을 표시합니다. 요청 요금은 다음과 같이 `x-ms-request-charge` 키 아래에서 확인할 수 있습니다.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

자세한 내용은 [빠른 시작: Azure Cosmos DB SQL API 계정을 사용하여 Python 앱 빌드](create-sql-api-python.md)를 참조하세요. 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB API for MongoDB

RU 요금은 `getLastRequestStatistics`라는 사용자 지정 [데이터베이스 명령](https://docs.mongodb.com/manual/reference/command/)을 통해 표시됩니다. 이 명령은 마지막으로 실행된 작업의 이름, 해당 작업의 요청 요금 및 해당 작업 기간이 포함된 문서를 반환합니다. Azure Cosmos DB API for MongoDB를 사용하는 경우 RU 요금을 검색하는 여러 옵션이 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

현재는 Azure Portal에서 쿼리에 대한 요청 요금만 알아볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들고](create-mongodb-dotnet.md#create-a-database-account) 데이터를 입력하거나, 이미 데이터가 들어 있는 기존 계정을 선택합니다.

1. **Data Explorer** 창으로 이동한 다음, 작업할 컬렉션을 선택합니다.

1. **새 쿼리**를 선택합니다.

1. 유효한 쿼리를 입력한 다음, **쿼리 실행**을 선택합니다.

1. **쿼리 통계**를 선택하여 방금 실행한 요청의 실제 요청 요금을 표시합니다.

![Azure Portal의 MongoDB 쿼리 요청 요금 스크린샷](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>MongoDB .NET 드라이버 사용

[공식 MongoDB .NET 드라이버](https://docs.mongodb.com/ecosystem/drivers/csharp/)를 사용하는 경우 `IMongoDatabase` 개체에서 `RunCommand` 메서드를 호출하여 명령을 실행할 수 있습니다. 이 메서드를 호출하려면 다음과 같이 `Command<>` 추상 클래스를 구현해야 합니다.

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

자세한 내용은 [빠른 시작: Azure Cosmos DB API for MongoDB를 사용하여 .NET 웹앱 빌드](create-mongodb-dotnet.md)를 참조하세요.

### <a name="use-the-mongodb-java-driver"></a>MongoDB Java 드라이버 사용


[공식 MongoDB Java 드라이버](https://mongodb.github.io/mongo-java-driver/)를 사용하는 경우 `MongoDatabase` 개체에서 `runCommand` 메서드를 호출하여 명령을 실행할 수 있습니다.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

자세한 내용은 [빠른 시작: Azure Cosmos DB API for MongoDB 및 Java SDK를 사용하여 웹앱 빌드](create-mongodb-java.md)를 참조하세요.

### <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js 드라이버 사용

[공식 MongoDB Node.js 드라이버](https://mongodb.github.io/node-mongodb-native/)를 사용하는 경우 `db` 개체에서 `command` 메서드를 호출하여 명령을 실행할 수 있습니다.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

자세한 내용은 [빠른 시작: 기존 MongoDB Node.js 웹앱을 Azure Cosmos DB로 마이그레이션](create-mongodb-nodejs.md)을 참조하세요.

## <a name="cassandra-api"></a>Cassandra API

Azure Cosmos DB Cassandra API에 대해 작업을 수행하는 경우 RU 요금은 수신 페이로드에 `RequestCharge`라는 필드로 반환됩니다. 여러 가지 방법으로 RU 요금을 검색할 수 있습니다.

### <a name="use-the-net-sdk"></a>.NET SDK 사용

[.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)를 사용하는 경우 `RowSet` 개체의 `Info` 속성 아래에서 수신 페이로드를 검색할 수 있습니다.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

자세한 내용은 [빠른 시작: .NET SDK 및 Azure Cosmos DB를 사용하여 Cassandra 앱 빌드](create-cassandra-dotnet.md)를 참조하세요.

### <a name="use-the-java-sdk"></a>Java SDK 사용

[Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)를 사용하는 경우 `ResultSet` 개체에서 `getExecutionInfo()` 메서드를 호출하여 수신 페이로드를 검색할 수 있습니다.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

자세한 내용은 [빠른 시작: Java SDK 및 Azure Cosmos DB를 사용하여 Cassandra 앱 빌드](create-cassandra-java.md)를 참조하세요.

## <a name="gremlin-api"></a>Gremlin API

Gremlin API를 사용하는 경우 Azure Cosmos 컨테이너에 대해 실행한 작업의 RU 사용량을 알아보는 여러 옵션이 있습니다. 

### <a name="use-drivers-and-sdk"></a>드라이버 및 SDK 사용

Gremlin API에서 반환되는 헤더는 현재 Gremlin .NET 및 Java SDK에서 표시하는 사용자 지정 상태 특성에 매핑됩니다. 요청 요금은 `x-ms-request-charge` 키 아래에서 사용할 수 있습니다.

### <a name="use-the-net-sdk"></a>.NET SDK 사용

[Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/)를 사용하는 경우 `ResultSet<>` 개체의 `StatusAttributes` 속성 아래에서 상태 특성을 사용할 수 있습니다.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

자세한 내용은 [빠른 시작: Azure Cosmos DB Gremlin API 계정을 사용하여 .NET Framework 또는 Core 애플리케이션 빌드](create-graph-dotnet.md)를 참조하세요.

### <a name="use-the-java-sdk"></a>Java SDK 사용

[Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)를 사용하는 경우 `ResultSet` 개체의 `statusAttributes()` 메서드를 호출하여 상태 특성을 검색할 수 있습니다.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

자세한 내용은 [빠른 시작: Java SDK를 사용하여 Azure Cosmos DB에서 그래프 데이터베이스 만들기](create-graph-java.md)를 참조하세요.

## <a name="table-api"></a>테이블 API

현재 테이블 작업에 대한 RU 요금을 반환하는 유일한 SDK는 [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)입니다. `TableResult` 개체는 Azure Cosmos DB Table API에 대해 사용할 경우 SDK에 의해 자동으로 채워지는 `RequestCharge` 속성을 표시합니다.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

자세한 내용은 [빠른 시작: .NET SDK 및 Azure Cosmos DB를 사용하여 Table API 앱 빌드](create-table-dotnet.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

RU 사용량을 최적화하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](optimize-cost-queries.md)
* [프로비저닝된 처리량을 전역적으로 크기 조정](scaling-throughput.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [컨테이너의 처리량 프로비전](how-to-provision-container-throughput.md)
