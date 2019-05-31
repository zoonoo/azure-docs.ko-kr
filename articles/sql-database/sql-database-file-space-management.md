---
title: Azure SQL Database 단일/풀링된 데이터베이스 파일 공간 관리 | Microsoft Docs
description: 이 페이지에서는 Azure SQL Database의 단일 및 풀링된 데이터베이스로 파일 공간을 관리하는 방법을 설명하고, 단일 및 풀링된 데이터베이스를 축소해야 할지 여부를 결정하는 방법은 물론 데이터베이스 축소 작업을 수행하는 방법에 대한 코드 샘플을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 96d55da713b8591b20f95ba36f332a340999181e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242746"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Azure SQL Database의 단일 및 풀링된 데이터베이스의 파일 공간 관리

이 문서에서는 Azure SQL Database의 단일 및 풀링된 데이터베이스에 대한 다양한 종류의 스토리지 공간을 설명하고 데이터베이스 및 탄력적 풀에 할당된 파일 공간을 명시적으로 관리해야 하는 경우 취할 수 있는 단계를 설명합니다.

> [!NOTE]
> 이 문서는 Azure SQL Database의 관리형 인스턴스 배포 옵션에는 적용되지 않습니다.

## <a name="overview"></a>개요

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL 데이터베이스에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

Azure SQL Database의 단일 및 풀링된 데이터베이스를 사용하면, 데이터베이스에 대한 기본 데이터 파일의 할당이 사용되는 데이터 페이지의 양을 초과할 수 있는 워크로드 패턴이 있습니다. 이 상태는 사용되는 공간이 증가하고 그 후에 데이터가 삭제되는 경우 발생할 수 있습니다. 이렇게 되는 이유는 데이터가 삭제될 때 할당되어 있는 파일 공간이 자동으로 회수되지 않기 때문입니다.

파일 공간 사용량 모니터링 및 데이터 파일 축소는 다음과 같은 시나리오에서 필요할 수 있습니다.

- 데이터베이스에 할당된 파일 공간이 풀 최대 크기에 도달하는 경우 탄력적 풀의 데이터 증가를 허용합니다.
- 단일 데이터베이스 또는 탄력적 풀의 최대 크기 감소를 허용합니다.
- 단일 데이터베이스 또는 탄력적 풀을 더 작은 최대 크기의 다른 서비스 계층 또는 성능 계층으로 변경하는 것을 허용합니다.

### <a name="monitoring-file-space-usage"></a>파일 공간 사용량 모니터링

Azure Portal 및 다음 API에 표시되는 대부분의 저장소 공간 메트릭은 사용한 데이터 페이지의 크기만 측정합니다.

- PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)를 포함한 Azure Resource Manager 기반 메트릭 API
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

그러나 다음 API는 데이터베이스 및 탄력적 풀에 할당된 공간의 크기도 측정합니다.

- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>데이터 파일 축소

