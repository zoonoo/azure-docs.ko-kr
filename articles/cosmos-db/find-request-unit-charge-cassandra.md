---
title: Azure Cosmos DB에서 Cassandra API 쿼리의 요청 단위 (r) 요금을 찾습니다.
description: Azure Cosmos 컨테이너에 대해 실행 되는 Cassandra 쿼리에 대 한 (요청 단위) 요금을 찾는 방법에 대해 알아봅니다. Azure Portal, .NET 및 Java 드라이버를 사용 하 여이에 대 한 비용을 찾을 수 있습니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: a0451e76ad8ce65486534bfbc68ae1b71adc9098
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284437"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB에서 실행 된 작업에 대 한 요청 단위 요금을 찾습니다 Cassandra API

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업 비용은 Azure Cosmos DB에서 정규화되고 RU(요청 단위)를 기준으로 표시됩니다. RUs는 Azure Cosmos DB에서 지 원하는 데이터베이스 작업을 수행 하는 데 필요한 CPU, IOPS 및 메모리와 같은 시스템 리소스를 추상화 하는 성능 통화로 간주할 수 있습니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리 인지에 상관 없이 비용은 항상 RUs로 측정 됩니다. 자세히 알아보려면 [요청 단위 및 it 고려 사항](request-units.md) 문서를 참조 하세요.

이 문서에서는 Azure Cosmos DB Cassandra API의 컨테이너에 대해 실행 된 모든 작업에 대해 작업을 수행 [하는 데 사용할 수 있는 여러](request-units.md) 가지 방법을 제공 합니다. 다른 API를 사용 하는 경우 MongoDB, [SQL api](find-request-unit-charge.md), [Gremlin api](find-request-unit-charge-gremlin.md)및 [Table API](find-request-unit-charge-table.md) 문서 [에 대 한 api](find-request-unit-charge-mongodb.md)를 참조 하 여 r u/초 요금을 찾으십시오.

Azure Cosmos DB Cassandra API에 대해 작업을 수행하는 경우 RU 요금은 수신 페이로드에 `RequestCharge`라는 필드로 반환됩니다. 여러 가지 방법으로 RU 요금을 검색할 수 있습니다.

## <a name="use-the-net-sdk"></a>.NET SDK 사용

[.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)를 사용하는 경우 `RowSet` 개체의 `Info` 속성 아래에서 수신 페이로드를 검색할 수 있습니다.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

자세한 내용은 [빠른 시작: .NET SDK를 사용 하 여 Cassandra 앱 빌드 및 Azure Cosmos DB](create-cassandra-dotnet.md)을 참조 하세요.

## <a name="use-the-java-sdk"></a>Java SDK 사용

[Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)를 사용하는 경우 `ResultSet` 개체에서 `getExecutionInfo()` 메서드를 호출하여 수신 페이로드를 검색할 수 있습니다.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

자세한 내용은 [빠른 시작: JAVA SDK를 사용 하 여 Cassandra 앱 빌드 및 Azure Cosmos DB](create-cassandra-java.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

RU 사용량을 최적화하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](optimize-cost-queries.md)