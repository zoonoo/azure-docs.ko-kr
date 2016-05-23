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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell을 사용하여 삭제된 Azure SQL 데이터베이스 복원


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

이 문서에서는 삭제된 Azure SQL 데이터베이스를 복원하는 방법을 보여 줍니다.

데이터베이스가 삭제된 경우, Azure SQL 데이터베이스를 사용하면 삭제된 데이터베이스를 지정된 삭제 시점으로 복원할 수 있습니다. Azure SQL 데이터베이스는 데이터베이스 보존 기간 동안 삭제된 데이터베이스의 백업을 저장합니다.

삭제된 데이터베이스의 보존 기간은 해당 데이터베이스가 존재했던 서비스 계층 또는 데이터베이스의 존재 일 수 중 더 작은 일 수에 의해 결정됩니다. 데이터베이스 보존에 대해 자세히 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.


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
- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)



## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->