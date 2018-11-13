---
title: Azure Cosmos DB에서 데이터베이스 처리량 프로비전
description: Azure Cosmos DB의 데이터베이스 수준에서 처리량을 프로비전하는 방법 알아보기
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4eba0381eb302ca4400fb5669b486fb3ad337993
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262525"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터베이스에 대한 처리량 프로비전

이 문서에서는 Azure Cosmos DB의 데이터베이스에 대한 처리량을 프로비전하는 방법을 설명합니다. 단일 [컨테이너](how-to-provision-container-throughput.md) 또는 데이터베이스의 처리량을 프로비전하고 그 안에 있는 컨테이너 간에 처리량을 공유할 수 있습니다. Azure Portal 또는 Cosmos DB SDK를 사용하여 데이터베이스 수준 처리량을 프로비전할 수 있습니다.

## <a name="provision-throughput-using-azure-portal"></a>Azure Portal을 사용하여 처리량 프로비전

### <a id="portal-sql"></a>SQL(Core) API

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. [새 Cosmos DB 계정을 만들거나](create-sql-api-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 데이터베이스**를 선택합니다. 다음 세부 정보로 양식을 채웁니다.

   * 데이터베이스 ID를 입력합니다. 
   * [처리량 프로비전]을 선택합니다.
   * 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

![SQL API 데이터베이스 처리량 프로비전](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>.NET SDK를 사용하여 처리량 프로비전

> [!Note]
> 모든 API의 처리량을 프로비전하려면 SQL API를 사용합니다. 필요에 따라 Cassandra API에도 아래 예제를 사용할 수 있습니다.

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

다음 문서를 참조하여 Cosmos DB에서 처리량을 프로비전하는 방법을 알아보세요.

* [컨테이너의 처리량을 프로비전하는 방법](how-to-provision-container-throughput.md)
* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)
