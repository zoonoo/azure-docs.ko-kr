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
    ms.workload="data-management" 
    ms.date="06/09/2016"
    ms.author="sstein"/>

# PowerShell을 사용하여 이전 시점으로 Azure SQL 데이터베이스 복원

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

- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)
- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [Azure 포털을 사용하여 지정 시간 복원](sql-database-point-in-time-restore-portal.md)
- [REST API를 사용하여 지정 시간 복원](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)

## 추가 리소스

- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->