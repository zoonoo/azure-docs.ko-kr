---
title: Azure Cosmos DB에서 컨테이너 만들기
description: Azure Cosmos DB에서 컨테이너를 만드는 방법 알아보기
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 445d9f220a215eb17436d52f637b57bd3492aaae
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864777"
---
# <a name="create-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너 만들기

이 문서에서는 컨테이너(컬렉션, 테이블 또는 그래프)를 만드는 여러 가지 방법을 설명합니다. Azure Portal, Azure CLI 또는 지원되는 SDK를 사용합니다. 이 문서에서는 컨테이너를 만들고 파티션 키를 지정하고 처리량을 프로비저닝하는 방법을 보여줍니다.

## <a name="create-a-container-by-using-azure-portal"></a>Azure Portal을 사용하여 컨테이너 만들기

### <a id="portal-sql"></a>SQL API

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos DB 계정을 만들거나](create-sql-api-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 컬렉션**을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * 컬렉션 ID를 입력합니다.
   * 파티션 키를 입력합니다.
   * 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

![새 컬렉션이 강조 표시된 데이터 탐색기 창 스크린샷](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Azure Cosmos DB API for MongoDB

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos DB 계정을 만들거나](create-mongodb-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 컬렉션**을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * 컬렉션 ID를 입력합니다.
   * **무제한** 저장소 용량을 선택합니다.
   * 분할 키를 입력합니다.
   * 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

![Azure Cosmos DB API for MongoDB, 컬렉션 추가 대화 상자의 스크린샷](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos DB 계정을 만들거나](create-cassandra-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 테이블**을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 키스페이스를 만드는지 아니면 기존 키스페이스를 사용하는지 표시합니다.
   * 테이블 이름을 입력합니다.
   * 속성을 입력하고 기본 키를 지정합니다.
   * 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

![Cassandra API, 테이블 추가 대화 상자의 스크린샷](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API의 경우 기본 키가 파티션 키로 사용됩니다.

### <a id="portal-gremlin"></a>Gremlin API

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos DB 계정을 만들거나](create-graph-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 그래프**를 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * 그래프 ID를 입력합니다.
   * **무제한** 저장소 용량을 선택합니다.
   * 꼭짓점의 파티션 키를 입력합니다.
   * 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

![Gremlin API, 그래프 추가 대화 상자의 스크린샷](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Table API

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos DB 계정을 만들거나](create-table-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 테이블**을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 테이블 ID를 입력합니다.
   * **무제한** 저장소 용량을 선택합니다.
   * 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

![Table API, 테이블 추가 대화 상자의 스크린샷](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Table API의 경우 새 행을 추가할 때마다 파티션 키가 지정됩니다.

## <a name="create-a-container-by-using-azure-cli"></a>Azure CLI를 사용하여 컨테이너 만들기

### <a id="cli-sql"></a>SQL API

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>Azure Cosmos DB API for MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>Cassandra API

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>Gremlin API

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>Table API

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-by-using-net-sdk"></a>.NET SDK를 사용하여 컨테이너 만들기

### <a id="dotnet-sql-graph"></a>SQL API 및 Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>Azure Cosmos DB API for MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
MongoDB 유선 프로토콜은 단위를 요청하지 않습니다. 처리량을 사용하여 새 컬렉션을 만들려면 Azure Portal 또는 SQL API를 사용하세요.

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB에서 분할](partitioning-overview.md)
