---
title: 'PowerShell: SQL Database 및 SQL Server 간 데이터 동기화'
description: Azure SQL Database와 SQL Server 간에 데이터 동기화를 수행하는 Azure PowerShell 예제 스크립트를 사용합니다.
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
ms.openlocfilehash: 194c2466e29c15ac35cf0bd88b90bf9f816e9035
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284468"
---
# <a name="use-powershell-to-sync-data-between-sql-database-and-sql-server"></a>PowerShell을 사용하여 SQL Database와 SQL Server 간에 데이터 동기화

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 Azure PowerShell 예제는 Azure SQL Database와 SQL Server 간에 데이터 동기화를 수행하도록 데이터 동기화를 구성합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Az PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

SQL 데이터 동기화에 대한 개요는 [Azure의 SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](../sql-data-sync-data-sql-server-sql-database.md)를 참조하세요.

> [!IMPORTANT]
> SQL 데이터 동기화는 현재 Azure SQL Managed Instance를 지원하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- AdventureWorksLT 샘플 데이터베이스의 Azure SQL Database에 있는 데이터베이스를 허브 데이터베이스로 만듭니다.
- 동일한 지역의 Azure SQL Database에 있는 데이터베이스를 동기화 데이터베이스로 만듭니다.
- SQL Server 인스턴스에 있는 데이터베이스를 구성원 데이터베이스로 만듭니다.
- 예제를 실행하기 전에 매개 변수 자리 표시자를 업데이트합니다.

## <a name="example"></a>예제

```powershell-interactive
using namespace Microsoft.Azure.Commands.Sql.DataSync.Model
using namespace System.Collections.Generic

# hub database info
$subscriptionId = "<subscriptionId>"
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$databaseName = "<databaseName>"

# sync database info
$syncDatabaseResourceGroupName = "<syncResourceGroupName>"
$syncDatabaseServerName = "<syncServerName>"
$syncDatabaseName = "<syncDatabaseName>"

# sync group info
$syncGroupName = "<syncGroupName>"
$conflictResolutionPolicy = "HubWin" # can be HubWin or MemberWin
$intervalInSeconds = 300 # sync interval in seconds (must be no less than 300)

# member database info
$syncMemberName = "<syncMemberName>"
$memberServerName = "<memberServerName>"
$memberDatabaseName = "<memberDatabaseName>"
$memberDatabaseType = "SqlServerDatabase" # can be AzureSqlDatabase or SqlServerDatabase
$syncDirection = "Bidirectional" # can be Bidirectional, Onewaymembertohub, Onewayhubtomember

# sync agent info
$syncAgentName = "<agentName>"
$syncAgentResourceGroupName = "<syncAgentResourceGroupName>"
$syncAgentServerName = "<syncAgentServerName>"

# temp file to save the sync schema
$tempFile = $env:TEMP+"\syncSchema.json"

# list of included columns and tables in quoted name
$includedColumnsAndTables =  "[SalesLT].[Address].[AddressID]",
                             "[SalesLT].[Address].[AddressLine2]",
                             "[SalesLT].[Address].[rowguid]",
                             "[SalesLT].[Address].[PostalCode]",
                             "[SalesLT].[ProductDescription]"
$metadataList = [System.Collections.ArrayList]::new($includedColumnsAndTables)

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

# use if it's safe to show password in script, otherwise use PromptForCredential
# $user = "username"
# $password = ConvertTo-SecureString -String "password" -AsPlainText -Force
# $credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user, $password

$credential = $Host.ui.PromptForCredential("Need credential",
              "Please enter your user name and password for server "+$serverName+".database.windows.net",
              "",
              "")

# create a new sync agent
Write-Host "Creating new Sync Agent..."
New-AzSqlSyncAgent -ResourceGroupName $resourceGroupName -ServerName  $serverName -SyncDatabaseName $syncDatabaseName -SyncAgentName $syncAgentName

# generate agent key
Write-Host "Generating Agent Key..."
$agentKey = New-AzSqlSyncAgentKey -ResourceGroupName $resourceGroupName -ServerName $serverName -SyncAgentName $syncAgentName
Write-Host "Use your agent key to configure the sync agent. Do this before proceeding."
$agentkey

# DO THE FOLLOWING BEFORE THE NEXT STEP
# Install the on-premises sync agent on your machine and register the sync agent using the agent key generated above to bring the sync agent online.
# Add the SQL Server database information including server name, database name, user name, password on the configuration tool within the sync agent.  

# create a new sync group
Write-Host "Creating Sync Group "$syncGroupName"..."
New-AzSqlSyncGroup -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Name $syncGroupName `
    -SyncDatabaseName $syncDatabaseName -SyncDatabaseServerName $syncDatabaseServerName -SyncDatabaseResourceGroupName $syncDatabaseResourceGroupName `
    -ConflictResolutionPolicy $conflictResolutionPolicy -DatabaseCredential $credential

# use if it's safe to show password in script, otherwise use PromptForCredential
#$user = "username"
#$password = ConvertTo-SecureString -String "password" -AsPlainText -Force
#$credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user, $password

$credential = $Host.ui.PromptForCredential("Need credential",
              "Please enter your user name and password for server "+$memberServerName,
              "",
              "")

# get information from sync agent and confirm your SQL Server instance was configured (note the database ID to use for the sqlServerDatabaseID in the next step)
$syncAgentInfo = Get-AzSqlSyncAgentLinkedDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -SyncAgentName $syncAgentName

# add a new sync member
Write-Host "Adding member"$syncMemberName" to the sync group..."

New-AzSqlSyncMember -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
    -SyncGroupName $syncGroupName -Name $syncMemberName -MemberDatabaseType $memberDatabaseType -SyncAgentResourceGroupName $syncAgentResourceGroupName `
    -SyncAgentServerName $syncAgentServerName -SyncAgentName $syncAgentName  -SyncDirection $syncDirection -SqlServerDatabaseID  $syncAgentInfo.DatabaseId

