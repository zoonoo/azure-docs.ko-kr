---
title: Azure Cosmos DB에서 Gremlin API 쿼리에 대한 RU(요청 단위) 요금 찾기
description: Azure Cosmos 컨테이너에 대해 실행되는 Gremlin 쿼리에 대한 RU(요청 단위) 요금을 찾는 방법에 대해 알아봅니다. Azure Portal, .NET, Java 드라이버를 사용하여 RU 요금을 찾을 수 있습니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201351"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API에서 실행되는 작업에 대한 요청 단위 요금을 찾습니다.
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업 비용은 Azure Cosmos DB에서 정규화되고 RU(요청 단위)를 기준으로 표시됩니다. 요청 요금은 모든 데이터베이스 작업에서 사용되는 요청 단위입니다. RU는 Azure Cosmos DB에서 지원하는 데이터베이스 작업을 수행하는 데 필요한 CPU, IOPS, 메모리와 같은 시스템 리소스를 추상화하는 성능 통화로 생각할 수 있습니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리든 간에 비용은 항상 RU로 측정됩니다. 자세한 내용은 [요청 단위 및 고려 사항](request-units.md) 문서를 참조하세요.

이 문서에서는 Azure Cosmos DB Gremlin API의 컨테이너에 대해 실행한 작업의 [RU(요청 단위)](request-units.md) 사용량을 알아보는 다양한 방법을 설명합니다. 다른 API를 사용하는 경우 초당 RU 요금은 [MongoDB용 API](find-request-unit-charge-mongodb.md), [Cassandra API](find-request-unit-charge-cassandra.md), [SQL API](find-request-unit-charge.md), [Table API](find-request-unit-charge-table.md) 문서를 참조하세요.

Gremlin API에서 반환되는 헤더는 현재 Gremlin .NET 및 Java SDK에서 표시하는 사용자 지정 상태 특성에 매핑됩니다. 요청 요금은 `x-ms-request-charge` 키 아래에서 사용할 수 있습니다. Gremlin API를 사용하는 경우 Azure Cosmos 컨테이너에 대해 실행한 작업의 RU 사용량을 알아보는 여러 옵션이 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들고](create-graph-gremlin-console.md#create-a-database-account) 데이터를 입력하거나, 이미 데이터가 들어 있는 기존 계정을 선택합니다.

1. **Data Explorer** 창으로 이동한 다음, 작업할 컨테이너를 선택합니다.

1. 유효한 쿼리를 입력한 다음 **Gremlin 쿼리 실행** 을 선택합니다.

1. **쿼리 통계** 를 선택하여 방금 실행한 요청의 실제 요청 요금을 표시합니다.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Azure Portal의 Gremlin 쿼리 요청 요금 스크린샷":::

## <a name="use-the-net-sdk-driver"></a>.NET SDK 드라이버 사용

[Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/)를 사용하는 경우 `ResultSet<>` 개체의 `StatusAttributes` 속성 아래에서 상태 특성을 사용할 수 있습니다.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

자세한 내용은 [빠른 시작: Azure Cosmos DB Gremlin API 계정을 사용하여 .NET Framework 또는 Core 애플리케이션 빌드](create-graph-dotnet.md)를 참조하세요.

## <a name="use-the-java-sdk-driver"></a>Java SDK 드라이버 사용

[Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)를 사용하는 경우 `ResultSet` 개체의 `statusAttributes()` 메서드를 호출하여 상태 특성을 검색할 수 있습니다.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

자세한 내용은 [빠른 시작: JAVA SDK를 사용하여 Azure Cosmos DB에서 그래프 데이터베이스 만들기](create-graph-java.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

RU 사용량을 최적화하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](./optimize-cost-reads-writes.md)