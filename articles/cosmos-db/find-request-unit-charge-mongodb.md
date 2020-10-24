---
title: Azure Cosmos DB API for MongoDB 작업에 대 한 요청 단위 요금을 찾습니다.
description: Azure Cosmos 컨테이너에 대해 실행 되는 MongoDB 쿼리에 대 한 (요청 단위) 요금을 찾는 방법에 대해 알아봅니다. Azure Portal MongoDB .NET, Java, Node.js 드라이버를 사용할 수 있습니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6b1e2d6e9d5d06410596a40f16869d7c1a0f094e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490699"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>MongoDB 용 Azure Cosmos DB API에서 실행 된 작업에 대 한 요청 단위 요금을 찾습니다.

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업 비용은 Azure Cosmos DB에서 정규화되고 RU(요청 단위)를 기준으로 표시됩니다. RUs는 Azure Cosmos DB에서 지 원하는 데이터베이스 작업을 수행 하는 데 필요한 CPU, IOPS 및 메모리와 같은 시스템 리소스를 추상화 하는 성능 통화로 간주할 수 있습니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리 인지에 상관 없이 비용은 항상 RUs로 측정 됩니다. 자세히 알아보려면 [요청 단위 및 it 고려 사항](request-units.md) 문서를 참조 하세요.

이 문서에서는 MongoDB 용 Azure Cosmos DB API의 컨테이너에 대해 실행 된 모든 작업에 대해 작업을 수행 [하는 데](request-units.md) 필요한 다양 한 방법을 제공 합니다. 다른 API를 사용 하는 경우 [SQL api](find-request-unit-charge.md), [CASSANDRA API](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)및 [Table API](find-request-unit-charge-table.md) 문서를 참조 하 여 r u/초 요금을 확인 하세요.

RU 요금은 `getLastRequestStatistics`라는 사용자 지정 [데이터베이스 명령](https://docs.mongodb.com/manual/reference/command/)을 통해 표시됩니다. 이 명령은 마지막으로 실행된 작업의 이름, 해당 작업의 요청 요금 및 해당 작업 기간이 포함된 문서를 반환합니다. Azure Cosmos DB API for MongoDB를 사용하는 경우 RU 요금을 검색하는 여러 옵션이 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들고](create-mongodb-dotnet.md#create-a-database-account) 데이터를 입력하거나, 이미 데이터가 들어 있는 기존 계정을 선택합니다.

1. **Data Explorer** 창으로 이동한 다음, 작업할 컨테이너를 선택합니다.

1. **새 쿼리**를 선택합니다.

1. 유효한 쿼리를 입력한 다음, **쿼리 실행**을 선택합니다.

1. **쿼리 통계**를 선택하여 방금 실행한 요청의 실제 요청 요금을 표시합니다.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Azure Portal의 MongoDB 쿼리 요청 요금 스크린샷":::

## <a name="use-the-mongodb-net-driver"></a>MongoDB .NET 드라이버 사용

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

자세한 내용은 [빠른 시작: MONGODB API를 사용 Azure Cosmos DB 하 여 .net 웹 앱 빌드](create-mongodb-dotnet.md)를 참조 하세요.

## <a name="use-the-mongodb-java-driver"></a>MongoDB Java 드라이버 사용


[공식 MongoDB Java 드라이버](https://mongodb.github.io/mongo-java-driver/)를 사용하는 경우 `MongoDatabase` 개체에서 `runCommand` 메서드를 호출하여 명령을 실행할 수 있습니다.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

자세한 내용은 [빠른 시작: MONGODB API를 사용 Azure Cosmos DB 하 여 웹 앱 빌드 및 JAVA SDK](create-mongodb-java.md)를 참조 하세요.

## <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js 드라이버 사용

[공식 MongoDB Node.js 드라이버](https://mongodb.github.io/node-mongodb-native/)를 사용하는 경우 `db` 개체에서 `command` 메서드를 호출하여 명령을 실행할 수 있습니다.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

자세한 내용은 [빠른 시작: 기존 MongoDB Node.js 웹 앱을 Azure Cosmos DB로 마이그레이션](create-mongodb-nodejs.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

RU 사용량을 최적화하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](./optimize-cost-reads-writes.md)