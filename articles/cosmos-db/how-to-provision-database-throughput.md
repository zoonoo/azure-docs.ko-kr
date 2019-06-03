---
title: Azure Cosmos DB에서 데이터베이스 처리량 프로비전
description: Azure Cosmos DB의 데이터베이스 수준에서 처리량을 프로비전하는 방법 알아보기
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: d73dd5ffe8cc8ed00288209b628d7175b795b335
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243766"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Azure Cosmos DB의 데이터베이스에 대한 처리량 프로비전

이 문서에서는 Azure Cosmos DB의 데이터베이스에 대한 처리량을 프로비저닝하는 방법을 설명합니다. 단일 [컨테이너](how-to-provision-container-throughput.md) 또는 데이터베이스의 처리량을 프로비전하고 그 안에 있는 컨테이너 간에 처리량을 공유할 수 있습니다. 컨테이너 수준 및 데이터베이스 수준 처리량을 언제 사용하는지 알아보려면 [컨테이너 및 데이터베이스의 처리량 프로비전 사용 사례](set-throughput.md) 문서를 참조하세요. Azure Portal 또는 Azure Cosmos DB SDK를 사용하여 데이터베이스 수준 처리량을 프로비저닝할 수 있습니다.

## <a name="provision-throughput-using-azure-portal"></a>Azure Portal을 사용하여 처리량 프로비전

### <a id="portal-sql"></a>SQL(Core) API

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-sql-api-dotnet.md#create-account) 기존 Azure Cosmos 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 데이터베이스**를 선택합니다. 다음과 같은 세부 정보를 제공합니다.

   * 데이터베이스 ID를 입력합니다. 
   * **처리량 프로비전**을 선택합니다.
   * 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

![새 데이터베이스 대화 상자 스크린샷](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>.NET SDK를 사용하여 처리량 프로비전

> [!Note]
> SQL API용 Cosmos SDK를 사용하여 모든 API의 처리량을 프로비저닝할 수 있습니다. 필요에 따라 Cassandra API에도 아래 예제를 사용할 수 있습니다.

### <a id="dotnet-all"></a>모든 API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 프로비저닝된 처리량에 대한 자세한 내용은 다음 문서를 참조하세요.

* [전역적으로 프로비저닝된 처리량 크기 조정](scaling-throughput.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [컨테이너의 처리량을 프로비전하는 방법](how-to-provision-container-throughput.md)
* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)