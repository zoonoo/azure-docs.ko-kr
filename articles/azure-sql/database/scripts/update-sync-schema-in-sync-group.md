---
title: 'PowerShell: SQL 데이터 동기화의 동기화 스키마 업데이트'
description: SQL 데이터 동기화에 대한 동기화 스키마를 업데이트하기 위한 Azure PowerShell 예제 스크립트
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792721"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>PowerShell을 사용하여 기존 동기화 그룹의 동기화 스키마 업데이트
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 Azure PowerShell 예제에서는 기존 SQL 데이터 동기화의 동기화 그룹에 있는 동기화 스키마를 업데이트합니다. 여러 테이블을 동기화하는 경우 이 스크립트는 동기화 스키마를 효과적으로 업데이트하는 데 도움이 됩니다. 이 예제는 GitHub에서 [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1)으로 사용할 수 있는 **UpdateSyncSchema** 스크립트의 사용을 보여 줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Az PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

SQL 데이터 동기화에 대한 개요는 [Azure SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](../sql-data-sync-data-sql-server-sql-database.md)를 참조하세요.

> [!IMPORTANT]
> SQL 데이터 동기화는 현재 Azure SQL Managed Instance를 지원하지 않습니다.

## <a name="examples"></a>예제

### <a name="add-all-tables-to-the-sync-schema"></a>동기화 스키마에 모든 테이블 추가

다음 예제에서는 데이터베이스 스키마를 새로 고치고 허브 데이터베이스의 모든 유효한 테이블을 동기화 스키마에 추가합니다.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>테이블과 열 추가 및 제거

다음 예제에서는 동기화 스키마에 `[dbo].[Table1]` 및 `[dbo].[Table2].[Column1]`을 추가하고 `[dbo].[Table3]`를 제거합니다.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>스크립트 매개 변수

**UpdateSyncSchema** 스크립트에는 다음 매개 변수가 있습니다.

| 매개 변수 | 메모 |
|---|---|
| $subscriptionId | 동기화 그룹이 생성되는 구독입니다. |
| $resourceGroupName | 동기화 그룹이 생성되는 리소스 그룹입니다.|
| $serverName | 허브 데이터베이스의 서버 이름입니다.|
| $databaseName | 허브 데이터베이스 이름입니다. |
| $syncGroupName | 동기화 그룹 이름입니다. |
| $memberName | 허브 데이터베이스 대신, 동기화 멤버에서 데이터베이스 스키마를 로드하려는 경우 멤버 이름을 지정합니다. 허브에서 데이터베이스 스키마를 로드하려는 경우에는 이 매개 변수를 비워 둡니다. |
| $timeoutInSeconds | 스크립트가 데이터베이스 스키마를 새로 고치는 시간 제한입니다. 기본값은 900초입니다. |
| $refreshDatabaseSchema | 스크립트가 데이터베이스 스키마를 새로 고쳐야 할지 여부를 지정합니다. 데이터베이스 스키마가 이전 구성에서 변경된 경우(예를 들어 새 테이블이나 새 열을 추가한 경우) 다시 구성하기 전에 스키마를 새로 고쳐야 합니다. 기본값은 false입니다. |
| $addAllTables | 이 값이 true이면 모든 유효한 테이블 및 열이 동기화 스키마에 추가됩니다. 값 $TablesAndColumnsToAdd 및 $TablesAndColumnsToRemove는 무시됩니다. |
| $tablesAndColumnsToAdd | 동기화 스키마에 추가할 테이블 또는 열을 지정합니다. 각 테이블 또는 열 이름은 스키마 이름과 완전히 구분되어야 합니다. 예: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. 여러 테이블 또는 열 이름은 쉼표(,)로 구분해서 지정할 수 있습니다. |
| $tablesAndColumnsToRemove | 동기화 스키마에서 제거할 테이블 또는 열을 지정합니다. 각 테이블 또는 열 이름은 스키마 이름과 완전히 구분되어야 합니다. 예: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. 여러 테이블 또는 열 이름은 쉼표(,)로 구분해서 지정할 수 있습니다. |

## <a name="script-explanation"></a>스크립트 설명

**UpdateSyncSchema** 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | 동기화 그룹에 대한 정보를 반환합니다. |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | 동기화 그룹을 업데이트합니다. |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | 동기화 구성원에 대한 정보를 반환합니다. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | 동기화 스키마에 대한 정보를 반환합니다. |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | 동기화 스키마를 업데이트합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../powershell-script-content-guide.md)에 있습니다.

SQL 데이터 동기화에 대한 자세한 내용은 다음 항목을 참조하세요.

- 개요 - [Azure SQL Database 및 SQL Server와 Azure의 SQL 데이터 동기화 간의 동기화 데이터](../sql-data-sync-data-sql-server-sql-database.md)
- 데이터 동기화 설정
    - Azure Portal 사용 - [자습서: Azure SQL Database와 SQL Server 간에 데이터를 동기화하도록 SQL 데이터 동기화 설정](../sql-data-sync-sql-server-configure.md)
    - PowerShell 사용
        -  [PowerShell을 사용하여 Azure SQL Database의 여러 데이터베이스 간에 데이터 동기화](sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell을 사용하여 Azure SQL Database와 SQL Server 간에 데이터 동기화](sql-data-sync-sync-data-between-azure-onprem.md)
- 데이터 동기화 에이전트 - [Azure의 SQL 데이터 동기화용 데이터 동기화 에이전트](../sql-data-sync-agent-overview.md)
- 모범 사례 - [Azure의 SQL 데이터 동기화에 대한 모범 사례](../sql-data-sync-best-practices.md)
- 모니터 - [Azure Monitor 로그를 사용하여 SQL 데이터 동기화 모니터링](../monitor-tune-overview.md)
- 문제 해결 - [Azure의 SQL 데이터 동기화 문제 해결](../sql-data-sync-troubleshoot.md)
- 동기화 스키마 업데이트
    - Transact-SQL 사용 - [Azure의 SQL 데이터 동기화에서 스키마 변경 내용 복제 자동화](../sql-data-sync-update-sync-schema.md)

SQL Database에 대한 자세한 내용은 다음 항목을 참조하세요.

- [SQL Database 개요](../sql-database-paas-overview.md)
- [데이터베이스 수명 주기 관리](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))