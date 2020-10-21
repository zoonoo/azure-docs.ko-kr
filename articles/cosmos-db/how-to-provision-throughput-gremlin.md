---
title: Azure Cosmos DB Gremlin API 리소스에 대 한 처리량 프로 비전
description: Azure Cosmos DB Gremlin API 리소스에서 컨테이너, 데이터베이스 및 자동 크기 조정 처리량을 프로 비전 하는 방법에 대해 알아봅니다. Azure Portal, CLI, PowerShell 및 기타 다양 한 Sdk를 사용 합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 70eecc7843867a5832d962b7efaecda1b6ab4ae4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284003"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Azure Cosmos DB Gremlin API 리소스에 대 한 데이터베이스, 컨테이너 또는 자동 크기 조정 처리량 프로 비전

이 문서에서는 Azure Cosmos DB Gremlin API에서 처리량을 프로 비전 하는 방법을 설명 합니다. 컨테이너 또는 데이터베이스에 표준 (수동) 또는 자동 크기 조정 처리량을 프로 비전 하 고 데이터베이스 내의 컨테이너 간에 공유할 수 있습니다. Azure Portal, Azure CLI 또는 Azure Cosmos DB Sdk를 사용 하 여 처리량을 프로 비전 할 수 있습니다.

다른 API를 사용 하는 경우 처리량을 프로 비전 하려면 [SQL api](how-to-provision-container-throughput.md), [Cassandra API](how-to-provision-throughput-cassandra.md), [API for MongoDB](how-to-provision-throughput-mongodb.md) 문서를 참조 하세요.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure Portal

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-mongodb-dotnet.md#create-a-database-account) 기존 Azure Cosmos 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 그래프**를 선택 합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다. 데이터베이스 수준에서 처리량을 프로 비전 하려면 **데이터베이스 처리량 프로 비전** 옵션을 선택 합니다.
   * 그래프 ID를 입력합니다.
   * 파티션 키 값을 입력합니다(예: `/ItemID`).
   * 프로비저닝하려는 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="데이터베이스 수준 처리량을 사용 하 여 새 그래프를 만들 때 데이터 탐색기의 스크린샷":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Cosmos Sdk for SQL API를 사용 하 여 Cassandra 및 MongoDB API를 제외한 모든 Azure Cosmos DB Api에 대 한 처리량을 프로 비전 합니다.

### <a name="provision-container-level-throughput"></a>컨테이너 수준 처리량 프로 비전

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

### <a name="provision-database-level-throughput"></a>데이터베이스 수준 처리량 프로 비전

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager 템플릿을 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 예제는 [Azure Cosmos DB Azure Resource Manager 템플릿을](templates-samples-gremlin.md) 참조 하세요.

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 샘플은 [Azure Cosmos DB Azure CLI 샘플](cli-samples-gremlin.md)을 참조 하세요.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell를 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 샘플은 [Azure Cosmos DB Azure PowerShell 샘플](powershell-samples-gremlin.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Azure Cosmos DB에서 처리량을 프로비저닝하는 방법을 알아보세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)