# refresh database schema from hub database, specify the -SyncMemberName parameter if you want to refresh schema from the member database
Write-Host "Refreshing database schema from hub database..."
$startTime = Get-Date
Update-AzSqlSyncSchema -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -SyncGroupName $syncGroupName

# waiting for successful refresh
$startTime = $startTime.ToUniversalTime()
$timer=0
$timeout=90

# check the log and see if refresh has gone through
Write-Host "Check for successful refresh..."
$isSucceeded = $false
while ($isSucceeded -eq $false) {
    Start-Sleep -s 10
    $timer=$timer+10
    $details = Get-AzSqlSyncSchema -SyncGroupName $syncGroupName -ServerName $serverName -DatabaseName $databaseName -ResourceGroupName $resourceGroupName
    if ($details.LastUpdateTime -gt $startTime) {
        Write-Host "Refresh was successful"
        $isSucceeded = $true
    }
    if ($timer -eq $timeout) {
        Write-Host "Refresh timed out"
        break;
    }
}

# get the database schema
Write-Host "Adding tables and columns to the sync schema..."
$databaseSchema = Get-AzSqlSyncSchema -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
    -DatabaseName $DatabaseName -SyncGroupName $SyncGroupName `

$databaseSchema | ConvertTo-Json -depth 5 -Compress | Out-File "C:\Users\OnPremiseServer\AppData\Local\Temp\syncSchema.json"

$newSchema = [AzureSqlSyncGroupSchemaModel]::new()
$newSchema.Tables = [List[AzureSqlSyncGroupSchemaTableModel]]::new();

# add columns and tables to the sync schema
foreach ($tableSchema in $databaseSchema.Tables) {
    $newTableSchema = [AzureSqlSyncGroupSchemaTableModel]::new()
    $newTableSchema.QuotedName = $tableSchema.QuotedName
    $newTableSchema.Columns = [List[AzureSqlSyncGroupSchemaColumnModel]]::new();
    $addAllColumns = $false
    if ($MetadataList.Contains($tableSchema.QuotedName)) {
        if ($tableSchema.HasError) {
            $fullTableName = $tableSchema.QuotedName
            Write-Host "Can't add table $fullTableName to the sync schema" -foregroundcolor "Red"
            Write-Host $tableSchema.ErrorId -foregroundcolor "Red"
            continue;
        }
        else {
            $addAllColumns = $true
        }
    }
    foreach($columnSchema in $tableSchema.Columns) {
        $fullColumnName = $tableSchema.QuotedName + "." + $columnSchema.QuotedName
        if ($addAllColumns -or $MetadataList.Contains($fullColumnName)) {
            if ((-not $addAllColumns) -and $tableSchema.HasError) {
                Write-Host "Can't add column $fullColumnName to the sync schema" -foregroundcolor "Red"
                Write-Host $tableSchema.ErrorId -foregroundcolor "Red"
            }
            elseif ((-not $addAllColumns) -and $columnSchema.HasError) {
                Write-Host "Can't add column $fullColumnName to the sync schema" -foregroundcolor "Red"
                Write-Host $columnSchema.ErrorId -foregroundcolor "Red"
            }
            else {
                Write-Host "Adding"$fullColumnName" to the sync schema"
                $newColumnSchema = [AzureSqlSyncGroupSchemaColumnModel]::new()
                $newColumnSchema.QuotedName = $columnSchema.QuotedName
                $newColumnSchema.DataSize = $columnSchema.DataSize
                $newColumnSchema.DataType = $columnSchema.DataType
                $newTableSchema.Columns.Add($newColumnSchema)
            }
        }
    }
    if ($newTableSchema.Columns.Count -gt 0) {
        $newSchema.Tables.Add($newTableSchema)
    }
}

# convert sync schema to JSON format
$schemaString = $newSchema | ConvertTo-Json -depth 5 -Compress

# workaround a powershell bug
$schemaString = $schemaString.Replace('"Tables"', '"tables"').Replace('"Columns"', '"columns"').Replace('"QuotedName"', '"quotedName"').Replace('"MasterSyncMemberName"','"masterSyncMemberName"')

# save the sync schema to a temp file
$schemaString | Out-File $tempFile

# update sync schema
Write-Host "Updating the sync schema..."
Update-AzSqlSyncGroup -ResourceGroupName $resourceGroupName -ServerName $serverName `
    -DatabaseName $databaseName -Name $syncGroupName -Schema $tempFile

