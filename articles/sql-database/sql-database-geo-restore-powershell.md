<properties 
    pageTitle="지역 중복 백업에서 Azure SQL 데이터베이스 복원(PowerShell) | Microsoft Azure" 
    description="지역 중복 백업에서 새 서버로 Azure SQL 데이터베이스를 복원합니다." 
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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# PowerShell을 사용하여 지역 중복 백업에서 Azure SQL 데이터베이스 복원


> [AZURE.SELECTOR]
- [개요](sql-database-geo-restore.md)
- [Azure 포털](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

이 문서에서는 PowerShell을 사용하여 지역 중복을 사용하는 새 서버에 데이터베이스를 복원하는 방법을 보여 줍니다.

[AZURE.INCLUDE [PowerShell 세션 시작](../../includes/sql-database-powershell.md)]

## 데이터베이스를 독립 실행형 데이터베이스로 지역 복원

1. [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) cmdlet을 사용하여 복원하려는 데이터베이스의 지역 중복 백업을 가져옵니다.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) cmdlet을 사용하여 지역 중복 백업에서 복원을 시작합니다.
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## 데이터베이스를 탄력적 데이터베이스 풀로 지역 복원

1. [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) cmdlet을 사용하여 복원하려는 데이터베이스의 지역 중복 백업을 가져옵니다.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) cmdlet을 사용하여 지역 중복 백업에서 복원을 시작합니다. 데이터베이스를 복원하려는 풀 이름을 지정합니다.
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## 다음 단계

- 지역 중복 백업에서 Azure 포털을 사용하여 Azure SQL 데이터베이스를 복원하는 방법에 대한 자세한 단계는 [Azure 포털을 사용하여 지리적 복원](sql-database-geo-restore-portal.md)을 참조하세요.
- 지역 중복 백업에서 Azure SQL 데이터베이스를 복원하는 것과 관련된 자세한 내용은 [PowerShell을 사용하여 지리적 복원](sql-database-geo-restore.md)을 참조하세요.
- 가동 중단에서 복구하는 방법에 대한 자세한 내용은 [가동 중단에서 복구](sql-database-disaster-recovery.md)를 참조하세요.


## 추가 리소스

- [비즈니스 연속성 시나리오](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->