SQL Database 서비스는 사용되지 않은 할당된 공간을 회수하기 위해 데이터 파일을 자동으로 축소하지 않습니다. 이렇게 하면 데이터베이스 성능에 잠재적인 영향을 미치기 때문입니다.  하지만 고객이 [사용되지 않은 할당된 공간 회수](#reclaim-unused-allocated-space)에 설명된 단계를 수행하기로 선택하면 셀프 서비스를 통해 데이터 파일을 축소할 수 있습니다.

> [!NOTE]
> 데이터 파일과 달리 SQL Database 서비스는 로그 파일을 자동으로 축소합니다. 이 작업은 데이터베이스 성능에 영향을 미치지 않기 때문입니다. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>데이터베이스의 저장소 공간 유형 이해

다음 저장소 공간 수량을 이해하는 것은 데이터베이스의 파일 공간을 관리하는 데 중요합니다.

|데이터베이스 수량|정의|설명|
|---|---|---|
|**사용된 데이터 공간**|8KB 페이지에 데이터베이스 데이터를 저장하는 데 사용된 공간의 크기입니다.|일반적으로 사용된 공간은 삽입(삭제) 시 증가(감소)합니다. 작업 및 조각화와 관련된 데이터의 크기 및 패턴에 따라 삽입 또는 삭제 시 사용된 공간이 변경되지 않는 경우가 있습니다. 예를 들어 모든 데이터 페이지에서 하나의 행을 삭제한다고 해서 사용된 공간이 반드시 감소하지는 않습니다.|
|**할당된 데이터 공간**|데이터베이스 데이터 저장에 사용할 수 있는 형식화된 파일 공간의 크기입니다.|할당된 공간의 크기는 자동으로 증가하지만 삭제 후에는 감소하지 않습니다. 이 동작은 공간을 다시 형식화할 필요가 없기 때문에 향후 삽입이 더 빨라질 수 있습니다.|
|**할당되었지만 사용되지 않은 데이터 공간**|할당된 데이터 공간의 크기와 사용된 데이터 공간 간의 차이입니다.|이 수량은 데이터베이스 데이터 파일을 축소하면 회수할 수 있는 사용 가능한 공간의 최대 크기를 나타냅니다.|
|**데이터 최대 크기**|데이터베이스 데이터 저장에 사용할 수 있는 최대 공간의 크기입니다.|할당된 데이터 공간 크기는 데이터 최대 크기를 초과할 수 없습니다.|
||||

다음 다이어그램에서는 데이터베이스에 대한 여러 저장소 공간 유형 간의 관계를 보여 줍니다.

![저장소 공간 유형 및 관계](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>스토리지 공간 정보에 대해 단일 데이터베이스 쿼리

다음 쿼리를 사용하여 단일 데이터베이스의 스토리지 공간 수량을 확인할 수 있습니다.  

### <a name="database-data-space-used"></a>사용된 데이터베이스 데이터 공간

다음 쿼리를 수정하여 사용된 데이터베이스 데이터 공간의 크기를 반환합니다.  쿼리 결과의 단위는 MB입니다.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>할당된 데이터베이스 데이터 공간 및 사용되지 않은 공간

다음 쿼리를 사용하여 할당된 데이터베이스 데이터 공간 크기 및 할당된 사용되지 않은 공간 크기를 반환합니다.  쿼리 결과의 단위는 MB입니다.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>데이터베이스 데이터 최대 크기

다음 쿼리를 수정하여 데이터베이스 데이터 최대 크기를 반환합니다.  쿼리 결과의 단위는 바이트입니다.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>탄력적 풀을 위한 저장소 공간 유형 이해

다음 저장소 공간 수량을 이해하는 것은 탄력적 풀의 파일 공간을 관리하는 데 중요합니다.

|탄력적 풀 수량|정의|설명|
|---|---|---|
|**사용된 데이터 공간**|탄력적 풀에서 모든 데이터베이스에 사용되는 데이터 공간의 합계입니다.||
|**할당된 데이터 공간**|탄력적 풀에서 모든 데이터베이스에 할당된 데이터 공간의 합계입니다.||
|**할당되었지만 사용되지 않은 데이터 공간**|탄력적 풀에서 모든 데이터베이스에 할당된 데이터 공간의 크기와 사용된 데이터 공간 간의 차이입니다.|이 수량은 데이터베이스 데이터 파일을 축소하면 회수할 수 있는 탄력적 풀에 대해 할당된 공간의 최대 크기를 나타냅니다.|
|**데이터 최대 크기**|해당 데이터베이스 모두에 대해 탄력적 풀에서 사용할 수 있는 최대 데이터 공간의 크기입니다.|탄력적 풀에 할당된 공간은 탄력적 풀 최대 크기를 초과할 수 없습니다.  이 상태가 발생하면 데이터베이스 데이터 파일을 축소하여 사용되지 않은 할당된 공간을 회수할 수 있습니다.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>저장소 공간 정보를 탄력적 풀에 쿼리

탄력적 풀에 대한 저장소 공간 수량을 확인하려면 다음 쿼리를 사용할 수 있습니다.  

### <a name="elastic-pool-data-space-used"></a>사용되는 탄력적 풀 데이터 공간

다음 쿼리를 수정하여 사용된 탄력적 풀 데이터 공간의 크기를 반환합니다.  쿼리 결과의 단위는 MB입니다.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>할당된 탄력적 풀 데이터 공간 및 사용되지 않은 공간

다음 PowerShell 스크립트를 수정하여 탄력적 풀의 각 데이터베이스에 대해 할당된 공간 및 사용되지 않은 할당된 공간이 나열된 테이블을 반환합니다. 테이블에는 데이터베이스가 사용되지 않은 할당된 공간이 가장 큰 것에서 사용되지 않은 할당된 공간이 가장 작은 순서로 정렬됩니다.  쿼리 결과의 단위는 MB입니다.  

풀의 각 데이터베이스에 할당된 공간을 확인하는 쿼리 결과를 함께 추가하여 탄력적 풀에 대한 할당된 총 공간을 확인할 수 있습니다. 할당된 탄력적 풀 공간은 탄력적 풀 최대 크기를 초과할 수 없습니다.  

PowerShell 스크립트를 사용하려면 SQL Server PowerShell 모듈이 필요합니다. 설치하려면 [PowerShell 모듈 다운로드](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module)를 참조하세요.

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

다음 스크린샷은 스크립트 출력의 예입니다.

![탄력적 풀에 할당된 공간 및 사용되지 않는 할당된 공간 예](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>탄력적 풀 데이터 최대 크기

다음 T-SQL 쿼리를 수정하여 탄력적 풀 데이터 최대 크기를 반환합니다.  쿼리 결과의 단위는 MB입니다.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>사용되지 않는 할당된 공간 회수

> [!NOTE]
> 이 명령은 데이터베이스가 실행하는 동안 성능에 영향을 줄 수 있으므로, 가능하면 사용량이 낮은 기간 동안 실행해야 합니다.

### <a name="dbcc-shrink"></a>DBCC 축소

사용되지 않은 할당된 공간을 회수하기 위해 데이터베이스를 확인하고 나면, 다음 명령에서 데이터베이스 이름을 수정하여 각 데이터베이스에 대한 데이터 파일을 축소합니다.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

이 명령은 데이터베이스가 실행하는 동안 성능에 영향을 줄 수 있으므로, 가능하면 사용량이 낮은 기간 동안 실행해야 합니다.  

이 명령에 대한 자세한 내용은 [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)를 참조하세요. 

### <a name="auto-shrink"></a>자동 축소

또는 데이터베이스에 대한 자동 축소를 사용하도록 설정할 수 있습니다.  자동 축소는 파일 관리의 복잡도를 줄여 주며 데이터베이스 성능에 대한 영향이 `SHRINKDATABASE` 또는 `SHRINKFILE`보다 더 적습니다.  자동 축소는 많은 데이터베이스에서 탄력적 풀을 관리하는 데 특히 유용할 수 있습니다.  그러나 자동 축소의 파일 공간 회수 효과는 `SHRINKDATABASE` 및 `SHRINKFILE`보다 떨어질 수 있습니다.
자동 축소를 사용하도록 설정하려면 다음 명령에서 데이터베이스의 이름을 수정합니다.


```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

이 명령에 대한 자세한 내용은 [DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) 옵션을 참조하세요. 

### <a name="rebuild-indexes"></a>인덱스 다시 작성

데이터베이스 데이터 파일이 축소된 후에는 인덱스가 조각화되어 성능 최적화 효과가 상실될 수 있습니다. 성능 저하가 발생하는 경우 데이터베이스 인덱스를 다시 작성하는 것을 고려합니다. 조각화 및 인덱스 다시 작성에 대한 자세한 내용은 [인덱스 재구성 및 다시 작성](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 최대 데이터베이스 크기에 대한 정보는 다음을 참조하세요.
  - [단일 데이터베이스에 대한 Azure SQL Database vCore 기반 구매 모델 한도](sql-database-vcore-resource-limits-single-databases.md)
  - [DTU를 기반 구매 모델을 사용한 단일 데이터베이스에 대한 리소스 제한](sql-database-dtu-resource-limits-single-databases.md)
  - [탄력적 풀에 대한 Azure SQL Database vCore 기반 구매 모델 제한](sql-database-vcore-resource-limits-elastic-pools.md)
  - [DTU를 기반 구매 모델을 사용한 탄력적 풀에 대한 리소스 제한](sql-database-dtu-resource-limits-elastic-pools.md)
- `SHRINKDATABASE` 명령에 대한 자세한 내용은 [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)를 참조하세요. 
- 조각화 및 인덱스 다시 작성에 대한 자세한 내용은 [인덱스 재구성 및 다시 작성](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)을 참조하세요.
