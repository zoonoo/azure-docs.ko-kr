<properties
	pageTitle="삭제된 Azure SQL 데이터베이스 복원(PowerShell) | Microsoft Azure"
	description="삭제된 Azure SQL 데이터베이스를 복원합니다(PowerShell)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell을 사용하여 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [개요](sql-database-restore-deleted-database.md)
- [Azure 포털](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [PowerShell 세션 시작](../../includes/sql-database-powershell.md)]


## 삭제된 데이터베이스를 독립 실행형 데이터베이스로 복원

1. [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) cmdlet을 사용하여 복원하려는 삭제된 데이터베이스 백업을 가져옵니다.

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) cmdlet을 사용하여 삭제된 데이터베이스 백업에서 복원을 시작합니다.
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## 삭제된 데이터베이스를 탄력적 데이터베이스 풀로 복원

1. [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) cmdlet을 사용하여 복원하려는 삭제된 데이터베이스 백업을 가져옵니다.

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) cmdlet을 사용하여 삭제된 데이터베이스 백업에서 복원을 시작합니다.
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## 다음 단계

- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)
- [Azure 포털을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-portal.md)
- [REST API를 사용하여 삭제된 데이터베이스 복원](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)

## 추가 리소스

- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->