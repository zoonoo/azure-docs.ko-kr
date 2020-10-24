---
title: Azure Cosmos DB Gremlin API에서 컨테이너 만들기
description: Azure Portal, .NET 및 기타 Sdk를 사용 하 여 Azure Cosmos DB Gremlin API에서 컨테이너를 만드는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 55dabccf5a5ab9e81ff0644056002f3beb644fa9
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491158"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API에서 컨테이너 만들기

이 문서에서는 Azure Cosmos DB Gremlin API에서 컨테이너를 만드는 다양 한 방법을 설명 합니다. Azure Portal, Azure CLI, PowerShell 또는 지원 되는 Sdk를 사용 하 여 컨테이너를 만드는 방법을 보여 줍니다. 이 문서에서는 컨테이너를 만들고 파티션 키를 지정하고 처리량을 프로비저닝하는 방법을 보여줍니다.

이 문서에서는 Azure Cosmos DB Gremlin API에서 컨테이너를 만드는 다양 한 방법을 설명 합니다. 다른 API를 사용 하는 경우 MongoDB, [Cassandra API](how-to-create-container-cassandra.md), [TABLE API](how-to-create-container-table.md)및 [SQL api](how-to-create-container.md) 아티클의 [api](how-to-create-container-mongodb.md)를 참조 하 여 컨테이너를 만듭니다.

> [!NOTE]
> 컨테이너를 만들 때 이름은 같지만 대/소문자가 다른 두 개의 컨테이너를 만들지 않도록 해야 합니다. Azure 플랫폼의 일부 부분에서는 대/소문자를 구분하지 않으므로 이로 인해 이러한 이름을 가진 컨테이너에 대한 원격 분석 및 작업의 혼동/충돌이 발생할 수 있습니다.

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>Azure Portal을 사용하여 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-graph-dotnet.md#create-a-database-account)기존 계정을 선택 합니다.

1. **데이터 탐색기** 창을 열고 **새 그래프**를 선택 합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * Graph ID를 입력합니다.
   * **무제한** 스토리지 용량을 선택합니다.
   * 꼭짓점의 파티션 키를 입력합니다.
   * 프로비저닝할 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Gremlin API, 그래프 추가 대화 상자의 스크린샷":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>.NET SDK를 사용 하 여 만들기

컬렉션을 만들 때 시간 제한 예외가 발생 하면 읽기 작업을 수행 하 여 컬렉션이 성공적으로 만들어졌는지 확인 합니다. 읽기 작업은 컬렉션 만들기 작업이 성공할 때까지 예외를 throw 합니다. 만들기 작업에서 지원 되는 상태 코드 목록은 [Azure Cosmos DB에 대 한 HTTP 상태 코드](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 문서를 참조 하세요.

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

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Azure CLI를 사용 하 여 만들기

[Azure CLI를 사용 하 여 Gremlin 그래프를 만듭니다](./scripts/cli/gremlin/create.md). 모든 Azure Cosmos DB Api에 대 한 모든 Azure CLI 샘플 목록은 [Azure Cosmos DB에 대 한 Azure CLI 샘플](cli-samples.md)을 참조 하세요.

## <a name="create-using-powershell"></a>PowerShell을 사용 하 여 만들기

[PowerShell을 사용 하 여 Gremlin graph를 만듭니다](./scripts/powershell/gremlin/create.md). 모든 Azure Cosmos DB Api에 대 한 모든 PowerShell 샘플 목록은 [Powershell 샘플](powershell-samples.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](./account-databases-containers-items.md)