$syncLogStartTime = Get-Date

# trigger sync manually
Write-Host "Trigger sync manually..."
Start-AzSqlSyncGroupSync -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -SyncGroupName $syncGroupName

# check the sync log and wait until the first sync succeeded
Write-Host "Check the sync log..."
$isSucceeded = $false
for ($i = 0; ($i -lt 300) -and (-not $isSucceeded); $i = $i + 10) {
    Start-Sleep -s 10
    $syncLogEndTime = Get-Date
    $syncLogList = Get-AzSqlSyncGroupLog -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -SyncGroupName $syncGroupName -StartTime $syncLogStartTime.ToUniversalTime() -EndTime $syncLogEndTime.ToUniversalTime()

    if ($synclogList.Length -gt 0) {
        foreach ($syncLog in $syncLogList) {
            if ($syncLog.Details.Contains("Sync completed successfully")) {
                Write-Host $syncLog.TimeStamp : $syncLog.Details
                $isSucceeded = $true
            }
        }
    }
}

if ($isSucceeded) {
    # enable scheduled sync
    Write-Host "Enable the scheduled sync with 300 seconds interval..."
    Update-AzSqlSyncGroup  -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -Name $syncGroupName -IntervalInSeconds $intervalInSeconds
}
else {
    # output all log if sync doesn't succeed in 300 seconds
    $syncLogEndTime = Get-Date
    $syncLogList = Get-AzSqlSyncGroupLog  -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -SyncGroupName $syncGroupName -StartTime $syncLogStartTime.ToUniversalTime() -EndTime $syncLogEndTime.ToUniversalTime()

    if ($synclogList.Length -gt 0) {
        foreach ($syncLog in $syncLogList) {
            Write-Host $syncLog.TimeStamp : $syncLog.Details
        }
    }
}
```

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트를 실행한 후에는 다음 명령을 실행하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Remove-AzResourceGroup -ResourceGroupName $syncDatabaseResourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzSqlSyncAgent](/powershell/module/az.sql/New-azSqlSyncAgent) |  새 동기화 에이전트를 만듭니다. |
| [New-AzSqlSyncAgentKey](/powershell/module/az.sql/New-azSqlSyncAgentKey) |  동기화 에이전트와 연결된 에이전트 키를 생성합니다. |
| [Get-AzSqlSyncAgentLinkedDatabase](/powershell/module/az.sql/Get-azSqlSyncAgentLinkedDatabase) |  동기화 에이전트에 대한 모든 정보를 가져옵니다. |
| [New-AzSqlSyncMember](/powershell/module/az.sql/New-azSqlSyncMember) |  동기화 그룹에 새 멤버를 추가합니다. |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/Update-azSqlSyncSchema) |  데이터베이스 스키마 정보를 새로 고칩니다. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/Get-azSqlSyncSchema) |  데이터베이스 스키마 정보를 가져옵니다. |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/Update-azSqlSyncGroup) |  동기화 그룹을 업데이트합니다. |
| [Start-AzSqlSyncGroupSync](/powershell/module/az.sql/Start-azSqlSyncGroupSync) | 동기화를 트리거합니다. |
| [Get-AzSqlSyncGroupLog](/powershell/module/az.sql/Get-azSqlSyncGroupLog) |  동기화 로그를 확인합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../powershell-script-content-guide.md)에 있습니다.

SQL 데이터 동기화에 대한 자세한 내용은 다음 항목을 참조하세요.

- 개요 - [Azure SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](../sql-data-sync-data-sql-server-sql-database.md)
- 데이터 동기화 설정
    - Azure Portal 사용 - [자습서: Azure SQL Database와 SQL Server 온-프레미스 간에 데이터를 동기화하도록 SQL 데이터 동기화 설정](../sql-data-sync-sql-server-configure.md)
    - PowerShell 사용 - [PowerShell을 사용하여 Azure SQL Database의 여러 데이터베이스 간에 동기화](sql-data-sync-sync-data-between-sql-databases.md)
- 데이터 동기화 에이전트 - [Azure의 SQL 데이터 동기화용 데이터 동기화 에이전트](../sql-data-sync-agent-overview.md)
- 모범 사례 - [Azure의 SQL 데이터 동기화에 대한 모범 사례](../sql-data-sync-best-practices.md)
- 모니터 - [Azure Monitor 로그를 사용하여 SQL 데이터 동기화 모니터링](../sql-data-sync-monitor-sync.md)
- 문제 해결 - [Azure의 SQL 데이터 동기화 문제 해결](../sql-data-sync-troubleshoot.md)
- 동기화 스키마 업데이트
    - Transact-SQL 사용 - [Azure의 SQL 데이터 동기화에서 스키마 변경 내용 복제 자동화](../sql-data-sync-update-sync-schema.md)
    - PowerShell 사용 - [PowerShell을 사용하여 기존 동기화 그룹의 동기화 스키마 업데이트](update-sync-schema-in-sync-group.md)

Azure SQL Database에 대한 자세한 내용은 다음 항목을 참조하세요.

- [SQL Database 개요](../sql-database-paas-overview.md)
- [데이터베이스 수명 주기 관리](https://msdn.microsoft.com/library/jj907294.aspx)
