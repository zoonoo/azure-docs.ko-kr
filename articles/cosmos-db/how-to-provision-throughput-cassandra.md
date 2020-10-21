---
title: Azure Cosmos DB Cassandra API 리소스에 대 한 처리량 프로 비전
description: Azure Cosmos DB Cassandra API 리소스에서 컨테이너, 데이터베이스 및 자동 크기 조정 처리량을 프로 비전 하는 방법에 대해 알아봅니다. Azure Portal, CLI, PowerShell 및 기타 다양 한 Sdk를 사용 합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 6b8b145c919a1f4e2ea9129a032da69bd30e6b71
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284120"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Azure Cosmos DB Cassandra API 리소스에 대 한 데이터베이스, 컨테이너 또는 자동 크기 조정 처리량 프로 비전

이 문서에서는 Azure Cosmos DB Cassandra API에서 처리량을 프로 비전 하는 방법을 설명 합니다. 컨테이너 또는 데이터베이스에 표준 (수동) 또는 자동 크기 조정 처리량을 프로 비전 하 고 데이터베이스 내의 컨테이너 간에 공유할 수 있습니다. Azure Portal, Azure CLI 또는 Azure Cosmos DB Sdk를 사용 하 여 처리량을 프로 비전 할 수 있습니다.

다른 API를 사용 하는 경우 [SQL api](how-to-provision-container-throughput.md), [MongoDB API](how-to-provision-throughput-mongodb.md), [Gremlin api](how-to-provision-throughput-gremlin.md) 문서를 참조 하 여 처리량을 프로 비전 합니다.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure Portal

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-mongodb-dotnet.md#create-a-database-account) 기존 Azure Cosmos 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 테이블**을 선택 합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 keyspace를 만들지, 아니면 기존 항목을 사용 하는지 여부를 나타냅니다. Keyspace 수준에서 처리량을 프로 비전 하려면 **데이터베이스 처리량 프로 비전** 옵션을 선택 합니다.
   * CQL 명령 내에 테이블 ID를 입력 합니다.
   * 기본 키 값 (예:)을 입력 `/userrID` 합니다.
   * 프로비저닝하려는 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="데이터베이스 수준 처리량을 사용 하 여 새 컬렉션을 만들 때 데이터 탐색기의 스크린샷":::

> [!Note]
> Cassandra API를 사용하여 구성한 Azure Cosmos 계정의 컨테이너에 처리량을 프로비저닝하는 경우 파티션 키 경로로 `/myPrimaryKey`를 사용합니다.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Cassandra 테이블에 대 한 처리량 프로 비전

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
CQL 규격 드라이버를 통해 비슷한 명령을 실행할 수 있습니다.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Cassandra 테이블에 대 한 처리량 변경 또는 변경

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

Azure Resource Manager 템플릿을 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 예제는 [Azure Cosmos DB Azure Resource Manager 템플릿을](templates-samples-cassandra.md) 참조 하세요.

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 샘플은 [Azure Cosmos DB Azure CLI 샘플](cli-samples-cassandra.md)을 참조 하세요.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell를 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 샘플은 [Azure Cosmos DB Azure PowerShell 샘플](powershell-samples-cassandra.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Azure Cosmos DB에서 처리량을 프로비저닝하는 방법을 알아보세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)