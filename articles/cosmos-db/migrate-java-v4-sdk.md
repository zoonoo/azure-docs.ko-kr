---
title: Azure Cosmos DB Java SDK v4 (Cosmos)를 사용 하도록 응용 프로그램 마이그레이션
description: 이전 Azure Cosmos DB Java Sdk를 사용 하 여 기존 java 응용 프로그램을 Core (SQL) API에 대 한 최신 Java SDK 4.0 (Cosmos 패키지)로 업그레이드 하는 방법에 대해 알아봅니다.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.reviewer: sngun
ms.openlocfilehash: 929fa936cdb864fd9b84f8feba55ef01ae6fed9c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984708"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4를 사용 하도록 응용 프로그램 마이그레이션

> [!IMPORTANT]  
> 이 SDK에 대 한 자세한 내용은 Azure Cosmos DB Java SDK v4 릴리스 정보, [Maven 리포지토리](https://mvnrepository.com/artifact/com.azure/azure-cosmos), AZURE COSMOS DB java sdk v4 [성능 팁](performance-tips-java-sdk-v4-sql.md)및 Azure Cosmos DB java sdk v4 [문제 해결 가이드](troubleshoot-java-sdk-v4-sql.md)를 참조 하세요.
>

이 문서에서는 이전 Azure Cosmos DB Java SDK를 사용 하는 기존 Java 응용 프로그램을 최신 Azure Cosmos DB Java SDK 4.0 for Core (SQL) API로 업그레이드 하는 방법을 설명 합니다. Azure Cosmos DB Java SDK v4는 `com.azure.cosmos` 패키지에 해당 합니다. 다음 Azure Cosmos DB Java Sdk 중 하나에서 응용 프로그램을 마이그레이션하는 경우이 문서의 지침을 사용할 수 있습니다. 

* Java SDK 2.x 동기화
* Async Java SDK 2.x. x. x
* Java SDK 3.x. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Java SDK 및 패키지 매핑 Azure Cosmos DB

다음 표에는 다양 한 Azure Cosmos DB Java Sdk, 패키지 이름 및 릴리스 정보가 나와 있습니다.

| Java SDK| 출시 날짜 | 번들 Api   | Maven Jar  | Java 패키지 이름  |API 참조   | 릴리스 정보  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2.x. x. x  | 2018년 6월    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [릴리스 정보](sql-api-sdk-async-java.md) |
| Sync 2.x. x. x     | 9 월 2018    | 동기화   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [릴리스 정보](sql-api-sdk-java.md)  |
| 2.x. x. x    | 2019년 7월    | 비동기 (Reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | 2020년 4월   | 비동기 (Reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>SDK 수준 구현 변경 내용

다음은 서로 다른 Sdk의 키 구현 차이점입니다.

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>Rxjava는 Azure Cosmos DB Java SDK 버전 3.x 및 4.0에서 원자로로 바뀝니다.

비동기 프로그래밍 또는 사후 프로그래밍에 익숙하지 않은 경우 비동기 프로그래밍 및 Project Reactor 소개에 대 한 [reactor 패턴 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) 를 참조 하세요. 이 가이드는 이전에 Java SDK 2.x 또는 Azure Cosmos DB Java SDK 3.x 동기화 API를 사용 하 Azure Cosmos DB를 사용 하는 경우에 유용할 수 있습니다.

Azure Cosmos DB Async Java SDK 2.x를 사용 하 고 있고 4.0 SDK로 마이그레이션할 계획인 경우 reactor [Vs Rxjava 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 를 참조 하 여 rxjava 코드에서 reactor를 사용 하도록 변환 하는 방법에 대 한 지침을 참조 하세요.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4는 비동기 및 동기화 Api 모두에 직접 연결 모드를 포함 합니다.

Java SDK 2.x Azure Cosmos DB Sync를 사용 하는 경우에는 HTTP를 기반으로 하는 직접 연결 모드가 Async 및 Sync Api 모두에 대 한 Azure Cosmos DB Java SDK 4.0에서 구현 됩니다.

## <a name="api-level-changes"></a>API 수준 변경

다음은 이전 Sdk와 비교 하 Azure Cosmos DB Java SDK 4.x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x)의 API 수준 변경 내용입니다.

![Azure Cosmos DB Java SDK 명명 규칙](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* Azure Cosmos DB Java SDK 3.x 및 4.0는 클라이언트 리소스를으로 `Cosmos<resourceName>`참조 합니다. 예: `CosmosClient`, `CosmosDatabase`, `CosmosContainer`. 버전 2.x에서 Azure Cosmos DB Java Sdk에는 일관 된 이름 지정 체계가 없습니다.

* Azure Cosmos DB Java SDK 3.x 및 4.0은 동기화 Api와 비동기 Api를 모두 제공 합니다.

  * **JAVA SDK 4.0** : `Async` 뒤 `Cosmos`에 클래스 이름을 추가 하지 않으면 모든 클래스가 Sync API에 속합니다.

  * **JAVA SDK**3.x: 클래스 이름 뒤 `Async` `Cosmos`에를 추가 하지 않는 한 모든 클래스는 Async API에 속합니다.

  * **Async JAVA sdk**2.x: 클래스 이름은 동기화 Java sdk 2.x와 유사 하지만 이름이 *Async*로 시작 합니다.

### <a name="hierarchical-api-structure"></a>계층 구조 API 구조

Azure Cosmos DB Java SDK 4.0 및 2.x. x. x는 다음 4.0 SDK 코드 조각과 같이 클라이언트, 데이터베이스 및 컨테이너를 중첩 된 방식으로 구성 하는 계층적 API 구조를 도입 합니다.

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

Java SDK Azure Cosmos DB의 버전 2.x에서 리소스 및 문서에 대 한 모든 작업은 클라이언트 인스턴스를 통해 수행 됩니다.

### <a name="representing-documents"></a>문서 표시

Java SDK 4.0 Azure Cosmos DB 사용자 지정 POJO `JsonNodes` 는 Azure Cosmos DB에서 문서를 읽고 쓰는 두 가지 옵션입니다.

Java SDK 3.x Azure Cosmos DB에서 개체는 `CosmosItemProperties` 공용 API에 의해 노출 되며 문서 표현으로 제공 됩니다. 이 클래스는 더 이상 버전 4.0에서 공개적으로 노출 되지 않습니다.

### <a name="imports"></a>가져오기

* Azure Cosmos DB Java SDK 4.0 패키지는 다음으로 시작 합니다.`com.azure.cosmos`
  * Azure Cosmos DB Java SDK 3.x 패키지는 다음으로 시작 합니다.`com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4.0는 중첩 된 패키지 `com.azure.cosmos.models`에 여러 클래스를 배치 합니다. 이러한 패키지 중 일부는 다음과 같습니다.

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * 위의 모든 패키지에 대 한 Async API 동일
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`... 등.

### <a name="accessors"></a>접근자

Azure Cosmos DB Java SDK 4.0는 `get` 및 `set` 메서드를 노출 하 여 인스턴스 멤버에 액세스 합니다. 예를 `CosmosContainer` 들어 인스턴스에 `container.getId()` 및 `container.setId()` 메서드가 있습니다.

이는 흐름 인터페이스를 노출 하는 Java SDK 3.x. x. x Azure Cosmos DB와 다릅니다. 예를 들어 인스턴스 `CosmosSyncContainer` `container.id()` 는 `id` 값을 가져오거나 설정 하기 위해 오버 로드 됩니다.

## <a name="code-snippet-comparisons"></a>코드 조각 비교

### <a name="create-resources"></a>리소스 만들기

다음 코드 조각에서는 4.0 및 2.x 비동기 Api 간에 리소스를 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x. x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPo"ic"();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>항목 작업

다음 코드 조각에서는 4.0 및 2.x 비동기 Api 간에 항목 작업을 수행 하는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x. x Async API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>인덱싱

다음 코드 조각에서는 4.0 및 2.x 비동기 Api 간에 인덱싱이 만들어지는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x. x Async API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>저장 프로시저

다음 코드 조각에서는 4.0 및 2.x 비동기 Api 간에 저장 프로시저를 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x. x Async API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>변경 피드

다음 코드 조각에서는 4.0 및 2.x 비동기 Api 간에 변경 피드 작업을 실행 하는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        logger.info("--->handleChanges() END");

    })
    .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x. x Async API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>컨테이너 수준 TTL (time-to-live)

다음 코드 조각에서는 4.0 및 2.x 비동기 Api를 사용 하 여 컨테이너의 데이터에 대해 ttl (time to live)을 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x. x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>항목 수준 TTL (time-to-live)

다음 코드 조각에서는 4.0 및 2.x 비동기 Api를 사용 하 여 항목에 대해 ttl (time to live)을 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x. x Async API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>다음 단계

* V4 SDK를 사용 하 여 Azure Cosmos DB SQL API 데이터를 관리 하 [는 Java 앱 빌드](create-sql-api-java.md)
* [재 행위자 기반 Java sdk](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) 에 대해 알아보기
* [Reactor 및 Rxjava 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 를 사용 하 여 RxJava 비동기 코드를 reactor 비동기 코드로 변환 하는 방법에 대해 알아봅니다.