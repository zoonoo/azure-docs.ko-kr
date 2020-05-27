---
title: Azure Cosmos DB에 대한 Azure PowerShell 샘플 - SQL(Core) API
description: Azure PowerShell 샘플을 가져와 Azure Cosmos DB SQL API 계정에서 다양한 일반 작업 수행
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 7cd14151d8cb0fbef7593d62eee050be7987ab18
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713542"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure Cosmos DB에 대한 Azure PowerShell 샘플 - SQL(Core) API

다음 표에는 SQL(Core)용 Azure Cosmos DB API를 위한 일반적으로 사용되는 Azure PowerShell 스크립트에 대한 링크가 포함되어 있습니다. GitHub 리포지토리에서 Cosmos DB에 대한 이러한 PowerShell 샘플을 포크하려면 [GitHub의 Cosmos DB PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)을 방문하세요.

SQL(Core) API 및 설명서에 대한 추가 Cosmos DB PowerShell 샘플은 [PowerShell을 사용하여 Azure Cosmos DB SQL API 리소스 관리](manage-with-powershell.md)를 참조하세요. 다른 API에 대한 Cosmos DB PowerShell 샘플은 [Cassandra API](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md) 및 [Table API](powershell-samples-table.md)를 참조하세요.

> [!NOTE]
> 이 샘플에서는 [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 관리 cmdlet을 사용합니다. `Az.CosmosDB`에 대한 업데이트를 정기적으로 확인하세요.

| | |
|---|---|
|[계정, 데이터베이스 및 컨테이너 만들기](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다. |
|[큰 파티션 키로 컨테이너 만들기](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 큰 파티션 키로 컨테이너를 만듭니다. |
|[데이터베이스 또는 컨테이너 나열 또는 가져오기](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컨테이너를 나열하거나 가져옵니다. |
|[RU/s 가져오기](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컨테이너에 대한 RU/s를 가져옵니다. |
|[RU/s 업데이트](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컨테이너에 대한 RU/s를 업데이트합니다. |
|[인덱스 정책이 없는 컨테이너 만들기](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 인덱스 정책을 해제한 Azure Cosmos 컨테이너를 만듭니다.|
|[계정 업데이트](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB 계정의 기본 일관성 수준을 업데이트합니다. |
|[계정 지역 업데이트](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB 계정의 지역을 업데이트합니다. |
|[장애 조치(failover) 우선순위 변경 또는 장애 조치(failover) 트리거](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정의 장애 조치(failover) 우선순위를 변경하거나 수동 장애 조치(failover)를 트리거합니다. |
|[계정 키 또는 연결 문자열](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 기본 및 보조 키, 연결 문자열을 가져오거나 Azure Cosmos DB 계정의 계정 키를 다시 생성합니다. |
|[IP 방화벽을 사용하여 Cosmos 계정 만들기](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 활성화된 IP 방화벽을 사용하여 Azure Cosmos DB 계정을 만듭니다. |
|||
