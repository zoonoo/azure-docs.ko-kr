---
title: Azure Cosmos DB .NET SDK 3.0 (Cosmos)를 사용 하도록 응용 프로그램을 마이그레이션합니다.
description: 기존 .NET 응용 프로그램을 v2 SDK에서 코어 (SQL) API에 대 한 최신 .NET SDK v3 (cosmos 패키지)로 업그레이드 하는 방법에 대해 알아봅니다.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 3cddf76ae0157c6729e6f58e40ee3f1725a9d395
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096668"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Azure Cosmos DB .NET SDK v3를 사용 하도록 응용 프로그램 마이그레이션
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB .NET SDK v3에 대해 알아보려면 [릴리스 정보](sql-api-sdk-dotnet-standard.md), [.net GitHub 리포지토리](https://github.com/Azure/azure-cosmos-dotnet-v3), .Net SDK V3 [성능 팁](performance-tips-dotnet-sdk-v3-sql.md)및 [문제 해결 가이드](troubleshoot-dot-net-sdk.md)를 참조 하세요.
>

이 문서에서는 기존 .NET 응용 프로그램을 최신 Azure Cosmos DB .NET SDK v3 for Core (SQL) API로 업그레이드 하는 경우의 몇 가지 고려 사항을 중점적으로 설명 합니다. Azure Cosmos DB .NET SDK v3은 Cosmos 네임 스페이스에 해당 합니다. 다음 Azure Cosmos DB .NET Sdk 중 하나에서 응용 프로그램을 마이그레이션하는 경우이 문서에 제공 된 정보를 사용할 수 있습니다.

* SQL API 용 Azure Cosmos DB .NET Framework SDK v2
* SQL API 용 Azure Cosmos DB .NET Core SDK v2

이 문서의 지침을 참조 하 여 이제 코어 (SQL) API에 대 한 Azure Cosmos DB .NET SDK v3의 일부인 다음 외부 라이브러리를 마이그레이션할 수 있습니다.

* .NET 변경 피드 프로세서 라이브러리 2.0
* .NET 대량 실행자 라이브러리 1.1 이상

## <a name="whats-new-in-the-net-v3-sdk"></a>.NET V3 SDK의 새로운 기능

V3 SDK에는 다음과 같은 다양 한 유용성 및 성능 향상이 포함 되어 있습니다.

* 직관적인 프로그래밍 모델 명명
* .NET Standard 2.0 * *
* Stream API 지원을 통해 성능이 향상 됩니다.
* URI 팩터리의 필요성을 대체 하는 흐름 계층 구조
* 변경 피드 프로세서 라이브러리에 대 한 기본 제공 지원
* 대량 작업에 대 한 기본 제공 지원
* 보다 쉬운 유닛 테스트를 위한 Api Mockabale
* 트랜잭션 일괄 처리 및 Blazor 지원
* 플러그형 직렬 변환기
* 분할 되지 않은 크기 조정 및 자동 크기 조정 컨테이너 크기 조정

* * SDK는 기존 Azure Cosmos DB .NET Framework와 .NET Core Sdk를 단일 .NET SDK로 통합 하는 .NET Standard 2.0를 대상으로 합니다. .NET Framework 4.6.1 + 및 .NET Core 2.0 + 응용 프로그램을 포함 하 여 .NET Standard 2.0을 구현 하는 모든 플랫폼에서 .NET SDK를 사용할 수 있습니다.

대부분의 네트워킹, 재시도 논리 및 낮은 수준의 SDK는 거의 변경 되지 않습니다.

**Azure Cosmos DB .NET SDK v3은 이제 오픈 소스입니다.** 모든 끌어오기 요청을 시작 하 고 GitHub에서 문제를 기록 하 고 피드백을 추적 [합니다.](https://github.com/Azure/azure-cosmos-dotnet-v3/) 사용자 환경을 개선 하는 모든 기능을 사용 하 여 작업을 수행 합니다.

## <a name="why-migrate-to-the-net-v3-sdk"></a>.NET v3 SDK로 마이그레이션하는 이유

수많은 유용성 및 성능 향상 외에도 최신 SDK에서 수행 되는 새로운 기능 투자가 이전 버전으로 다시 이식할 수 없습니다.

[2.0 sdk에 대 한 지원의](sql-api-sdk-dotnet.md)사용을 중지 하는 즉각적인 계획은 없지만 sdk는 나중에 최신 버전으로 대체 되 고 sdk는 유지 관리 모드로 전환 됩니다. 최상의 개발 환경을 위해 항상 지원 되는 최신 버전의 SDK로 시작 하는 것이 좋습니다.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>V2 SDK에서 v3 SDK로의 주요 이름 변경

다음 이름 변경은 .NET 3.0 SDK 전체에서 적용 되어 코어 (SQL) API에 대 한 API 명명 규칙에 맞게 적용 되었습니다.

* `DocumentClient` 이름이로 바뀜 `CosmosClient`
* `Collection` 이름이로 바뀜 `Container`
* `Document` 이름이로 바뀜 `Item`

추가 속성을 사용 하 여 모든 리소스 개체의 이름을 변경 합니다. 여기에는 명확성을 위한 리소스 이름이 포함 됩니다.

다음은 몇 가지 주요 클래스 이름 변경 내용입니다.

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>.NET v3 SDK에서 대체 되는 클래스

다음 클래스는 3.0 SDK에서 바뀌었습니다.

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Microsoft.Azure.Documents입니다. UriFactory 클래스가 흐름 design으로 바뀌었습니다. 흐름 디자인은 Url을 내부적으로 빌드하고 `Container` , 및 대신 단일 개체가 전달 될 수 있도록 합니다 `DocumentClient` `DatabaseName` `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>항목 ID 생성 변경

항목 ID는 더 이상 .NET v3 SDK에서 자동으로 채워지지 않습니다. 따라서 항목 ID는 특별히 생성 된 ID를 포함 해야 합니다. 다음 예제를 봅니다.

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>연결 모드에 대 한 기본 동작 변경

이제 SDK v3은 이전 v2 SDK와 비교 하 여 직접 + TCP 연결 모드로 설정 되며,이는 기본적으로 게이트웨이 + HTTPS 연결 모드로 설정 됩니다. 이 변경은 향상 된 성능 및 확장성을 제공 합니다.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>FeedOptions에 대 한 변경 내용 (v 3.0 SDK의 QueryRequestOptions)

Sdk v 2의 `FeedOptions` 클래스는 이제 `QueryRequestOptions` sdk v3 및 클래스 내에서로 이름이 변경 되었습니다. 일부 속성의 이름 및/또는 기본값은 변경 되었거나 완전히 제거 되었습니다.  

`FeedOptions.MaxDegreeOfParallelism` 는로 이름이 바뀌고 `QueryRequestOptions.MaxConcurrency` 기본값은 동일 하 게 유지 되 고, 병렬 쿼리 실행 중에 클라이언트 쪽에서 실행 되는 작업은 병렬 처리 없이 직렬로 실행 됩니다.

`FeedOptions.EnableCrossPartitionQuery` 는 제거 되었으며 SDK 3.0의 기본 동작은 속성을 사용 하도록 설정 하지 않아도 파티션 간 쿼리가 실행 된다는 것입니다.

`FeedOptions.PopulateQueryMetrics` 는 응답의 진단 속성에 표시 되는 결과를 사용 하 여 기본적으로 사용 하도록 설정 됩니다.

`FeedOptions.RequestContinuation` 는 이제 쿼리 메서드로 승격 되었습니다.

다음 속성이 제거 되었습니다.

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>클라이언트 생성

.NET SDK v3은 `CosmosClientBuilder` sdk V2 URI 팩터리의 필요성을 대체 하는 흐름 클래스를 제공 합니다.

다음 예제에서는 `CosmosClientBuilder` 강력한 ConsistencyLevel 및 기본 위치 목록을 사용 하 여 새를 만듭니다.

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>V3 SDK에서 직접 변경 피드 프로세서 Api 사용

V3 SDK는 변경 피드 프로세서 Api에 대 한 기본 제공 지원을 제공 하므로 동일한 SDK를 사용 하 여 응용 프로그램을 빌드하고 피드 프로세서 구현을 변경할 수 있습니다. 이전에는 별도의 변경 피드 프로세서 라이브러리를 사용 해야 했습니다.

자세한 내용은 [변경 피드 프로세서 라이브러리에서 Azure Cosmos DB .net V3 SDK로 마이그레이션하는 방법](how-to-migrate-from-change-feed-library.md) 을 참조 하세요.

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>V3 SDK에서 직접 bulk executor 라이브러리 사용

V3 SDK는 대량 작업을 수행할 때 동일한 SDK를 사용 하 여 응용 프로그램을 빌드하고 대량 작업을 수행할 수 있도록 하는 대량 실행자 라이브러리를 기본적으로 지원 합니다. 이전에는 별도의 대량 실행자 라이브러리를 사용 해야 했습니다.

자세한 내용은 [대량 실행자 라이브러리에서 Azure Cosmos DB .Net V3 SDK에서 대량 지원으로 마이그레이션하는 방법](how-to-migrate-from-bulk-executor-library.md) 을 참조 하세요.

## <a name="code-snippet-comparisons"></a>코드 조각 비교

다음 코드 조각에서는 .NET v2와 v3 Sdk 간에 리소스를 만드는 방법의 차이점을 보여 줍니다.

## <a name="database-operations"></a>데이터베이스 작업

### <a name="create-a-database"></a>데이터베이스 만들기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>ID로 데이터베이스 읽기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>데이터베이스 삭제

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>컨테이너 작업

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>컨테이너 만들기 (자동 크기 조정 + 만료 시간에 대 한 ttl)

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>컨테이너 속성 읽기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>컨테이너 삭제

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>항목 및 쿼리 작업

### <a name="create-an-item"></a>항목 만들기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>컨테이너의 모든 항목 읽기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>쿼리 항목

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>항목 삭제

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>다음 단계

* V3 SDK를 사용 하 여 Azure Cosmos DB SQL API 데이터를 관리 하 [는 콘솔 앱 빌드](sql-api-get-started.md)
* [V3 SDK로 수행할 수 있는 작업](sql-api-dotnet-v3sdk-samples.md) 에 대 한 자세한 정보
