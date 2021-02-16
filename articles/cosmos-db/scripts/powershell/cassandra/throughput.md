---
title: Azure Cosmos DB Cassandra API 리소스에 대한 처리량(RU/s) 작업을 위한 PowerShell 스크립트
description: Azure Cosmos DB Cassandra API 리소스에 대한 처리량(RU/s) 작업을 위한 PowerShell 스크립트
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 72c35cba7c058928a7b087dd5f26f741e73a202c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "99821568"
---
# <a name="throughput-rus-operations-with-powershell-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Azure Cosmos DB - Cassandra API용 키스페이스 또는 테이블에 대해 PowerShell을 사용한 처리량(RU/s) 작업
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

이 샘플에는 Azure PowerShell Az 5.4.0 이상이 필요합니다. `Get-Module -ListAvailable Az`를 실행하여 설치된 버전을 확인합니다.
설치해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 실행하여 Azure에 로그인합니다.

## <a name="get-throughput"></a>처리량 가져오기

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-get.ps1 "Get throughput on a keyspace or table for Cassandra API")]

## <a name="update-throughput"></a>처리량 업데이트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-update.ps1 "Update throughput on a keyspace or table for Cassandra API")]

## <a name="migrate-throughput"></a>처리량 마이그레이션

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a keyspace or table for Cassandra API")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBCassandraKeyspaceThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspacethroughput) | Cassandra API 키스페이스의 처리량 값을 가져옵니다. |
| [Get-AzCosmosDBCassandraTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratablethroughput) | Cassandra API 테이블의 처리량 값을 가져옵니다. |
| [Update-AzCosmosDBCassandraKeyspaceThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbcassandrakeyspacethroughput) | Cassandra API 키스페이스의 처리량 값을 업데이트합니다. |
| [Update-AzCosmosDBCassandraTableThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbcassandratablethroughput) | Cassandra API 테이블의 처리량 값을 업데이트합니다. |
| [Invoke-AzCosmosDBCassandraKeyspaceThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandrakeyspacethroughputmigration) | Cassandra API 키스페이스에 대한 처리량을 마이그레이션합니다. |
| [Invoke-AzCosmosDBCassandraTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandratablethroughputmigration) | Cassandra API 테이블에 대한 처리량을 마이그레이션합니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.