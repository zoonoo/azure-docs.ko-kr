---
title: Azure Cosmos DB Cassandra API 리소스에서 처리량 프로비저닝
description: Azure Cosmos DB Cassandra API 리소스에서 컨테이너, 데이터베이스를 프로비저닝하고 Azure Cosmos DB Cassandra API 리소스에서 처리량 크기를 자동으로 조정하는 방법을 알아봅니다. Azure Portal, CLI, PowerShell 및 기타 다양한 SDK를 사용합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 729765daed676c860f8a8258281d37d93961fa02
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528575"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Azure Cosmos DB Cassandra API 리소스에서 데이터베이스, 컨테이너를 프로비저닝하고 처리량 크기를 자동으로 조정
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

이 문서에서는 Azure Cosmos DB Cassandra API에서 처리량을 프로비저닝하는 방법을 설명합니다. 컨테이너 또는 데이터베이스의 표준(수동)이나 처리량 크기를 자동으로 조정하거나 데이터베이스 내부의 컨테이너와 처리량을 공유할 수 있습니다. Azure Portal, Azure CLI 또는 Azure Cosmos DB SDK를 사용하여 처리량을 프로비저닝할 수 있습니다.

다른 API를 사용하는 경우 [SQL API](../how-to-provision-container-throughput.md), [MongoDB용 API](../mongodb/how-to-provision-throughput-mongodb.md), [Gremlin API](../how-to-provision-throughput-gremlin.md) 문서를 참조하여 처리량을 프로비저닝합니다.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure Portal

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](../mongodb/create-mongodb-dotnet.md#create-a-database-account) 기존 Azure Cosmos 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 테이블** 을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 키스페이스를 만드는지 아니면 기존 키스페이스를 사용하는지 표시합니다. 키스페이스 수준에서 처리량을 프로비저닝하려면 **데이터베이스 처리량 프로비저닝** 옵션을 선택합니다.
   * CQL 명령 내에 테이블 ID를 입력합니다.
   * 기본 키 값을 입력합니다(예: `/userrID`).
   * 프로비저닝하려는 처리량을 입력합니다(예: 1000RU).
   * **확인** 을 선택합니다.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="데이터베이스 수준 처리량으로 새 컬렉션을 만드는 Data Explorer의 스크린샷":::

> [!Note]
> Cassandra API를 사용하여 구성한 Azure Cosmos 계정의 컨테이너에 처리량을 프로비저닝하는 경우 파티션 키 경로로 `/myPrimaryKey`를 사용합니다.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Cassandra 테이블의 프로비저닝 처리량

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
CQL 규격 드라이버를 통해 비슷한 명령을 실행할 수 있습니다.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Cassandra 테이블의 처리량 변경

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

CQL 규격 드라이버를 통해 비슷한 명령을 실행할 수 있습니다.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager 템플릿은 데이터베이스에서 자동 크기 조정 처리량을 프로비저닝하거나 모든 Azure Cosmos DB API에 대해 컨테이너 수준 리소스를 프로비저닝하는 데 사용될 수 있습니다. [Azure Cosmos DB용 Azure Resource Manager 템플릿](templates-samples.md)에서 샘플을 확인하십시오.

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 데이터베이스에서 자동 크기 조정 처리량을 프로비저닝하거나 모든 Azure Cosmos DB API에 대해 컨테이너 수준 리소스를 프로비저닝하는 데 사용될 수 있습니다. [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples.md)에서 샘플을 확인하십시오.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell은 데이터베이스에서 자동 크기 조정 처리량을 프로비저닝하거나 모든 Azure Cosmos DB API에 대해 컨테이너 수준 리소스를 프로비저닝하는 데 사용될 수 있습니다. [Azure Cosmos DB에 대한 Azure PowerShell 샘플](powershell-samples.md)에서 샘플을 확인하십시오.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Azure Cosmos DB에서 처리량을 프로비저닝하는 방법을 알아보세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](../request-units.md)