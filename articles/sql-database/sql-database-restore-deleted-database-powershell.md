---
title: "삭제된 Azure SQL Database 복원(PowerShell) | Microsoft Docs"
description: "삭제된 Azure SQL 데이터베이스를 복원합니다(PowerShell)."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: ce7f0f3f-47a6-42af-b8a9-4a34bbbd8966
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 19a83f8206f589d1fb2bf76c5ad0cca04f281c3f


---
# <a name="restore-a-deleted-azure-sql-database-by-using-powershell"></a>PowerShell을 사용하여 삭제된 Azure SQL 데이터베이스 복원

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="get-a-list-of-deleted-databases"></a>삭제된 데이터베이스 목록 가져오기
```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"

$DeletedDatabases = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName
```

## <a name="restore-your-deleted-database-into-a-single-database"></a>삭제된 데이터베이스를 단일 데이터베이스로 복원
[Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387\(v=azure.300/\).aspx) cmdlet을 사용하여 복원하려는 삭제된 데이터베이스 백업을 가져옵니다. 그런 다음 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300/\).aspx) cmdlet을 사용하여 삭제된 데이터베이스 백업에서 복원을 시작합니다.

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase -FromDeletedDatabaseBackup -DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"
```


## <a name="restore-your-deleted-database-into-an-elastic-pool"></a>삭제된 데이터베이스를 탄력적 풀로 복원
[Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387\(v=azure.300/\).aspx) cmdlet을 사용하여 복원하려는 삭제된 데이터베이스 백업을 가져옵니다. 그런 다음 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300/\).aspx) cmdlet을 사용하여 삭제된 데이터베이스 백업에서 복원을 시작합니다.

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase -FromDeletedDatabaseBackup -DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $DeletedDatabase.ResourceID -ElasticPoolName "elasticpool01"
```


## <a name="next-steps"></a>다음 단계
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)  
* 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)




<!--HONumber=Feb17_HO3-->


