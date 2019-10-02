---
title: Azure Cosmos DB에서 컨테이너 처리량 프로비전
description: Azure Cosmos DB의 컨테이너 수준에서 처리량을 프로비전하는 방법 알아보기
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 8da27773cc74324c1dde5a95de1abef3256c1f1c
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811670"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너의 프로비전 처리량

이 문서에서는 Azure Cosmos DB에서 컨테이너(컬렉션, 그래프 또는 테이블)의 처리량을 프로비저닝하는 방법을 설명합니다. 단일 컨테이너 또는 [데이터베이스의 처리량을 프로비저닝](how-to-provision-database-throughput.md)하고 데이터베이스 내부의 컨테이너와 처리량을 공유할 수 있습니다. Azure Portal, Azure CLI 또는 Azure Cosmos DB SDK를 사용하여 컨테이너의 처리량을 프로비저닝할 수 있습니다.

## <a name="provision-throughput-using-azure-portal"></a>Azure Portal을 사용하여 처리량 프로비전

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-sql-api-dotnet.md#create-account) 기존 Azure Cosmos 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 컬렉션**을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * 컨테이너(테이블 또는 그래프) ID를 입력합니다.
   * 파티션 키 값을 입력합니다(예: `/userid`).
   * 프로비저닝하려는 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    ![새 컬렉션이 강조 표시된 데이터 탐색기 스크린샷](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell을 사용 하 여 처리량 프로 비전

전용 처리량을 사용 하 여 컨테이너를 만들려면를 참조 하세요.

* [Azure CLI를 사용 하 여 컨테이너 만들기](manage-with-cli.md#create-a-container)
* [Powershell을 사용 하 여 컨테이너 만들기](manage-with-powershell.md#create-container)

> [!Note]
> Azure Cosmos DB API for MongoDB를 사용하여 구성한 Azure Cosmos 계정의 컨테이너에 처리량을 프로비저닝하는 경우 파티션 키 경로로 `/myShardKey`를 사용합니다. Cassandra API를 사용하여 구성한 Azure Cosmos 계정의 컨테이너에 처리량을 프로비저닝하는 경우 파티션 키 경로로 `/myPrimaryKey`를 사용합니다.

## <a name="provision-throughput-by-using-net-sdk"></a>.NET SDK를 사용하여 처리량 프로비전

> [!Note]
> Cassandra API를 제외한 모든 Cosmos DB API의 처리량을 프로비저닝할 때 Cosmos SDKs for SQL API를 사용합니다.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin 및 Table API
### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Azure Cosmos DB에서 처리량을 프로비저닝하는 방법을 알아보세요.

* [데이터베이스에 처리량을 프로비저닝하는 방법](how-to-provision-database-throughput.md)
* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)
