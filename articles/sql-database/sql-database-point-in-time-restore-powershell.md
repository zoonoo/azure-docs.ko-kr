<properties 
    pageTitle="이전 시점으로 Azure SQL 데이터베이스 복원(PowerShell) | Microsoft Azure" 
    description="이전 시점으로 Azure SQL 데이터베이스를 복원합니다." 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="NA" 
    ms.date="07/17/2016"
    ms.author="sstein"/>

# PowerShell을 사용하여 이전 시점으로 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [개요](sql-database-recovery-using-backups.md)
- [지정 시간 복원: Azure 포털](sql-database-point-in-time-restore-portal.md)

이 문서에서는 PowerShell을 사용하여 [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)에서 이전 시점으로 데이터베이스를 복원하는 방법을 보여 줍니다.

[AZURE.INCLUDE [PowerShell 세션 시작](../../includes/sql-database-powershell.md)]

## 데이터베이스를 지정 시간의 독립 실행형 데이터베이스로 복원합니다.

1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) cmdlet을 사용하여 복원하려는 데이터베이스를 선택합니다.

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) cmdlet을 사용하여 데이터베이스를 지정 시간으로 복원합니다.
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## 데이터베이스를 지정 시간의 탄력적 데이터베이스 풀에 복원합니다.
   
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) cmdlet을 사용하여 복원하려는 데이터베이스를 선택합니다.

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) cmdlet을 사용하여 데이터베이스를 지정 시간으로 복원합니다.
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"


## 다음 단계

- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
- 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)를 참조하세요.

<!---HONumber=AcomDC_0727_2016-->