---
title: Azure Cosmos DB Cassandra API 리소스를 나열하고 가져오는 PowerShell 스크립트
description: Azure PowerShell 스크립트 - Cassandra API용 Azure Cosmos DB 작업 나열 및 가져오기
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 8cd9abf02d66c6bed5720129d0b19c9a7545fac7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488319"
---
# <a name="list-and-get-keyspaces-and-tables-for-azure-cosmos-db---cassandra-api"></a>Azure Cosmos DB - Cassandra API용 키스페이스, 테이블 나열 및 가져오기

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-list-get.ps1 "List or get keyspace or table for Cassandra API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Cosmos DB 계정을 나열하거나 지정된 Cosmos DB 계정을 가져옵니다. |
| [Get-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspace) | 계정의 Cosmos DB Cassandra API 키스페이스를 나열하거나 계정에 지정된 Cosmos DB Cassandra API 키스페이스를 가져옵니다. |
| [Get-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratable) | 키스페이스의 Cosmos DB Cassandra API 테이블을 나열하거나 키스페이스에 지정된 Cosmos DB Cassandra API 테이블을 가져옵니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.