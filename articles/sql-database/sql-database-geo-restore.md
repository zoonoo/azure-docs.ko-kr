---
title: "지역 중복 백업에서 Azure SQL Database 복원 | Microsoft Docs"
description: "Azure Portal 또는 PowerShell을 사용하여 지역 중복 백업에서 새 서버로 Azure SQL Database를 복원합니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6104936089ac9a1a6bbc08a345105e5fa4ae8be7
ms.openlocfilehash: 0ab7090ba7b37b3447da95a3d577cfe82ea8003e


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>지역 중복 백업에서 Azure SQL Database 복원

이 문서에서는 지역 복원을 사용하여 새 서버에 데이터베이스를 복원하는 방법을 보여 줍니다. Azure Portal 또는 PowerShell을 사용하여 할 수 있습니다.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Azure Portal을 사용하여 지역 중복 백업에서 Azure SQL Database 복원

Azure Portal에서 데이터베이스를 지역 복원하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 화면 왼쪽에서 **+새로 만들기** > **데이터베이스** > **SQL Database**를 선택합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 원본으로 **백업**을 선택한 다음 복원할 백업을 선택합니다. 데이터베이스 이름, 데이터베이스를 복원하려는 서버를 지정한 다음 **만들기**를 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-geo-restore-portal/geo-restore.png)

4. 페이지 오른쪽 위에 있는 알림 아이콘을 클릭하여 복원 작업의 상태를 모니터링합니다.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>PowerShell을 사용하여 지역 중복 백업에서 Azure SQL 데이터베이스 복원

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>데이터베이스를 독립 실행형 데이터베이스로 지역 복원
1. [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx) cmdlet을 사용하여 복원하려는 데이터베이스의 지역 중복 백업을 가져옵니다.
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet을 사용하여 지역 중복 백업에서 복원을 시작합니다.
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>데이터베이스를 탄력적 풀로 지역 복원
1. [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx) cmdlet을 사용하여 복원하려는 데이터베이스의 지역 중복 백업을 가져옵니다.
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet을 사용하여 지역 중복 백업에서 복원을 시작합니다. 데이터베이스를 복원하려는 풀 이름을 지정합니다.
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>다음 단계
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.  
* 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)를 참조하세요.




<!--HONumber=Dec16_HO3-->


