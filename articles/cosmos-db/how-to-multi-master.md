---
title: Azure Cosmos DB에서 다중 지역 쓰기를 구성 하는 방법
description: Azure Cosmos DB에서 다른 Sdk를 사용 하 여 응용 프로그램에 대 한 다중 지역 쓰기를 구성 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/10/2020
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 109ce54b48df0724c0d4ba64c02721088ed7ffad
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330634"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Azure Cosmos DB를 사용 하는 응용 프로그램에서 다중 지역 쓰기 구성
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

여러 쓰기 지역을 사용 하도록 설정 된 계정을 만든 후에는 Azure Cosmos DB에서 다중 지역 쓰기 및 다중 호 밍 기능을 사용할 수 있도록 DocumentClient에 대 한 ConnectionPolicy에 대해 응용 프로그램에서 두 가지 변경을 수행 해야 합니다. ConnectionPolicy 내에서 UseMultipleWriteLocations를 true로 설정하고 애플리케이션이 배포되는 지역 이름을 SetCurrentLocation으로 전달합니다. 이를 통해 전달된 위치에서 지역 근접도에 따라 PreferredLocations 속성이 채워집니다. 나중에 계정에 새 지역이 추가되는 경우 애플리케이션은 업데이트되거나 다시 배포될 필요 없이 가까운 지역을 자동으로 탐지하고 자동 호밍되어 지역 이벤트가 발생합니다.

> [!Note]
> 단일 쓰기 지역으로 처음 구성 된 Cosmos 계정은 중단 시간이 0 인 여러 쓰기 지역으로 구성 될 수 있습니다. 자세한 내용은 [다중 쓰기 지역 구성](how-to-manage-database-account.md#configure-multiple-write-regions)을 참조하세요.

## <a name="azure-portal"></a><a id="portal"></a> Azure Portal

Azure Portal에서 다중 지역 쓰기를 사용 하도록 설정 하려면 다음 단계를 사용 합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. Azure Cosmos 계정으로 이동 하 고 메뉴에서 **전역으로 데이터 복제** 창을 엽니다.

1. **다중 지역 쓰기** 옵션에서 **사용** 을 선택 합니다. 영역을 읽고 쓰기 위해 기존 영역을 자동으로 추가 합니다.

1. 지도에서 아이콘을 선택 하거나 **지역 추가** 단추를 선택 하 여 영역을 더 추가할 수 있습니다. 추가 하는 모든 지역에서 읽기 및 쓰기를 모두 사용할 수 있습니다.

1. 지역 목록을 업데이트 한 후 **저장** 을 선택 하 여 변경 내용을 적용 합니다.

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="Azure Portal를 사용 하 여 다중 지역 쓰기를 사용 하도록 설정 하는 스크린샷" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

응용 프로그램에서 다중 지역 쓰기를 사용 하도록 설정 하려면 `UseMultipleWriteLocations` 를로 설정 `true` 합니다. 또한 `SetCurrentLocation`을 애플리케이션이 배포되고 Azure Cosmos DB가 복제되는 지역으로 설정합니다.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

응용 프로그램에서 다중 지역 쓰기를 사용 하도록 설정 하려면 `ApplicationRegion` 응용 프로그램이 배포 되는 영역 및 Cosmos DB 복제 되는 위치를 설정 합니다.

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

필요에 따라 `CosmosClientBuilder` 및 `WithApplicationRegion`을 사용하여 동일한 결과를 얻을 수 있습니다.

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java V4 SDK

응용 프로그램에서 다중 지역 쓰기를 사용 하도록 설정 하려면 `.multipleWriteRegionsEnabled(true)` `.preferredRegions(preferredRegions)` 클라이언트 작성기에서 및를 호출 합니다 `preferredRegions` . 여기서는 `List` 응용 프로그램이 배포 되는 영역인 Cosmos DB 복제 되는 영역인 하나의 요소를 포함 하는입니다.

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md)(Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[동기화](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md)(Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Async Java V2 SDK

Java V2 SDK는 Maven [:: azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)를 사용 했습니다. 응용 프로그램에서 다중 지역 쓰기를 사용 하도록 설정 하려면를 설정 하 `policy.setUsingMultipleWriteLocations(true)` 고를 `policy.setPreferredLocations` 응용 프로그램이 배포 되는 지역으로 설정 하 고 Cosmos DB 복제 합니다.

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript 및 TypeScript SDK

응용 프로그램에서 다중 지역 쓰기를 사용 하도록 설정 하려면 `connectionPolicy.UseMultipleWriteLocations` 를로 설정 `true` 합니다. 또한 `connectionPolicy.PreferredLocations`를 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

응용 프로그램에서 다중 지역 쓰기를 사용 하도록 설정 하려면 `connection_policy.UseMultipleWriteLocations` 를로 설정 `true` 합니다. 또한 `connection_policy.PreferredLocations`를 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>다음 단계

다음 문서를 읽어보세요.

* [Azure Cosmos DB에서 세션 토큰을 사용하여 일관성 관리](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB의 충돌 유형 및 해결 정책](conflict-resolution-policies.md)
* [Azure Cosmos DB의 고가용성](high-availability.md)
* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
* [Azure Cosmos DB에서 올바른 일관성 수준 선택](./consistency-levels.md)
* [Azure Cosmos DB의 일관성, 가용성 및 성능 절충](./consistency-levels.md)
* [다양한 일관성 수준의 가용성 및 성능 절충](./consistency-levels.md)
* [전역적으로 프로비전된 처리량 크기 조정](./request-units.md)
* [전역 배포: 내부적으로](global-dist-under-the-hood.md)