---
title: Azure Cosmos DB에 대한 Azure PowerShell 샘플
description: Azure PowerShell 샘플을 가져와 Azure Cosmos DB에 일반 작업 수행
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505017"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 Azure PowerShell 샘플

다음 표에는 Azure Cosmos DB에 일반적으로 사용되는 Azure PowerShell 스크립트의 링크가 포함되어 있습니다. 오른쪽의 링크를 사용하여 API 관련 샘플로 이동합니다. 일반적인 샘플은 모든 API에서 동일합니다. 모든 Azure Cosmos DB PowerShell cmdlet에 대한 참조 페이지는 [Azure PowerShell 참조](/powershell/module/az.cosmosdb)에 제공됩니다. `Az.CosmosDB`에 대한 업데이트를 정기적으로 확인하세요. [GitHub의 Cosmos DB PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)에서 GitHub 리포지토리의 Cosmos DB에 대한 PowerShell 샘플을 포크할 수도 있습니다.

## <a name="common-samples"></a>일반적인 샘플

|Task | 설명 |
|---|---|
|[계정 업데이트](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB 계정의 기본 일관성 수준을 업데이트합니다. |
|[계정 지역 업데이트](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB 계정의 지역을 업데이트합니다. |
|[장애 조치(failover) 우선순위 변경 또는 장애 조치(failover) 트리거](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정의 장애 조치(failover) 우선순위를 변경하거나 수동 장애 조치(failover)를 트리거합니다. |
|[계정 키 또는 연결 문자열](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 기본 및 보조 키, 연결 문자열을 가져오거나 Azure Cosmos DB 계정의 계정 키를 다시 생성합니다. |
|[IP 방화벽을 사용하여 Cosmos 계정 만들기](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 활성화된 IP 방화벽을 사용하여 Azure Cosmos DB 계정을 만듭니다. |
|||

## <a name="core-sql-api-samples"></a>Core(SQL) API 샘플

|Task | 설명 |
|---|---|
|[계정, 데이터베이스 및 컨테이너 만들기](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다. |
|[자동 스케일링되는 계정, 데이터베이스 및 컨테이너 만들기](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 자동 스케일링되는 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다. |
|[큰 파티션 키로 컨테이너 만들기](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 큰 파티션 키로 컨테이너를 만듭니다. |
|[인덱스 정책이 없는 컨테이너 만들기](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 인덱스 정책을 해제한 Azure Cosmos 컨테이너를 만듭니다.|
|[데이터베이스 또는 컨테이너 나열 또는 가져오기](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컨테이너를 나열하거나 가져옵니다. |
|[처리량 가져오기](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컨테이너의 처리량을 가져옵니다. |
|[처리량 업데이트](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컨테이너의 처리량을 업데이트합니다. |
|[삭제에서 리소스 잠그기](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 리소스 잠금을 사용하여 리소스를 삭제하지 않습니다. |
|||

## <a name="cassandra-api-samples"></a>Cassandra API 샘플

|Task | 설명 |
|---|---|
|[계정 Keyspace 및 테이블 만들기](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정, Keyspace 및 테이블을 만듭니다. |
|[자동 스케일링되는 계정, 키스페이스 및 테이블 만들기](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 자동 스케일링되는 Azure Cosmos 계정, 키스페이스 및 테이블을 만듭니다. |
|[Keyspace 또는 목록 나열 또는 가져오기](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Keyspace 또는 목록을 나열하거나 가져옵니다. |
|[처리량 가져오기](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 키스페이스 또는 테이블의 처리량을 가져옵니다. |
|[처리량 업데이트](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 키스페이스 또는 테이블의 처리량을 업데이트합니다. |
|[삭제에서 리소스 잠그기](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 리소스 잠금을 사용하여 리소스를 삭제하지 않습니다. |
|||

## <a name="mongo-db-api-samples"></a>Mongo DB API 샘플

|Task | 설명 |
|---|---|
|[계정, 데이터베이스 및 컬렉션 만들기](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정, 데이터베이스 및 컬렉션을 만듭니다. |
|[자동 스케일링되는 계정, 데이터베이스 및 컬렉션 만들기](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 자동 스케일링되는 Azure Cosmos 계정, 데이터베이스 및 컬렉션을 만듭니다. |
|[데이터베이스 또는 컬렉션 나열 또는 가져오기](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컬렉션을 나열하거나 가져옵니다. |
|[처리량 가져오기](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컬렉션의 처리량을 가져옵니다. |
|[처리량 업데이트](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컬렉션의 처리량을 업데이트합니다. |
|[삭제에서 리소스 잠그기](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 리소스 잠금을 사용하여 리소스를 삭제하지 않습니다. |
|||

## <a name="gremlin-api-samples"></a>Gremlin API 샘플

|Task | 설명 |
|---|---|
|[계정, 데이터베이스 및 그래프 만들기](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정, 데이터베이스 및 그래프를 만듭니다. |
|[자동 스케일링되는 계정, 데이터베이스 및 그래프 만들기](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 자동 스케일링되는 Azure Cosmos 계정, 데이터베이스 및 그래프를 만듭니다. |
|[데이터베이스 또는 그래프 나열 또는 가져오기](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 그래프를 나열하거나 가져옵니다. |
|[처리량 가져오기](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 그래프의 처리량을 가져옵니다. |
|[처리량 업데이트](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 그래프의 처리량을 업데이트합니다. |
|[삭제에서 리소스 잠그기](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 리소스 잠금을 사용하여 리소스를 삭제하지 않습니다. |
|||

## <a name="table-api-samples"></a>Table API 샘플

|Task | 설명 |
|---|---|
|[계정 및 테이블 만들기](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정 및 테이블을 만듭니다. |
|[자동 스케일링되는 계정 및 테이블 만들기](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 자동 스케일링되는 Azure Cosmos 계정 및 테이블을 만듭니다. |
|[테이블 나열 또는 가져오기](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 테이블을 나열하거나 가져옵니다. |
|[처리량 가져오기](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 테이블의 처리량을 가져옵니다. |
|[처리량 업데이트](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 테이블의 처리량을 업데이트합니다. |
|[삭제에서 리소스 잠그기](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 리소스 잠금을 사용하여 리소스를 삭제하지 않습니다. |
|||
